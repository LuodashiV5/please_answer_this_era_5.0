> 搞嵌入式的朋友，多少都跟回调函数打过交道。项目小的时候，一个函数指针搞定一切；但项目一大，回调满天飞，改一个地方牵一发动全身。今天咱们换个思路——把QT里的信号与槽那套东西，用纯C给它安排上。

## 从一个真实的痛点说起

先看一个嵌入式开发中最常见的场景：按键检测。
```c
// 按键模块
void key_scan(void)
{
    if (is_key_pressed()) {
        // 直接调用LED模块的函数
        led_toggle();
        // 后来又要加蜂鸣器...
        buzzer_beep();
        // 再后来又要加显示刷新...
        display_refresh();
        // 每加一个功能，就要改这里...
    }
}
```

看出问题了吗？按键模块居然要知道LED、蜂鸣器、显示屏的存在。每加一个新功能，都得回来改按键模块的代码。这就是**强耦合**——模块之间互相纠缠，改一处就得动好几处。

如果我们有一套信号槽机制，代码就可以变成这样：

```c
// 按键模块：只管发信号，不关心谁在听
void key_scan( void )
{
    if ( is_key_pressed() )
    {
        signal_emit( &key_pressed_signal ); // 发出信号，完事儿
    }
}

// 别的模块各自注册，各管各的
signal_connect( &key_pressed_signal, led_toggle );
signal_connect( &key_pressed_signal, buzzer_beep );
signal_connect( &key_pressed_signal, display_refresh );
```
是不是清爽多了？按键模块完全不需要知道谁在监听它。想加新功能？在新模块里 `connect` 一下就行，**按键模块一行代码都不用动**。

## 信号槽到底是个啥？

QT的信号槽机制说白了就三个角色：

- • **信号（Signal）**：某件事发生了，对外广播一下
    
- • **槽（Slot）**：收到广播后要执行的动作
    
- • **连接（Connect）**：把信号和槽绑在一起
    

整个流程就像订阅公众号：你关注了某个号（connect），号主发了文章（emit signal），你就能收到推送（slot被调用）。不想看了就取关（disconnect），号主根本不知道也不在乎。

用一张图来理解：

![图片](https://mmbiz.qlogo.cn/sz_mmbiz_png/TicLfDnraHxfIicDNRTDFUDG2hE4QPMB05q39HYJicEAX0iaCr9uuUWXA4BYywBHZdKhUfoO7mrxtia2nT1KFtbfS18ZA9gg8ULQV9rmTicLlLHYI/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&retryload=2&watermark=1#imgIndex=0)

关键在于：**发送者和接收者之间完全解耦**。发信号的人不需要 `#include` 接收者的头文件，接收者也不需要知道信号从哪来。

那要用纯C实现这套东西，我们需要解决几个核心问题：

1. 1. 信号怎么定义？—— 用**结构体**
    
2. 2. 槽函数怎么存？—— 用**函数指针数组**
    
3. 3. 连接关系怎么管？—— 用**注册/注销接口**
    
4. 4. 信号怎么触发？—— 用**遍历调用**
    

## 动手实现：从数据结构开始

### 3.1 定义槽函数类型和信号结构体

先想清楚数据结构，代码就成功了一半。

```c
#include <stdint.h>  
#include <stdbool.h>  
#include <string.h>  
  
/* 每个信号最多连接的槽数量，嵌入式环境按需调整 */  
#define MAX_SLOTS  8  
  
/* 槽函数类型：接受一个通用参数指针 */  
typedef void (*slot_func_t)(void *arg);  
  
/* 信号结构体 */  
typedef struct {   
	 const char   *name;                 /* 信号名称，方便调试 */    
	 slot_func_t   slots[MAX_SLOTS];     /* 槽函数数组 */    
	 uint8_t       slot_count;           /* 当前已连接的槽数量 */  
} signal_t;  
  
/* 信号初始化宏，用起来更方便 */  
#define SIGNAL_INIT(sig_name) { .name = #sig_name, .slot_count = 0 }
```

这里有几个设计考量值得说一说：

- • **静态数组而非链表**：嵌入式环境下内存宝贵，`malloc` 能不用就不用。固定大小的数组在编译期就确定了内存占用，不会产生碎片。
    
- • **`void *arg` 参数**：让槽函数能接收任意类型的数据。按键信号可以传键值，温度信号可以传温度值，一套接口搞定所有场景。
    
- • **`SIGNAL_INIT` 宏**：`#sig_name` 会把宏参数转成字符串，调试时打印信号名称很方便。
    

整个数据结构的内存布局如下：

![图片](https://mmbiz.qlogo.cn/mmbiz_png/TicLfDnraHxf12ricP1rZY3Nxu5rjrnTMwK9JorXUrVJiaFR6gsCvBvvsXqZXh5ve09T9icPZicD8dScAo3VN0NJL3KdsrDa6lwWXg4jQKdO1WYU/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&retryload=2&watermark=1#imgIndex=1)

37个字节就能管理一个信号的所有连接关系，对于STM32这类MCU来说完全不是问题。

### 3.2 三个核心API

数据结构有了，接下来实现三个最关键的函数。

**连接：signal_connect**

```c
/**
 * @brief  将槽函数连接到信号
 * @param  sig   信号指针
 * @param  slot  槽函数指针
 * @retval 0-成功, -1-失败
 */
int signal_connect( signal_t *sig,  slot_func_t slot )
{
    if ( sig ==  NULL  || slot ==  NULL )
    {
        return  -1;
    }

    /* 防止重复连接同一个槽 */
    for ( uint8_t i =  0; i < sig->slot_count; i++ )
    {
        if ( sig->slots[i] == slot )
        {
            return 0;/* 已经连过了，直接返回 */
        }
    }

    /* 槽位满了 */
    if ( sig->slot_count >= MAX_SLOTS )
    {
        return  -1;
    }

    sig->slots[sig->slot_count] = slot;
    sig->slot_count++;
    return 0;
}
```

这里有个细节：**重复连接检查**。如果不做这个判断，同一个槽函数可能被调用多次，在嵌入式场景里这往往不是你想要的行为。

**断开连接：signal_disconnect**

```c
/**
 * @brief  断开槽函数与信号的连接
 * @param  sig   信号指针
 * @param  slot  要断开的槽函数
 * @retval 0-成功, -1-未找到
 */
int signal_disconnect(signal_t *sig, slot_func_t slot)
{
    if (sig == NULL || slot == NULL) {
        return -1;
    }

    for (uint8_t i = 0; i < sig->slot_count; i++) {
        if (sig->slots[i] == slot) {
            /* 用最后一个元素填补空位，避免数组移动 */
            sig->slot_count--;
            sig->slots[i] = sig->slots[sig->slot_count];
            sig->slots[sig->slot_count] = NULL;
            return 0;
        }
    }
    return -1;  /* 没找到这个槽 */
}
```

注意这里的**删除策略**：不是把后面的元素往前挪（O(n)），而是直接把最后一个元素搬过来填坑（O(1)）。在中断环境里，效率很重要。

**触发信号：signal_emit**

```c
/**
 * @brief  触发信号，依次调用所有已连接的槽函数
 * @param  sig  信号指针
 * @param  arg  传递给槽函数的参数
 */
void signal_emit(signal_t *sig, void *arg)
{
    if (sig == NULL) {
        return;
    }

    for (uint8_t i = 0; i < sig->slot_count; i++) {
        if (sig->slots[i] != NULL) {
            sig->slots[i](arg);  /* 逐个调用槽函数 */
        }
    }
}
```

三个函数就这么简单。emit的时候遍历数组，逐个调用——这就是信号槽的全部秘密。

用一张调用时序图来总结一下：

![图片](https://mmbiz.qlogo.cn/mmbiz_png/TicLfDnraHxev3UQX1VTxKWta7fxfARdfY5wJkIeVPYokswVf0icXeicpXxnJxG8dtLpJic4Kw4VNI6fpwiazxZ2Hc6Zed5dgCwJuQtpo4aNqibqU/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&retryload=2&watermark=1#imgIndex=2)

## 实战：温度报警系统

光讲理论不过瘾，来个完整的例子。假设我们在做一个温度监控系统：温度传感器采集到数据后，需要通知LCD显示、超限报警、数据记录三个模块。

```c
/* ========== 信号定义 ========== */
/* 温度模块对外只暴露这一个信号 */
signal_t temp_updated = SIGNAL_INIT(temp_updated);

/* ========== 槽函数实现（各模块各自实现） ========== */

/* LCD显示模块 */
void lcd_show_temp(void *arg)
{
    float temp = *(float *)arg;
    printf("[LCD] 当前温度: %.1f°C\n", temp);
}

/* 报警模块 */
void alarm_check(void *arg)
{
    float temp = *(float *)arg;
    if (temp > 85.0f) {
        printf("[ALARM] 温度过高! %.1f°C 超过阈值!\n", temp);
        /* 这里可以驱动蜂鸣器、发送告警等 */
    }
}

/* 数据记录模块 */
void logger_record(void *arg)
{
    float temp = *(float *)arg;
    printf("[LOG] 记录温度: %.1f°C\n", temp);
    /* 实际项目中写入Flash或SD卡 */
}

/* ========== 系统初始化 ========== */
void system_init(void)
{
    /* 各模块自行注册关心的信号 */
    signal_connect(&temp_updated, lcd_show_temp);
    signal_connect(&temp_updated, alarm_check);
    signal_connect(&temp_updated, logger_record);
}

/* ========== 温度采集任务 ========== */
void temp_task(void)
{
    float current_temp = read_sensor();  /* 读取传感器 */
    signal_emit(&temp_updated, &current_temp);  /* 发出信号，完事 */
}
```

看到没有？温度采集模块(`temp_task`)只做了两件事：读温度、发信号。它压根不知道有LCD、报警器、日志这些东西的存在。

如果哪天产品经理说"加个蓝牙上传功能"，你只需要：
```c
/* 新增蓝牙模块，一行搞定 */   
signal_connect(&temp_updated, ble_upload_temp);`
```


温度采集模块一个字都不用改。**这就是解耦的威力**。

## 进阶：带参数类型检查的安全版本

上面的实现有个隐患：`void *` 类型不安全，传错参数类型编译器不会报错。我们可以用宏做一层包装，让用起来更安全：

```c
/* 定义带类型信息的信号 */
#define DEFINE_SIGNAL(name, param_type)          \
    typedef void (*name##_slot_t)(param_type);   \
    typedef struct {                             \
        const char      *sig_name;               \
        name##_slot_t    slots[MAX_SLOTS];       \
        uint8_t          slot_count;             \
    } name##_signal_t

/* 类型安全的emit */
#define TYPED_EMIT(sig, arg)                     \
    do {                                         \
        for (uint8_t i = 0; i < (sig)->slot_count; i++) { \
            if ((sig)->slots[i]) (sig)->slots[i](arg);    \
        }                                        \
    } while(0)

/* 使用示例 */
DEFINE_SIGNAL(temperature, float);   /* 定义温度信号类型 */
temperature_signal_t temp_sig = { .sig_name = "temperature" };

/* 槽函数签名必须匹配，传错类型编译器直接报错 */
void on_temp_change(float temp) {    /* 参数类型是float，不是void* */
    printf("温度: %.1f\n", temp);
}
```

这样做的好处是：**编译期就能发现类型不匹配的问题**，而不是在运行时出一些诡异的bug。

## 在嵌入式环境中的几点注意

把这套机制搬进实际项目之前，有几个坑得提前踩明白：

**1. 中断安全**

如果 `signal_emit` 在中断中调用，而 `connect/disconnect` 在主循环中调用，就可能出现竞态条件。最简单的做法是在 `connect/disconnect` 时关中断：

```c
int signal_connect_safe(signal_t *sig, slot_func_t slot)
{
    __disable_irq();            /* 关中断 */
    int ret = signal_connect(sig, slot);
    __enable_irq();             /* 开中断 */
    return ret;
}
```

**2. 槽函数执行时间**

所有槽函数是**同步顺序执行**的。如果某个槽函数耗时太长，会阻塞后面的槽函数。在中断上下文里尤其要注意——槽函数应该尽量短小精悍，耗时操作留给主循环去做。

**3. 递归触发**

如果槽函数A里面又 `emit` 了另一个信号，而那个信号的某个槽函数又回来 `emit` 了原来的信号……恭喜你，栈溢出了。在设计时要注意避免信号的循环触发：

`signal_A → slot_X → signal_B → slot_Y → signal_A  ← 死循环！`

**4. MAX_SLOTS的选择**

8个槽位对绝大多数嵌入式场景够用了。如果你的某个信号确实需要连接更多槽，可以单独定义一个更大的版本，没必要全局改大浪费所有信号的内存。

## 回顾：我们做了什么

用不到100行C代码，我们实现了一套完整的信号槽机制：

|功能|函数|复杂度|
|---|---|---|
|连接信号与槽|`signal_connect()`|O(n) 查重|
|断开连接|`signal_disconnect()`|O(n) 查找|
|触发信号|`signal_emit()`|O(n) 遍历|

总结一下这套机制的核心价值：

![图片](https://mmbiz.qlogo.cn/mmbiz_png/TicLfDnraHxePqVicyHmHLguEZcBz1daKS1REpXXEicTE2aZpUJRCCSXPQUfJnceibib5VC8nicUKE9WI8ptxic568rKfdHoUtaYwBaPEDeMb8EWVo/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&retryload=2&watermark=1#imgIndex=3)

## 写在最后

其实你发现没有，今天实现的这个信号槽机制，本质上就是观察者模式（Observer Pattern）的C语言版本。而QT的信号槽之所以好用，也是因为它在语言层面把这个设计模式给内建了。

在嵌入式C的世界里，像这样把经典的设计模式思想融入代码，能让你的架构清晰度和可维护性提升一大截。信号槽只是冰山一角，状态模式、策略模式、命令模式……这些在嵌入式场景下都有非常实用的落地方式。

如果你对"**嵌入式C语言设计模式**"这个方向感兴趣，可以看看我整理的设计模式合集，每一篇都有完整代码和实战案例：

👉 [嵌入式C语言设计模式合集](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzYzMzA3MTkxMA==&action=getalbum&album_id=4315683061955772430#wechat_redirect)

代码能跑只是及格线，写出能"生长"的架构才是真本事。共勉。