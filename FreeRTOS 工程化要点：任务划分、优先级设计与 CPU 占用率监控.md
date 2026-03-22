 

大家好，我是杂烩君。

今天我们来简单分享：**FreeRTOS任务怎么拆、优先级怎么配、CPU 占用怎么看**。

![图片](https://mmbiz.qpic.cn/mmbiz_png/K62vJYPSiaghnjIzK5qaQ3v0RxGNmG742jn7uqzGquXibH4cY6E7vY7NjRcgQwSI8QBib0G66jEsVibiaXaASXJYhSqfCLXG4Jv9lxORgPxpkBGM/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=0)

## 1. 任务划分原则

### 1.1 单一职责：一个任务只干一件事

把"串口接收 + 数据解析 + 指令执行 + 结果反馈"全塞一个任务，解析环节一出问题整条链路全挂，而且任务执行时间拉长后会阻塞其他高优先级任务。

正确做法：按照职责拆成各独立任务，通过队列串联，某一环节异常不影响其他环节。

### 1.2 低耦合与合理粒度

任务间数据传递走队列、任务通知、事件组，别直接读写全局变量。任务数量上，一般中小项目控制在 **5~8 个**比较合理——太少功能挤一起难维护，太多上下文切换开销吃不消。

划分流程可以按下面这个思路走：

![图片](https://mmbiz.qpic.cn/mmbiz_png/K62vJYPSiagh7a1tYvnn5djqkbB51ViahQW2TeHasX3dfecBFCesHvyeMaIXqlMR9bjemwnibAS6fn0SYIkV9DTaNxibndo07bCDiaKISyaGSSak/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=1)

### 1.3 轻量化：单次执行控制在 10ms 以内

看一下内核中空闲任务的主循环（`tasks.c`）：

```c
for( ; configCONTROL_INFINITE_LOOP(); )
{
    prvCheckTasksWaitingTermination();

    #if ( configUSE_IDLE_HOOK == 1 )
    {
        vApplicationIdleHook();
    }
    #endif

    /* ... tickless idle, yield 逻辑 ... */
}
```

空闲任务只有在**所有其他任务都阻塞**时才能拿到 CPU。你的任务一跑就是几十毫秒不释放，空闲任务跑不了，低功耗和看门狗喂狗逻辑都会出问题。耗时操作必须拆子任务或分段执行。

## 2. 优先级设计

### 2.1 核心原则：数字越大，优先级越高

这是非常多人搞反的点。FreeRTOS 中空闲任务优先级为 0，是**最低**的：

```c
/* FreeRTOS-Kernel/include/task.h */   
#define tskIDLE_PRIORITY    ( ( UBaseType_t ) 0U )  
```

优先级范围是 `0` 到 `configMAX_PRIORITIES - 1`，数字越大越先执行。分配时，核心实时任务给大数字，后台任务给小数字。非必要别让多个任务共用同一优先级，否则触发时间片轮转，增加切换开销。

|任务类型|优先级|说明|
|---|---|---|
|故障报警|10|必须最快响应|
|传感器采集|8|实时性要求高|
|数据解析/指令执行|6||
|外设通信（串口等）|4||
|显示/按键|3||
|日志/自检|1|仅高于空闲任务|

## 3. CPU 占用率监控

CPU 占用率是系统健康度的晴雨表，建议控制在 30%~70%。FreeRTOS 内核原生支持运行时统计（`configGENERATE_RUN_TIME_STATS`），每次上下文切换时自动累计每个任务的运行时间，精度高、零应用层计算代码，直接用就行。

### 3.1 内核原理

开启 `configGENERATE_RUN_TIME_STATS` 后，内核在每次任务切换时执行以下逻辑（`tasks.c`）：

```c
/* FreeRTOS-Kernel/tasks.c — 上下文切换时更新运行时统计 */
#if ( configGENERATE_RUN_TIME_STATS == 1 )
{
    ulTotalRunTime[0] = portGET_RUN_TIME_COUNTER_VALUE();
    if( ulTotalRunTime[0] > ulTaskSwitchedInTime[0] )
    {
        pxCurrentTCB->ulRunTimeCounter +=
            ( ulTotalRunTime[0] - ulTaskSwitchedInTime[0] );
    }
    ulTaskSwitchedInTime[0] = ulTotalRunTime[0];
}
#endif 
```


每个 TCB 里有个 `ulRunTimeCounter` 字段，记录该任务累计占用 CPU 的时间。调用 `vTaskGetRunTimeStats()` 时，内核遍历所有任务，算出每个任务的时间占比，格式化成可读字符串直接输出。

你只需要做两件事：**配好 FreeRTOSConfig.h** + **提供一个高精度计数器**。

### 3.2 验证代码

下面是一个例子：用 **DWT 周期计数器**作为运行时统计的时间基准，创建了两个不同负载的测试任务 + 一个监控任务，串口输出统计结果。

#### 3.2.1 FreeRTOSConfig.h 需要增加的配置

在你已有的 `FreeRTOSConfig.h` 中加入以下内容：

```c

/* ---------- 运行时统计相关 ---------- */
#define configGENERATE_RUN_TIME_STATS           1
#define configUSE_TRACE_FACILITY                1
#define configUSE_STATS_FORMATTING_FUNCTIONS    1

extern void     vConfigureTimerForRunTimeStats(void);
extern uint32_t ulGetRunTimeCounterValue(void);
#define portCONFIGURE_TIMER_FOR_RUN_TIME_STATS() vConfigureTimerForRunTimeStats()
#define portGET_RUN_TIME_COUNTER_VALUE()         ulGetRunTimeCounterValue()
```



这三个宏缺一不可：`configGENERATE_RUN_TIME_STATS` 开启统计功能，`configUSE_TRACE_FACILITY` 开启 `uxTaskGetSystemState()`，`configUSE_STATS_FORMATTING_FUNCTIONS` 开启 `vTaskGetRunTimeStats()` 格式化输出。

#### 3.2.2 核心测试代码

```c
/*------------------------------------------------------------  
 *    运行时统计计数器 — DWT CYCCNT (内核自带, 不占任何外设)  
 *  
 *    DWT 单元有一个 32 位周期计数器,  
 *    以内核主频计数 (L431 最高 80MHz → 分辨率 12.5ns)  
 *    32 位在 80MHz 下约 53.7 秒溢出, 但内核每次上下文切换  
 *    都会读取并做差值累加, 任务切换间隔远小于该值, 不影响统计  
 *------------------------------------------------------------*/  
void vConfigureTimerForRunTimeStats(void)  
{  
    CoreDebug->DEMCR |= CoreDebug_DEMCR_TRCENA_Msk;  
    DWT->CYCCNT = 0;  
    DWT->CTRL |= DWT_CTRL_CYCCNTENA_Msk;  
}  
  
uint32_t ulGetRunTimeCounterValue(void)  
{  
    return DWT->CYCCNT;  
}  
  
/*------------------------------------------------------------  
 * 测试任务  
 *------------------------------------------------------------*/  
  
/* 重载任务: 忙等 ~5ms + 休眠 20ms, 预期占用约 20% CPU */  
void Task_Heavy(void *pv)  
{  
    volatileuint32_t i;  
    for (;;)  
    {  
        for (i = 0; i < 20000; i++) {}  /* 忙等, 模拟计算负载 */  
        vTaskDelay(pdMS_TO_TICKS(20));  
    }  
}  
  
/* 轻载任务: 几乎不占 CPU, 500ms 唤醒一次做极少工作 */  
void Task_Light(void *pv)  
{  
    volatileuint32_t count = 0;  
    for (;;)  
    {  
        count++;  
        vTaskDelay(pdMS_TO_TICKS(500));  
    }  
}  
  
/* 监控任务: 每 3 秒输出一次各任务的运行时间统计 */  
void Task_Monitor(void *pv)  
{  
    staticchar buf[400];  
  
    for (;;)  
    {  
        vTaskDelay(pdMS_TO_TICKS(3000));  
  
        printf("\r\n-------- Run Time Stats --------\r\n");  
        printf("Task            Abs Time    %%Time\r\n");  
        vTaskGetRunTimeStats(buf);  
        printf("%s", buf);  
        printf("--------------------------------\r\n");  
    }  
}  
  
int main(void)  
{  
    // ...  
  
    xTaskCreate(Task_Heavy,   "Heavy",   256, NULL, 4, NULL);  
    xTaskCreate(Task_Light,   "Light",   128, NULL, 3, NULL);  
    xTaskCreate(Task_Monitor, "Monitor", 512, NULL, 1, NULL);  
  
    vTaskStartScheduler();  
  
    for (;;) {}  
}
```


#### 3.2.3 串口输出

每 3 秒会看到类似输出：

![[Pasted image 20260321130522.png]]


`Heavy` 任务忙等约 5ms + 休眠 20ms，占用约 16%，符合预期。`IDLE` 占 83% 说明系统大部分时间空闲。
如果你在自己的项目里加上这个监控任务，一眼就能看出哪个任务吃 CPU 最多。

### 3.3 异常速查

|现象|常见原因|优化方向|
|---|---|---|
|>80%|死循环无延时、中断过频、资源竞争激烈|拆分耗时任务、降低中断频率|
|<20%|延时过长、执行频率低|缩短延时、充分利用 CPU|
|波动大|突发耗时操作、中断源不稳定|分段执行、稳定中断源|

## 4. 总结

FreeRTOS 工程落地就三件事：

1. **任务划分**——单一职责、轻量化、低耦合，5~8 个任务为宜，别把所有逻辑塞一个任务里，也别拆太碎。
    
2. **优先级设计**——数字越大优先级越高（`tskIDLE_PRIORITY = 0` 是最低），核心实时任务给高值，后台任务给低值；共享资源用 `xSemaphoreCreateMutex()`，优先级继承内核自动搞定。
    
3. **CPU 占用率监控**——开启 `configGENERATE_RUN_TIME_STATS`，用 DWT CYCCNT 做时间基准（不占外设），`vTaskGetRunTimeStats()` 直接输出每个任务的占比，30%~70% 是健康区间。