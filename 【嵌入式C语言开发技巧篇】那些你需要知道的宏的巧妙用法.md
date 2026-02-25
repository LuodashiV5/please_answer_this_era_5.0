

接触嵌入式开发久了，相信大家都遇到过这种情况：宏定义看似简单，结果一展开就坑你一把。有人说宏就是简单的“文本替换”，但在 Linux 内核和 GCC 头文件里，它早就被玩出花了。下面讲讲几种实用的宏的使用方式；

------

## 1. `do{...}while(0)` —— 多语句宏的打包技巧

如果定义的宏有多条语句时，如果没有使用代码块包含，容易踩坑，例如下面代码所示：

- 错误写法：

```c
#define INIT_DEVICE(dev) \ 
dev->status = 0; \ 
dev->count = 0; \ 
dev->flag = 1; 
if (need_init) INIT_DEVICE(dev); 
else do_something_else();
 
//展开后
if (need_init) dev->status = 0; // 只有第一条语句属于if的代码块，后续语句已经不在 if 里了 
dev->count = 0;
dev->flag = 1; 
else do_something_else(); // 编译报错
```

使用 `do{...}while(0)` 包起来。这样无论怎么写 `if-else`，宏都能当成一条语句，不会乱跑。这是内核里常见的写法，属于“必备技能”。

- 正确写法：

```c
#define INIT_DEVICE(dev) \
    do { \
        dev->status = 0; \
        dev->count = 0; \
        dev->flag = 1; \
    } while(0)
```

## 2. `#` 和 `##` —— 字符串化 & 拼接

### 字符串化`#`：

调试日志里，最常用的就是 `#`。它能把变量名直接变成字符串，写个 `DBG_VAR(var)`，就能自动打印“变量名 = 值”，再也不用手动敲变量名。

```c
#define DBG_VAR(var) \
    printf("[DEBUG] %s = %d\n", #var, (int)(var))
 
int temperature = 36;
DBG_VAR(temperature);// 输出: [DEBUG] temperature = 36
```

### 拼接符`##`

而 `##` 拼接符更是神器。例如写 GPIO 驱动时，用一个宏批量生成形式类似的函数，省下了无数重复代码。

例如下面代码示例的IO口设置函数：

```c
#include <stdio.h>

#define GPIO_PIN_0 0
#define GPIO_PIN_1 1
#define GPIO_PIN_2 2
#define GPIOA "GPIOA"
#define GPIOB "GPIOB"
#define GPIOC "GPIOC"

#define IO_SET_HIGH(name,pin) \
    printf("set %s PIN%d to high\n",GPIO##name,GPIO_PIN_##pin);
#define IO_SET_LOW(name,pin) \
    printf("set %s PIN%d to low\n",GPIO##name,GPIO_PIN_##pin);

int main()
{
    IO_SET_HIGH(A,0);
    IO_SET_HIGH(B,1);
    IO_SET_HIGH(C,2);
    IO_SET_LOW(A,0);
    IO_SET_LOW(B,1);
    IO_SET_LOW(C,2);
    return 0;
}
```

输出：
set GPIOA PIN0 to high
set GPIOB PIN1 to high
set GPIOC PIN2 to high
set GPIOA PIN0 to low
set GPIOB PIN1 to low
set GPIOC PIN2 to low



## 3. 可变参数宏 —— 打造统一简洁的调试输出形式

嵌入式日志系统里，通常需要打印的参数不确定，使用`__VA_ARGS__`可变参数宏是标配，但是这个宏有一个坑，展开后会自动添加`,`，结果不带参数日志直接编译不过。需要使用GCC的扩展特性`##`：

```c
#define LOG(fmt, ...) \
    printf("[LOG] " fmt "\n", ##__VA_ARGS__)
 
LOG("system started");        // 输出：[LOG] system started
LOG("temp = %d", 36);         // 输出：[LOG] temp = 36
LOG("x=%d, y=%d", 10, 20);    // 输出：[LOG] x=10, y=20
```

- `##__VA_ARGS__` 的作用
        - `##` 是 GCC 的扩展特性，用来在参数为空时自动去掉前面的逗号，避免在没有参数打印的时候展开后添加逗号导致编译报错；

------

### 升级版：添加行号和文件名和支持设置不同的日志级别

```c
#include <stdio.h>
 
// 定义日志级别常量
#define LOG_LEVEL_DEBUG 0
#define LOG_LEVEL_INFO  1
#define LOG_LEVEL_WARN  2
#define LOG_LEVEL_ERROR 3

// 在这里设置编译时日志等级
#define LOG_LEVEL LOG_LEVEL_INFO

// 不同级别的日志宏
#if LOG_LEVEL <= LOG_LEVEL_DEBUG
#define LOGD(fmt, ...) \
    printf("[DEBUG][%s:%d] " fmt "\n", __FILE__, __LINE__, ##__VA_ARGS__)
#else
#define LOGD(fmt, ...)  // 空宏，编译时直接去掉
#endif

#if LOG_LEVEL <= LOG_LEVEL_INFO
#define LOGI(fmt, ...) \
    printf("[INFO][%s:%d] " fmt "\n", __FILE__, __LINE__, ##__VA_ARGS__)
#else
#define LOGI(fmt, ...)  // 空宏
#endif

#if LOG_LEVEL <= LOG_LEVEL_WARN
#define LOGW(fmt, ...) \
    printf("[WARN][%s:%d] " fmt "\n", __FILE__, __LINE__, ##__VA_ARGS__)
#else
#define LOGW(fmt, ...)  // 空宏
#endif

#if LOG_LEVEL <= LOG_LEVEL_ERROR
#define LOGE(fmt, ...) \
    printf("[ERROR][%s:%d] " fmt "\n", __FILE__, __LINE__, ##__VA_ARGS__)
#else
#define LOGE(fmt, ...)  // 空宏
#endif

int main() {
    LOGI("System started");
    LOGD("Debug info: x=%d", 42);
    LOGW("Warning: low battery");
    LOGE("Error: read failed, err=%d", -1);
    return 0;
}
```

`__FILE__, __LINE__`:为预定义的宏，预编译阶段会被替换为对应的文件名和所在行号；



## 4. `typeof` + `container_of` —— LINUX内核里的镇山之宝

`typeof`： 保证宏语句的“类型安全”，避免引起副作用；
`container_of`： 在Linux内核中是链表操作的核心,效率神器；能从成员指针反推出整个结构体。零运行时开销，纯编译期计算。

### 使用`typeof`保证类型安全和避免副作用

来看一个经典的宏使用方式：返回两个值中的较大值；

```c
#define MAX(a, b) ({ \
    typeof(a) _a = (a); \
    typeof(b) _b = (b); \
    _a > _b ? _a : _b; \
})
```

这样写的好处是：

- 类型安全（通过`typeof`能自动匹配参数类型）。

- 避免副作用（参数只求值一次），在宏内通过临时变量接收参数值，避免原始参数更改；

- 使用方便（像函数一样返回结果）。

### `container_of`

```c
#define offsetof(TYPE, MEMBER) ((size_t)&((TYPE *)0)->MEMBER)
#define container_of(ptr, type, member) ({ \
    const typeof(((type *)0)->member) *__mptr = (ptr); \
    (type *)((char *)__mptr - offsetof(type, member)); \
})
```

上述宏有两部分组成：

offsetof 的原理说明：

- `(TYPE *)0`：把地址 0 强制转换为 TYPE* 类型的指针。

- `((TYPE *)0)->MEMBER`：访问这个“虚拟结构体”的成员。因为基地址是 0，所以这个成员的地址就是它在结构体中的偏移量。

- `&((TYPE *)0)->MEMBER`：取成员的地址，得到的就是偏移量。

- `(size_t)`：把结果转成整数类型。

**所以 offsetof(TYPE, MEMBER) 就能得到某个成员在结构体里的偏移量。**

container_of 的原理说明：

- `ptr`：这是某个结构体成员的指针。
- `typeof(((type *)0)->member)`：获取成员的类型，保证类型安全。
- `__mptr = (ptr)`：把传入的成员指针保存到临时变量。
- `(char *)__mptr - offsetof(type, member)`：成员指针减去偏移量，就能得到结构体首地址。
-`(type *)...`：再把结果强制转换为结构体类型指针。

**最终效果：已知成员指针，反推出整个结构体指针。**

示例代码：

```c
struct device {
    int id;
    char name[16];
    int status;
    struct list_head node; // 链表节点
};
struct list_head *pos = ...; // 遍历链表时拿到的是 node 的指针
struct device *dev = container_of(pos, struct device, node);
```

这里 `pos` 是 `node` 的指针，`container_of` 会自动算出 `node` 在 `struct device` 里的偏移量，然后用 `pos - 偏移量` 得到 `struct device` 的首地址，最终得到 `dev`。

------

## 5. 编译期断言 —— 把错误扼杀在摇篮里

### C11引入的新特性：`_Static_assert`

与运行时的 assert() 不同，_Static_assert可以在编译阶段就触发错误，降低运行损耗；

```c
_Static_assert(sizeof(int) == 4, "int must be 4 bytes");
```

上述代码表示：判断`sizeof(int) == 4`，如果不为真，编译器就会报错并输出后面的错误信息；

### Linux 内核中的 `BUILD_BUG_ON`

```c
#define BUILD_BUG_ON(condition) \
    ((void)sizeof(char[1 - 2 * !!(condition)]))
```

工作原理

1. `!!(condition)`：把条件转换为布尔值（0 或 1）。

2. `1 - 2 * !!(condition)`：

    - 如果 condition == 0 → !!(condition) = 0 → 结果为 1。

    - 如果 condition != 0 → !!(condition) = 1 → 结果为 -1。

3. `char[1 - 2 * !!(condition)]`：

    - 当结果为 1 → 数组大小合法，编译通过。
    - 当结果为 -1 → 数组大小为负数，编译器报错。

因此，只要 condition 为真，编译器就会报错，强制开发者在编译期发现问题。

使用场景：

- 结构体大小检查，确保某个结构体不会超过硬件要求的大小；

```c
BUILD_BUG_ON(sizeof(struct dma_descriptor) > 256);
```

- 枚举值和数组大小一致性，防止查找表和枚举定义不匹配；

```c
BUILD_BUG_ON(ARRAY_SIZE(cmd_handlers) != CMD_MAX);
```

## 6. X-Macro —— 一次定义，到处展开

核心思想是：把一组数据（错误码和描述）集中定义在一个宏里，然后用不同的“展开方式”生成枚举、字符串数组和函数逻辑。这样可以避免多处重复维护，保证同步

这种宏模式在协议解析、状态机定义里特别好用，属于“效率神器”。

```c
#define ERROR_TABLE(X) \
    X(ERR_NONE, "No error") \
    X(ERR_TIMEOUT, "Operation timeout") \
    X(ERR_OVERFLOW, "Buffer overflow") \
    X(ERR_CRC, "CRC check failed")

#define MAKE_ENUM(id, desc) id,
enum error_code { ERROR_TABLE(MAKE_ENUM) };

#define MAKE_STR(id, desc) [id] = desc,
const char *error_strings[] = { ERROR_TABLE(MAKE_STR) };

#define MAKE_CASE(id, desc) case id: return desc;
const char *error_to_string(enum error_code code) {
    switch (code) {
        ERROR_TABLE(MAKE_CASE)
        default: return "Unknown";
    }
}
```

上述代码分析：
将错误码统一定义在宏`ERROR_TABLE(X)`,然后再分别定义三个宏展开为不同的形式（枚举，数组，case条件）；，其中:
`id`=`{ERR_NONE, ERR_TIMEOUT, ERR_OVERFLOW, ERR_CRC}`，
`desc`=`{"No error","Operation timeout","Buffer overflow","CRC check failed"}`



## 总结

- 能用函数就别硬用宏，滥用宏会增加无用的代码体积；
- 宏要写得安全，避免副作用；
- 调试友好很重要，日志宏和断言宏是救命的；
- 不要炫技，宏只在合适的场景下用；

以上就是本篇文章的全部内容，如果对你有帮助请帮忙点个赞和推荐，你的支持是我持续创作的最大动力，非常感谢；
