
# Zephyr 嵌入式实时操作系统深度解析与开发者实战指南

在嵌入式开发领域，随着物联网（IoT）设备复杂性的爆炸式增长，传统的裸机开发（Bare-metal）或单一功能的实时操作系统（RTOS）已难以满足现代项目对连接性、安全性及硬件无关性的严苛要求。Zephyr 项目作为 Linux 基金会旗下的开源实时操作系统，正是在这一背景下应运而生，旨在为资源受限的设备提供一个可扩展、安全且功能完备的软件平台 1。

## 第一章 Zephyr 生态系统概述与核心哲学

Zephyr 的诞生标志着嵌入式系统从“厂商驱动”向“生态驱动”的重大转型。它不仅仅是一个内核，更是一个完整的软件协议栈解决方案。

### 生态定位与开源治理

Zephyr 专为小型网关、终端接口及各类传感器等资源受限设备量身定制，其设计目标是覆盖从仅有几 KB 内存的超低功耗单片机到功能复杂的工业级多核控制器 1。这种极高的适应性得益于其托管在 Linux 基金会旗下的中立治理结构。包括 Nordic Semiconductor、STMicroelectronics、NXP、Renesas 等在内的全球主流 MCU 厂商均是该项目的活跃成员，这确保了硬件驱动的即时更新与生态的长期稳定性 1。

Zephyr 采用 Apache 2.0 许可证，这种极具包容性的授权模式允许开发者自由地使用、修改及分发代码，且无需强制开源其私有应用部分，极大地保护了商业用户的知识产权 1。

### 核心架构哲学：模块化与可配置性

Zephyr 架构的核心哲学在于“按需付费”。系统被划分为高度模块化的内核服务、中间件协议栈及驱动模型 5。开发者可以通过精细化的配置，仅将必要的代码编译进最终镜像，从而在几 KB 内存的设备上实现复杂功能 1。


|      |                                |                    |
| ---- | ------------------------------ | ------------------ |
| 特性维度 | Zephyr 的实现方式                   | 对开发者的意义            |
| 内存占用 | 最小镜像可压缩至 2-8 KB                | 适用于极低成本硬件 7        |
| 跨平台性 | 支持 ARM, x86, RISC-V, ARC 等多种架构 | 代码可在不同芯片间无缝迁移 1    |
| 集成度  | 内置 BLE, TCP/IP, 文件系统, USB 协议栈  | 缩短从零到产品原型的时间 6     |
| 安全性  | 硬件强制的栈溢出保护、内存域隔离               | 提高设备在物联网环境下的生存能力 3 |

## 第二章 构建与配置系统的“三位一体”

掌握 Zephyr 的关键不在于编写 C 代码本身，而在于理解其独特的构建与配置体系：West、Kconfig 与 Devicetree。这种体系被形象地称为“80% 配置，20% 编码”模式 7。

### West：多仓库管理与元工具

West 是 Zephyr 开发流程的指挥官，它不仅管理着庞大的代码仓库及其依赖项，还作为 CMake、Ninja 及各种调试工具的统一接口 8。

West 的核心是 west.yml 清单文件，它定义了整个工作区（Workspace）的拓扑结构。例如，在“T3 Forest”拓扑中，清单文件详细记录了项目所需的外部模块、HAL 库及协议栈的版本 10。通过 west init 和 west update 命令，开发者可以一键同步跨越数十个 Git 仓库的开发环境，确保团队成员的工作区完全一致 12。

### Kconfig：软件功能的精细剪裁

源自 Linux 内核的 Kconfig 系统负责软件特性的条件编译。它通过分层配置管理，允许开发者在不修改源代码的情况下，通过 prj.conf 文件或交互式界面（如 menuconfig）控制每一个功能点的开关 8。

Kconfig 符号分为有提示符（可手动修改）和无提示符（由依赖关系自动激活）两类 14。例如，当开发者在 prj.conf 中声明 CONFIG_USB_CONSOLE=y 时，Kconfig 系统会自动检查其依赖项（如 CONFIG_CONSOLE），并解析出最优的配置方案 14。如果尝试禁用某个被其他功能强制依赖的项，系统会通过其特有的依赖解析机制自动重新启用，从而保证构建的一致性 8。

### Devicetree：硬件描述的标准化

Devicetree（设备树）是硬件拓扑结构的机器可读描述。它将硬件资源（如引脚分配、时钟频率、中断号）从 C 代码中剥离出来，存放于 .dts 和 .dtsi 文件中 15。

这种设计的精妙之处在于，当硬件发生变更时（例如 LED 从 PA5 换到了 PB1），开发者只需修改设备树叠加层（Overlay），而无需改动业务逻辑代码 16。构建系统会将 DTS 文件编译为一系列 C 宏定义（生成在 devicetree_generated.h 中），驱动程序则通过 DT_DRV_INST 等宏直接获取硬件参数 11。这种模式彻底解决了嵌入式开发中长期存在的硬件描述代码分散在各个源文件中的混乱局面 16。

## 第三章 内核服务：调度与同步机制

Zephyr 内核是一个高度优化的抢占式实时调度器，专为低延迟、高确定性的应用设计 2。

### 线程模型与生命周期

Zephyr 的每个独立任务都运行在各自的线程中。线程分为两类：协作式线程（Cooperative Threads）和抢占式线程（Preemptive Threads） 17。

1. 协作式线程：优先级为负值。这类线程一旦获得 CPU，除非主动调用 k_yield()、k_sleep() 或进入等待状态，否则不会被其他同优先级或高优先级线程抢占 17。这在需要原子性操作或处理简单、快速任务时非常有用。
    
2. 抢占式线程：优先级为非负值。更高优先级的抢占式线程可以在任何时刻中断低优先级线程的执行，确保了系统对紧急事件的最高响应速度 17。
    

  

|   |   |   |
|---|---|---|
|线程创建方式|使用场景|优势|
|静态创建 (K_THREAD_DEFINE)|长期运行的背景任务（如网络监听）|编译时分配内存，启动速度快 19|
|动态创建 (k_thread_create)|根据事件触发的短期任务|灵活管理内存资源，按需开启 19|

### 调度算法的多样性

为了兼顾资源消耗与复杂性，Zephyr 提供了多种调度队列实现。对于资源极度受限的单线程应用，可以使用简单的链表队列（CONFIG_SCHED_SIMPLE）；而对于具有大量活跃线程的复杂系统，红黑树（Red/Black Tree）算法能提供更优的性能扩展性 17。此外，Zephyr 还支持最早截止时间优先（EDF）调度，这允许线程基于其任务的紧急程度（截止时间）而非固定的静态优先级进行执行，极大增强了硬实时系统的灵活性 6。

### 同步与通信原语

线程间的协调通过丰富的内核对象实现。信号量（Semaphores）用于同步和资源计数；互斥锁（Mutexes）提供优先级继承机制，防止实时系统中最棘手的优先级翻转问题 3。

特别值得关注的是工作队列（Workqueues）。它允许中断服务例程（ISR）将非紧急的后续处理推迟到特定的工作线程中执行，从而缩短中断关闭时间，提高系统整体的响应度 21。新一代的队列中心化工作队列 API 甚至支持多线程并行执行工作项，并优化了动态分配工作项的生命周期管理 23。

## 第四章 设备驱动模型：统一硬件接口

Zephyr 的设备驱动模型（Device Driver Model）是实现跨芯片移植的基石。它通过标准化的 API 隐藏了不同厂商寄存器操作的差异 6。

### 实例化与发现机制

在 Zephyr 中，每一个硬件设备（如串口 UART1）在软件中都对应一个 struct device 结构体 15。开发者通过 DEVICE_DT_GET 宏并传入设备树中的节点标签来获取设备句柄。在正式操作设备前，必须调用 device_is_ready() 进行验证，这确保了底层的时钟配置和硬件初始化已正确完成 25。

### 厂商支持矩阵

Zephyr 的驱动广度得益于各半导体巨头的深度参与。STMicroelectronics 为 STM32 系列提供了覆盖 ADC、CAN、DMA、Ethernet、USB 甚至专用无线协议（如 LoRa, IEEE 802.15.4）的全面支持 4。Nordic 则将其在蓝牙低功耗（BLE）领域的领先优势完全注入了 Zephyr 社区，使得 nRF52 系列成为学习和应用 Zephyr 无线功能的首选平台 26。

## 第五章 深度网络栈与连接性

作为“为 IoT 而生”的 OS，Zephyr 拥有一个完全原生的、针对嵌入式场景高度优化的网络协议栈 3。

### 蓝牙低功耗 (BLE) 5.0+

Zephyr 包含一个完全符合蓝牙 5.0 标准的协议栈。不同于许多 RTOS 依赖第三方库，Zephyr 的 BLE 栈是原生开发的，支持 Mesh 组网、GATT、GAP 及所有 LE 角色 3。

这种原生的力量在于其极高的灵活性。开发者可以将 Zephyr 配置为仅运行蓝牙控制器（Controller），并与运行在另一处理器上的 Linux 主机通过标准的 HCI 接口通信；或者直接在单块芯片上运行完整的 Host 与 Controller 组合，实现紧凑的单芯片解决方案 3。

### IP 联网与物联网协议

Zephyr 的网络子系统原生支持 IPv4 和 IPv6，并提供类似 BSD 的 Sockets API，极大地降低了具备 Linux 背景的开发者的上手难度 3。

  

|   |   |   |
|---|---|---|
|协议层|支持的技术|关键子系统|
|应用层|MQTT, LwM2M, SNTP, HTTP, CoAP|zperf 性能测试, sntp_async 异步授时 29|
|传输层|TCP, UDP, Websocket|BSD Sockets 兼容层 3|
|网络/数据链路|IPv6, 6LoWPAN, Thread, Wi-Fi, Ethernet|OpenThread 支持 2|

## 第六章 安全架构：从内核到边界

在物联网环境中，安全不再是可选项。Zephyr 的安全架构采用了纵深防御策略 1。

### 用户模式与内存隔离

对于配备内存保护单元（MPU）的 MCU，Zephyr 支持“用户模式”（User Mode）。这允许将不受信任的应用代码或复杂的协议栈（如文件系统、网络解析器）运行在低特权等级下 32。

在用户模式下，线程只能访问其被授予权限的特定内存区域（Memory Domains）和内核对象 31。任何跨越权限边界的操作必须通过系统调用（System Calls）进行。Zephyr 的构建系统会自动生成这些系统调用的校验代码，在运行时对参数进行严苛的边界检查和类型验证，有效防御了缓冲区溢出等常见攻击手段 31。

### 固件完整性与硬件安全

通过与 MCUboot 的深度集成，Zephyr 实现了安全的空中升级（OTA）和镜像签名验证 7。此外，它还支持 ARM 的 TrustZone 技术和 Trusted Firmware-M (TF-M)，通过硬件隔离创建一个执行机密操作（如存储私钥、执行加密算法）的“安全世界”，即使主操作系统被攻破，核心秘密依然安全 1。

## 第七章 开发者起步路径：从安装到 Blinky

作为新手，掌握 Zephyr 的第一步是建立正确的开发环境并跑通最基础的实验。

### 环境准备：跨平台开发阵地

Zephyr 的开发环境不再局限于昂贵的、厂商绑定的 IDE，而是拥抱现代化的命令行与跨平台工具链 13。

1. 依赖安装：在 Windows 上，推荐使用 winget 安装 CMake、Ninja、Python 等核心组件；Linux 用户则通过 apt 或 dnf 完成 13。
    
2. 虚拟环境：始终建议在 Python 虚拟环境（venv）中运行 pip install west，这能有效隔离不同项目间的依赖冲突 12。
    
3. SDK 安装：Zephyr SDK 包含了交叉编译器、调试器以及 QEMU 等仿真工具。通过 west sdk install 即可完成安装 13。
  
### Blinky：第一个硬件交互实验

“闪灯”程序（Blinky）在 Zephyr 中展示了设备树与驱动模型的协同工作。开发者不需要知道 LED 引脚的具体地址，而是通过别名 led0 在代码中引用。

```C
/* Blinky 代码逻辑流 */  
const struct gpio_dt_spec led = GPIO_DT_SPEC_GET(DT_ALIAS(led0), gpios);  
gpio_pin_configure_dt(&led, GPIO_OUTPUT_ACTIVE);  
while (1) {  
    gpio_pin_toggle_dt(&led);  
    k_msleep(1000);  
}  
```

通过 west build -b <board_name> 和 west flash，固件便能烧录进目标板。如果手头没有硬件，可以指定 -b qemu_cortex_m3 在模拟器中运行，这是验证算法逻辑的高效手段 36。

## 第八章 调试与优化实战：透视系统运行

在复杂 RTOS 项目中，单纯的 printf 往往会导致系统行为的改变。Zephyr 提供了更加专业的调试手段 38。

### 日志系统 (Logging) 与 Shell

Zephyr 的日志模块支持多种后端（UART, RTT, 网络等）并提供四种严重等级 38。

- 延迟处理：默认情况下，日志消息会被放入缓冲区，由低优先级线程在系统空闲时处理，最大程度减少对实时业务的影响 38。
    
- Panic 模式：当系统检测到不可恢复的错误（如 Kernel Panic）时，日志会自动切换到阻塞同步模式，确保最后的遗言能够完整输出到控制台 38。
    

Shell 模块则为设备提供了一个交互式的命令行界面。开发者可以在运行时通过串口扫描 I2C 总线设备、切换 GPIO 状态，甚至动态调整某个驱动模块的日志输出等级，而无需重启或重新编译镜像 38。

### 功耗管理：榨干每一焦耳能量

针对电池供电设备，Zephyr 提供了精细的功耗管理子系统 35。

- 系统功耗管理：在 CPU 空闲时自动进入睡眠状态，并根据下次定时器触发的时间选择最合适的休眠深度。
    
- 设备级功耗管理：允许独立控制每个外设的电源状态。例如，当线程请求读取传感器数据时，系统会自动唤醒该 I2C 控制器，读取完成后再次使其休眠 35。
    

## 第九章 结论：迈向大师之路

掌握 Zephyr OS 是一个从“面”到“点”的过程。起初，开发者可能会被其复杂的工具链和庞大的配置项所震慑，但一旦越过这条陡峭的学习曲线，所获得的回报将是巨大的：一份代码即可适配全球主流厂商的数百款芯片，一套工具链即可完成从简单传感器到复杂边缘计算节点的开发 7。

对于新手而言，建议的进阶路线是：首先通过 QEMU 模拟器熟悉内核调度与 IPC 原语；随后在 STM32 或 nRF52 平台上实践外设驱动与设备树；最后深入研究网络栈与安全特性。随着对 Kconfig 和 Devicetree 理解的加深，你会发现 Zephyr 并不是在增加负担，而是在为嵌入式软件工程的标准化与专业化铺就道路 39。

#### 引用的著作

1. zephyr rtos for embedded systems development - Witekio, 访问时间为 四月 5, 2026， [https://witekio.com/embedded-software/firmware/zephyr/](https://witekio.com/embedded-software/firmware/zephyr/)
    
2. Zephyr RTOS: Advantages of the Real-Time Operating System - ithinx, 访问时间为 四月 5, 2026， [https://www.ithinx.io/en/blog/software/what-is-zephyr-and-what-are-its-advantages/](https://www.ithinx.io/en/blog/software/what-is-zephyr-and-what-are-its-advantages/)
    
3. Introduction — Zephyr Project Documentation, 访问时间为 四月 5, 2026， [https://docs.zephyrproject.org/latest/introduction/index.html](https://docs.zephyrproject.org/latest/introduction/index.html)
    
4. Zephyr RTOS on STM32 MCUs: Open-Source Software - STMicroelectronics, 访问时间为 四月 5, 2026， [https://www.st.com/content/st_com/en/ecosystems/stm32-mcus-in-the-open-source-software-ecosystem/zephyr.html](https://www.st.com/content/st_com/en/ecosystems/stm32-mcus-in-the-open-source-software-ecosystem/zephyr.html)
    
5. Zephyr RTOS - What is it? Features, Examples and Benefits | Glossary, 访问时间为 四月 5, 2026， [https://conclusive.tech/glossary/introduction-to-zephyr-rtos-features-examples-and-benefits/](https://conclusive.tech/glossary/introduction-to-zephyr-rtos-features-examples-and-benefits/)
    
6. Zephyr - Open Source RTOS - OSRTOS, 访问时间为 四月 5, 2026， [https://osrtos.com/rtos/zephyr/](https://osrtos.com/rtos/zephyr/)
    
7. Zephyr RTOS vs FreeRTOS: A Comprehensive Comparison for IoT and Embedded Systems, 访问时间为 四月 5, 2026， [https://www.ezurio.com/resources/blog/zephyr-rtos-vs-freertos-a-comprehensive-comparison-for-iot-and-embedded-systems](https://www.ezurio.com/resources/blog/zephyr-rtos-vs-freertos-a-comprehensive-comparison-for-iot-and-embedded-systems)
8. How to Configure Zephyr RTOS: A Practical Guide to West, Kconfig, proj.conf, 访问时间为 四月 5, 2026， [https://www.beningo.com/how-to-configure-zephyr-rtos-a-practical-guide-to-west-kconfig-proj-conf/](https://www.beningo.com/how-to-configure-zephyr-rtos-a-practical-guide-to-west-kconfig-proj-conf/)
    
9. ZephyrRTOS Threads, Work Queues, Message Queues and how we use them, 访问时间为 四月 5, 2026， [https://www.zephyrproject.org/zephyrrtos-threads-work-queues-message-queues-and-how-we-use-them/](https://www.zephyrproject.org/zephyrrtos-threads-work-queues-message-queues-and-how-we-use-them/)
    
10. Getting started with Zephyr RTOS - TSM_AdvEmbSof : Advanced Embedded Software, 访问时间为 四月 5, 2026， [https://advembsof.isc.heia-fr.ch/codelabs/getting-started-zephyr/](https://advembsof.isc.heia-fr.ch/codelabs/getting-started-zephyr/)
    
11. Practical Zephyr - Devicetree basics (Part 3) - Memfault Interrupt, 访问时间为 四月 5, 2026， [https://interrupt.memfault.com/blog/practical_zephyr_dt](https://interrupt.memfault.com/blog/practical_zephyr_dt)
    
12. Set up Zephyr RTOS - Golioth, 访问时间为 四月 5, 2026， [https://docs.golioth.io/getting-started/device-examples/compile-example-code/zephyr/set-up-zephyr/](https://docs.golioth.io/getting-started/device-examples/compile-example-code/zephyr/set-up-zephyr/)
    
13. Getting Started Guide — Zephyr Project Documentation, 访问时间为 四月 5, 2026， [https://docs.zephyrproject.org/latest/develop/getting_started/index.html](https://docs.zephyrproject.org/latest/develop/getting_started/index.html)
    
14. Kconfig - Tips and Best Practices — Zephyr Project Documentation, 访问时间为 四月 5, 2026， [https://docs.zephyrproject.org/latest/build/kconfig/tips.html](https://docs.zephyrproject.org/latest/build/kconfig/tips.html)
    
15. Syntax and structure - Zephyr Documentation, 访问时间为 四月 5, 2026， [https://docs.zephyrproject.org/latest/build/dts/intro-syntax-structure.html](https://docs.zephyrproject.org/latest/build/dts/intro-syntax-structure.html)
    
16. Mastering the Zephyr RTOS Devicetree and Overlays | Beningo, 访问时间为 四月 5, 2026， [https://www.beningo.com/mastering-the-zephyr-rtos-devicetree-and-overlays/](https://www.beningo.com/mastering-the-zephyr-rtos-devicetree-and-overlays/)
    
17. Scheduling — Zephyr Project Documentation, 访问时间为 四月 5, 2026， [https://docs.zephyrproject.org/latest/kernel/services/scheduling/index.html](https://docs.zephyrproject.org/latest/kernel/services/scheduling/index.html)
    
18. Threads - Zephyr Project Documentation, 访问时间为 四月 5, 2026， [https://zephyr-docs.listenai.com/reference/kernel/threads/index.html](https://zephyr-docs.listenai.com/reference/kernel/threads/index.html)
    
19. Two Ways to Create Threads in Zephyr RTOS, 访问时间为 四月 5, 2026， [https://www.hugoshih.com/en/p/two-ways-to-create-threads-in-zephyr-rtos/](https://www.hugoshih.com/en/p/two-ways-to-create-threads-in-zephyr-rtos/)
    
20. Zephyr (OS) Real-Time Scheduling - mnml's vault - Obsidian Publish, 访问时间为 四月 5, 2026， [https://publish.obsidian.md/manuel/Wiki/Programming/Zephyr+(OS)+Real-Time+Scheduling](https://publish.obsidian.md/manuel/Wiki/Programming/Zephyr+\(OS\)+Real-Time+Scheduling)
    
21. Workqueue Threads - Zephyr Documentation, 访问时间为 四月 5, 2026， [https://docs.zephyrproject.org/latest/kernel/services/threads/workqueue.html](https://docs.zephyrproject.org/latest/kernel/services/threads/workqueue.html)
    
22. Using Custom Work Queues for Sensor Readings in Zephyr - The Golioth Developer Blog, 访问时间为 四月 5, 2026， [https://blog.golioth.io/using-custom-work-queues-for-sensor-readings-in-zephyr/](https://blog.golioth.io/using-custom-work-queues-for-sensor-readings-in-zephyr/)
    
23. Queue centric workqueue · Issue #106498 · zephyrproject-rtos/zephyr - GitHub, 访问时间为 四月 5, 2026， [https://github.com/zephyrproject-rtos/zephyr/issues/106498](https://github.com/zephyrproject-rtos/zephyr/issues/106498)
    
24. Devicetree - Zephyr Documentation, 访问时间为 四月 5, 2026， [https://docs.zephyrproject.org/latest/build/dts/index.html](https://docs.zephyrproject.org/latest/build/dts/index.html)
    
25. From Setup to Blinky: Your First Zephyr Project on STM32 - Hackster.io, 访问时间为 四月 5, 2026， [https://www.hackster.io/gkiryaziev/from-setup-to-blinky-your-first-zephyr-project-on-stm32-c5213b](https://www.hackster.io/gkiryaziev/from-setup-to-blinky-your-first-zephyr-project-on-stm32-c5213b)
    
26. Practical Zephyr - Zephyr Basics (Part 1) - Memfault Interrupt, 访问时间为 四月 5, 2026， [https://interrupt.memfault.com/blog/practical_zephyr_basics](https://interrupt.memfault.com/blog/practical_zephyr_basics)
    
27. Recommended first STM mcu for learning the platform and Zephyr OS : r/embedded - Reddit, 访问时间为 四月 5, 2026， [https://www.reddit.com/r/embedded/comments/1hsebal/recommended_first_stm_mcu_for_learning_the/](https://www.reddit.com/r/embedded/comments/1hsebal/recommended_first_stm_mcu_for_learning_the/)
    
28. Security Best Practices · zephyrproject-rtos/zephyr Wiki - GitHub, 访问时间为 四月 5, 2026， [https://github.com/zephyrproject-rtos/zephyr/wiki/Security-Best-Practices](https://github.com/zephyrproject-rtos/zephyr/wiki/Security-Best-Practices)
    
29. Kconfig - Tips and Best Practices - Technical Documentation, 访问时间为 四月 5, 2026， [https://docs.nordicsemi.com/bundle/ncs-1.1.0/page/zephyr/guides/kconfig/index.html](https://docs.nordicsemi.com/bundle/ncs-1.1.0/page/zephyr/guides/kconfig/index.html)
    
30. Zephyr 4.2.0, 访问时间为 四月 5, 2026， [https://docs.zephyrproject.org/latest/releases/release-notes-4.2.html](https://docs.zephyrproject.org/latest/releases/release-notes-4.2.html)
    
31. Retrofitting Zephyr Memory Protection - Linux Foundation, 访问时间为 四月 5, 2026， [https://events19.linuxfoundation.cn/wp-content/uploads/2017/11/Retrofitting-Memory-Protection-in-the-Zephyr-OS_Wayne-Ren-_-Huaqi-Fang.pdf](https://events19.linuxfoundation.cn/wp-content/uploads/2017/11/Retrofitting-Memory-Protection-in-the-Zephyr-OS_Wayne-Ren-_-Huaqi-Fang.pdf)
    
32. User Mode — Zephyr Project Documentation, 访问时间为 四月 5, 2026， [https://docs.zephyrproject.org/latest/kernel/usermode/index.html](https://docs.zephyrproject.org/latest/kernel/usermode/index.html)
    
33. zephyr/doc/kernel/usermode/overview.rst at main - GitHub, 访问时间为 四月 5, 2026， [https://github.com/zephyrproject-rtos/zephyr/blob/main/doc/kernel/usermode/overview.rst](https://github.com/zephyrproject-rtos/zephyr/blob/main/doc/kernel/usermode/overview.rst)
    
34. Memory Protection Design - Zephyr Documentation, 访问时间为 四月 5, 2026， [https://docs.zephyrproject.org/latest/kernel/usermode/memory_domain.html](https://docs.zephyrproject.org/latest/kernel/usermode/memory_domain.html)
    
35. Power Management — Zephyr Project Documentation, 访问时间为 四月 5, 2026， [https://docs.zephyrproject.org/latest/services/pm/index.html](https://docs.zephyrproject.org/latest/services/pm/index.html)
    
36. 3. Getting started on Zephyr RTOS - emlearn documentation, 访问时间为 四月 5, 2026， [https://emlearn.readthedocs.io/en/latest/getting_started_zephyr.html](https://emlearn.readthedocs.io/en/latest/getting_started_zephyr.html)
    
37. Hello World - Zephyr Documentation, 访问时间为 四月 5, 2026， [https://docs.zephyrproject.org/latest/samples/hello_world/README.html](https://docs.zephyrproject.org/latest/samples/hello_world/README.html)
    
38. Logging — Zephyr Project Documentation, 访问时间为 四月 5, 2026， [https://docs.zephyrproject.org/latest/services/logging/index.html](https://docs.zephyrproject.org/latest/services/logging/index.html)
    
39. Getting Started with Zephyr RTOS | Beningo, 访问时间为 四月 5, 2026， [https://www.beningo.com/getting-started-with-zephyr-rtos/](https://www.beningo.com/getting-started-with-zephyr-rtos/)
    

**