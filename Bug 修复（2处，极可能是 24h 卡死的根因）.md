### Bug 修复（2处，极可能是 24h 卡死的根因）

1. [MINI_R32.h:53-54](vscode-webview://0m550pj458ss7643ilp3lfhuu1h9m92lava4k1je6ospmbh4qqj5/src/user_code/MINI_R32.h#L53-L54) — GCC 分支下 `ENTER_CRITICAL_SECTION` / `EXIT_CRITICAL_SECTION` 原来是空宏（被注释掉了），现在恢复为调用 `R_BSP_InterruptsDisable()` / `R_BSP_InterruptsEnable()`。这意味着之前 `app_tim.c` 里所有定时器链表操作完全没有并发保护，长时间运行后竞态条件导致链表损坏 → 遍历死循环。
2. [app_tim.c:158](vscode-webview://0m550pj458ss7643ilp3lfhuu1h9m92lava4k1je6ospmbh4qqj5/src/user_code/app_tim.c#L158) — 第二个 `ENTER_CRITICAL_SECTION()` 改为 `EXIT_CRITICAL_SECTION()`。

### 崩溃诊断机制（新增）

1. 新建 [crash_log.h](vscode-webview://0m550pj458ss7643ilp3lfhuu1h9m92lava4k1je6ospmbh4qqj5/src/user_code/crash_log.h) / [crash_log.c](vscode-webview://0m550pj458ss7643ilp3lfhuu1h9m92lava4k1je6ospmbh4qqj5/src/user_code/crash_log.c) — 崩溃记录模块，使用 `.noinit` 段（软复位不清零），包含：
   - `crash_log_save()` — 在断言/故障处理函数中保存崩溃类型、PC、文件名、行号、任务名、tick、剩余堆
   - `crash_log_sci9_puts()` — 直接轮询 SCI9 寄存器输出字符串，不依赖中断和 RTOS
   - `crash_log_check_and_report()` — 开机检查并通过 EasyLogger 输出上次崩溃信息
2. 改造了 4 个死循环处理函数，在卡死前保存"遗言"：
   - `vAssertCalled()` — [freertos_start.c:298](vscode-webview://0m550pj458ss7643ilp3lfhuu1h9m92lava4k1je6ospmbh4qqj5/src/frtos_startup/freertos_start.c#L298)
   - `vApplicationMallocFailedHook()` — [freertos_start.c:360](vscode-webview://0m550pj458ss7643ilp3lfhuu1h9m92lava4k1je6ospmbh4qqj5/src/frtos_startup/freertos_start.c#L360)
   - `vApplicationStackOverflowHook()` — [freertos_start.c:386](vscode-webview://0m550pj458ss7643ilp3lfhuu1h9m92lava4k1je6ospmbh4qqj5/src/frtos_startup/freertos_start.c#L386)
   - `TbxAssertTrigger()` — [tbx_assert.c:106](vscode-webview://0m550pj458ss7643ilp3lfhuu1h9m92lava4k1je6ospmbh4qqj5/src/libmicroblt_v0.9.0/microtbx/source/tbx_assert.c#L106)
3. [MINI_R32.c:206](vscode-webview://0m550pj458ss7643ilp3lfhuu1h9m92lava4k1je6ospmbh4qqj5/src/user_code/MINI_R32.c#L206) — `main_task` 中添加 `crash_log_check_and_report()` 调用，开机时如果检测到上次崩溃记录，会通过 EasyLogger 输出完整信息。

### 使用方式

- 如果系统卡死后被手动复位（或看门狗复位），下次开机串口会自动输出上次崩溃的类型、PC 地址、tick 值、剩余堆大小等信息
- 拿到 PC 地址后，用 `rx-elf-addr2line -e your.elf 0xXXXXXXXX` 即可定位到具体源码行
- 卡死瞬间 SCI9 也会直接轮询输出一行提示（如果串口有连接的话）





你说得对，全局关中断确实不是最优方案。让我分析一下几种选择：

**对于 `app_tim.c` 的定时器链表保护，有三种方案：**

| 方案                        | 优点                                                         | 缺点                                |
| --------------------------- | ------------------------------------------------------------ | ----------------------------------- |
| `R_BSP_InterruptsDisable()` | 简单                                                         | 关闭所有中断，影响实时性            |
| `taskENTER_CRITICAL()`      | 只屏蔽到 `configMAX_SYSCALL_INTERRUPT_PRIORITY`，高优先级中断不受影响，可嵌套 | 仍然关部分中断                      |
| FreeRTOS Mutex              | 不关中断，有优先级继承                                       | 不能在 ISR 中使用，有上下文切换开销 |

`vtimer_process` 是从 FreeRTOS 软件定时器回调调用的（Timer Task 上下文），`vtimer_register`/`vtimer_delete` 也都是任务上下文调用，没有 ISR 调用的情况。所以 mutex 是可行的。

而且关键点：`vtimer_process` 里的临界区退出后才调用 `vtimer_register`（它有自己的临界区），所以不需要递归互斥量，普通 mutex 就行。

不过这些临界区都非常短（就几个指针操作），用 mutex 的上下文切换开销反而不划算。最佳折中是 `taskENTER_CRITICAL()` / `taskEXIT_CRITICAL()` — FreeRTOS 推荐的短临界区方案。

我建议：`MINI_R32.h` 的通用宏改用 `taskENTER_CRITICAL`，然后 `app_tim.c` 里直接用这个就行。



- `ENTER_CRITICAL_SECTION()` / `EXIT_CRITICAL_SECTION()` - only used in `app_tim.c` (the MINI_R32.h macros)
- `SPI_ENTER_CRITICAL_SECTION()` / `SPI_EXIT_CRITICAL_SECTION()` - for QSPI flash
- `CLI_ENTER_CRITICAL_SECTION()` / `CLI_EXIT_CRITICAL_SECTION()` - for CLI
- `E2_ENTER_CRITICAL_SECTION()` / `E2_EXIT_CRITICAL_SECTION()` - for EEPROM
- `MODBUS_ENTER_CRITICAL_SECTION()` / `MODBUS_EXIT_CRITICAL_SECTION()` - for Modbus