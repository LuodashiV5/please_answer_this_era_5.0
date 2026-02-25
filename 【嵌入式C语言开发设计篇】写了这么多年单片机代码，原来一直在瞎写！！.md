

原创 进击的小芯 

> 不知道各位嵌入式开发的小伙伴有没有遇到过这样的场景：
>
> - • 已经量产的产品突然采购的同事说有一款芯片更便宜可以降低成本，要求马上更换测试；
> - • 老板突然心血来潮，要给快开发完成的产品增加硬件功能；
> - • 前同事已离职，你接手了他遗留的项目，市场部有新需求需要增加功能，但是打开项目一看：代码逻辑混乱，不知从哪下手修改。

本文针对以上痛点（换芯片、增加硬件、维护遗留项目），介绍五种最常用的模式，并给出**C代码示例**以便更好理解，同时总结几种违反模式的操作实例说明。

------

## 1. 什么是设计模式

设计模式是对软件设计中反复出现问题的通用解决方案或模板，它描述了在特定情境下如何组织类、对象及它们之间的交互从而解决常见的设计问题。设计模式不是可直接拷贝的代码，而是一种软件设计思想和可复用的代码结构设计方法。

## 2. 设计模式的核心思想

- **解耦**：把硬件驱动和业务逻辑分离，降低修改风险与回归成本。
- **复用**：把通用功能抽象成可复用模块，避免复制粘贴带来的维护负担。
- **扩展**：通过接口和工厂等机制新增功能时尽量不改动已有代码，减少引入新 bug 的概率。

------

## 3. 单例模式：管好硬件资源

**特点**：保证系统中某个对象只有一个实例，通常用于管理唯一硬件资源或全局服务。
**适用场景**：UART、SPI、I2C、外设控制器、系统日志模块等全局唯一资源。
**优点**：避免重复初始化、配置冲突；提供统一访问点。
**注意事项**：避免把过多业务逻辑塞入单例，防止全局状态污染和测试困难。

```c
// uart_singleton.c
#include <stdint.h>
#include <stdio.h>

typedefstruct {
    int initialized;
    uint32_t baudrate;
} UART_Handle;

UART_Handle* UART_GetInstance(void) {
    static UART_Handle uart;
    if (!uart.initialized) {
        // 实际硬件初始化放在这里
        uart.baudrate = 115200;
        uart.initialized = 1;
        printf("UART initialized\n");
    }
    return &uart;
}

// 使用示例
UART_Handle* u = UART_GetInstance();
```

## 4. 适配器模式：让换芯片不再痛苦

- 特点：通过定义统一接口屏蔽底层平台差异，上层只依赖接口而非具体实现。
- 适用场景：需要支持多种 MCU、替换 HAL 库、跨平台移植、统一外设操作。
-  优点：换芯片或替换底层库时只需实现适配层，上层业务代码无需修改。
- 注意事项：接口设计要稳健且尽量覆盖上层需求，避免频繁改动接口。

```c
// gpio_adapter.h
typedefstruct {
    void (*write)(int pin, int value);
    int  (*read)(int pin);
} GPIO_Interface;

// stm32_gpio.c
#include "gpio_adapter.h"
#include <stdio.h>

void stm32_gpio_write(int pin, int value) {
    // HAL_GPIO_WritePin(...)
    printf("STM32 GPIO write pin=%d val=%d\n", pin, value);
}
int stm32_gpio_read(int pin) {
    // return HAL_GPIO_ReadPin(...);
    return 0;
}

GPIO_Interface stm32_gpio = {
    .write = stm32_gpio_write,
    .read  = stm32_gpio_read
};
// 上层只依赖 GPIO_Interface，不关心底层实现
```

## 5. 工厂模式：优雅地管理多种设备

- 特点：把对象创建逻辑集中管理，返回统一接口的实例，避免在业务代码中散布大量 if-else。
- 适用场景：多种传感器、多种外设驱动、插件式设备扩展。
- 优点：新增设备只需实现接口并在工厂注册，业务代码无需改动。
- 注意事项：工厂实现要易于扩展，注册机制要清晰（静态表或注册函数）。

```c
// sensor.h
typedefstruct {
    void (*init)(void);
    float (*read)(void);
} Sensor;

// ds18b20.c
#include "sensor.h"
#include <stdio.h>

static void ds18b20_init(void) { printf("DS18B20 init\n"); }
static float ds18b20_read(void) { return 25.0f; }

Sensor DS18B20 = {
    .init = ds18b20_init,
    .read = ds18b20_read
};

// ntc.c
static void ntc_init(void) { printf("NTC init\n"); }
static float ntc_read(void) { return 30.5f; }
Sensor NTC = { .init = ntc_init, .read = ntc_read };

// factory.c
#include <string.h>
Sensor* SensorFactory(const char* type) {
    if (strcmp(type, "DS18B20") == 0) return &DS18B20;
    if (strcmp(type, "NTC") == 0) return &NTC;
    return NULL;
}

// 使用示例
/*
Sensor* s = SensorFactory("DS18B20");
if (s) { s->init(); float t = s->read(); }
*/
```

## 6. 对象池模式：在内存紧张时优雅地活着

- 特点：预先分配固定数量对象并循环复用，避免运行时频繁 malloc/free 导致内存碎片。
- 适用场景：通信缓冲区、短生命周期对象、任务/消息对象池。
- 优点：降低内存碎片风险、提高分配效率、可预测内存使用。
- 注意事项：池大小需根据最大并发需求合理规划；要处理池耗尽的情况（返回错误或阻塞）。

```c
// obj_pool.c
#include <string.h>
#include <stdio.h>

#define POOL_SIZE 5

typedefstruct {
    int used;
    char buf[64];
} Obj;

static Obj pool[POOL_SIZE];

Obj* pool_get(void) {
    for (int i = 0; i < POOL_SIZE; ++i) {
        if (!pool[i].used) {
            pool[i].used = 1;
            memset(pool[i].buf, 0, sizeof(pool[i].buf));
            return &pool[i];
        }
    }
    return NULL; // 池耗尽
}

void pool_release(Obj* o) {
    if (o) o->used = 0;
}

// 使用示例
/*
Obj* o = pool_get();
if (o) { strcpy(o->buf, "data"); pool_release(o); }
*/
```

## 7. 观察者模式：让模块之间不再互相纠缠

- 特点：事件发布-订阅机制，发布者只负责发事件，订阅者自行注册处理逻辑，实现模块解耦。
- 适用场景：按键事件、状态变化通知、日志/监控、异步消息分发。
- 优点：新增响应逻辑无需修改事件源；便于扩展和模块化。
- 注意事项：订阅者过多或处理耗时会影响发布者性能，必要时使用任务队列或异步处理。

```c
// event_bus.c
#include <stdio.h>

#define MAX_OBSERVERS 8

typedef void (*Observer)(void* ctx);

static Observer observers[MAX_OBSERVERS];
static void* observer_ctx[MAX_OBSERVERS];
static int obs_count = 0;

int subscribe(Observer o, void* ctx) {
    if (obs_count >= MAX_OBSERVERS) return -1;
    observers[obs_count] = o;
    observer_ctx[obs_count] = ctx;
    obs_count++;
    return 0;
}

void notify_all(void) {
    for (int i = 0; i < obs_count; ++i) {
        if (observers[i]) observers[i](observer_ctx[i]);
    }
}

// 示例订阅者
void display_update(void* ctx) { printf("Display update: %s\n", (char*)ctx); }
void relay_control(void* ctx) { printf("Relay control: %s\n", (char*)ctx); }

// 使用示例
/*
subscribe(display_update, "Key1");
subscribe(relay_control, "Key1");
// 按键中断或轮询检测到按下时：
notify_all();
*/
```

### 实践总结

| 模式       | 适用场景                       | 优点                      | 注意事项                       |
| ---------- | ------------------------------ | ------------------------- | ------------------------------ |
| 单例模式   | 硬件资源唯一（UART\SPI\I2C等） | 避免重复初始化、统一管理  | 避免全局状态过多，影响测试     |
| 适配器模式 | 换芯片、跨平台、硬件抽象       | 上层代码无需修改          | 接口设计要稳定且覆盖需求       |
| 工厂模式   | 多种传感器/设备管理            | 避免大量的if-else，易拓展 | 工厂注册/维护要清晰            |
| 对象池模式 | 内存紧张、频繁分配释放场景     | 减少碎片、提高效率        | 池大小需合理、处理耗尽对应策略 |
| 观察者模式 | 事件驱动、按键、消息通知       | 模块解耦、易拓展          | 订阅者过多或耗时处理需异步化   |



## 常见反模式操作

### 1. 把所有逻辑塞进 main.c

问题：文件臃肿、可读性差、修改风险高。

后果：任何小改动都可能牵动全局，难以定位 bug。

改进：按功能拆分模块，按层次划分目录（driver、hal、service、app）。

### 2. 滥用全局变量

问题：状态不可控、并发/中断下易出错、测试困难。

后果：难以追踪数据流，模块间耦合度高。

改进：尽量使用局部变量、通过接口传参或单例封装共享资源。

### 3. 频繁在运行时 malloc/free

问题：内存碎片、不可预测的失败。

后果：长期运行后系统崩溃或行为异常。

改进：使用对象池或静态分配，避免动态分配在关键路径。

### 4. 硬编码硬件寄存器和魔法数字

问题：可移植性差、难以维护。

后果：换芯片或修改引脚时需要大范围改动。

改进：使用宏、配置表或适配层抽象硬件细节。

### 5. 过度设计或过度抽象

问题：为小项目引入复杂架构，增加理解成本。

后果：开发效率下降，团队成员难以上手。

改进：按需设计，先简单后重构；在增长到需要时再引入复杂模式。

### 6. 业务逻辑与驱动混在一起

问题：换芯片或替换驱动时需要改动业务代码。

后果：移植成本高，回归测试量大。

改进：严格分层，驱动层只做硬件操作，上层通过接口调用。



## 总结

设计模式的是计算机发展史上无数人总结出的宝贵经验，掌握设计模式，将使你从纯粹的码农逐渐成长为有全局思维，有架构设计能力的程序员，架构师。

但设计模式不是万能套路，在中大型或长期维护的嵌入式项目中，它们能显著提升代码的可维护性、可扩展性和可读性，在项目初期花一点时间做代码架构设计，后续能省下大量返工时间。对于小型项目，可能会增加额外的资源消耗，需根据实际情况应用；

**写能跑的代码不难，写别人能看懂、能维护、能扩展的代码才是真本事。**