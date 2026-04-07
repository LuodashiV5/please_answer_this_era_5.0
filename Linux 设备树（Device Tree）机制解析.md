
**从 DTS 到 Driver Probe，彻底吃透 Linux 板级硬件描述机制，** 先看一个框架图

![Image](https://mmbiz.qpic.cn/sz_mmbiz_jpg/ZwL47XUG5I8xFRvDsRkAoCOBj0plpGSfyshLJOiaicLAuo9Twvh9I2Toc1n6CAJ40n4LGyBvve3Q67PPQRvUQuzKfzRuGMt78GSriavTCjiajGc/640?wx_fmt=jpeg&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)

在 ARM、RISC-V、PowerPC 以及大量国产化 SoC 平台中，Device Tree（设备树）已经成为 Linux BSP、驱动适配和板级 bring-up 的核心基础设施。很多系统研发工程师日常会改 DTS、排查 probe 失败、修 GPIO 和中断，但如果只停留在“会改节点”的层面，遇到 deferred probe、overlay、phandle 依赖、时钟树和多层 dtsi 继承时就很容易陷入低效排查。

我们从 **DTS 源文件 → DTB 装载 → 内核 early boot → OF framework → platform driver probe → 运行时调试** 全链路展开。

第一章：为什么 Linux 需要 Device Tree

## 1.1 从 board file 到硬件描述数据分离

早期 ARM Linux 大量依赖 `mach-xxx/board-xxx.c` ，把 UART 基地址、GPIO 复用、I2C 从设备、PHY 地址、SPI Flash 等板级信息直接写死在内核源码里。这样做的最大问题是：每换一块板子，都要改一次 kernel source 并重新编译，导致 BSP 长期碎片化。Device Tree 的核心价值，就是把板级硬件差异从内核代码中剥离，迁移到外部描述文件，让 kernel image 与具体板型解耦。

```
旧模式：Board 差异 -> board-xxx.c -> 重新编译 kernel新模式：Board 差异 -> DTS/DTB -> 同一 kernel image
```

## 1.2 DTS、DTSI、DTB 的工程分层意义

实际 BSP 很少只用一个 dts，而是 SoC 层、模组层、板级层多层继承：SoC `.dtsi` 提供控制器节点，board `.dts` 负责打开状态和挂具体外设。这样 UART、I2C、PCIe、GMAC 等公共资源在 SoC 层统一维护，板级只覆盖差异部分，显著提升可维护性

```
soc.dtsi|+-- module.dtsi|+-- board.dts
```

```
第二章：设备树的数据结构本质
```

## 2.1 Node：每个节点都是一个硬件对象

设备树本质是树状 node + property 结构。每个 node 对应一个硬件对象，例如 UART 控制器、I2C 总线、GPIO 控制器、以太网 MAC、PCIe Root Complex。节点名通常采用 `name@addr` 形式，其中地址与寄存器基址相关。

```
uart0: serial@ff1a0000 { compatible = "rockchip,rk3568-uart"; reg = ; status = "okay";};
```

流程图：

```
根节点 /|+-- soc|+-- uart+-- i2c+-- spi
```

## 2.2 Property：驱动初始化的静态数据库

驱动 probe 时几乎所有初始化参数都来自 property： `reg` 提供寄存器地址， `interrupts` 提供 IRQ， `clocks` 提供时钟输入， `gpios` 提供控制线， `phy-handle` 建立 MAC 与 PHY 关联。本质上，DTS 就是驱动初始化的静态配置数据库。

```
Property|+-- reg -> ioremap+-- interrupts -> request_irq+-- clocks -> clk_get+-- gpios -> gpiod_get
```

#  第三章：Bootloader 如何把 DTB 交给 Linux

## 3.1 从 U-Boot 到内核 early boot 的传递链路

BootROM 先启动 Bootloader，U-Boot 再加载 kernel image 和 dtb 文件，随后通过 `booti/bootm` 把 dtb 地址传递给内核。ARM64 平台通常通过 `x0` 寄存器传递 FDT 地址，kernel 在 early boot 阶段解析。

```
ROM| vU-Boot|+--> load Image|+--> load xxx.dtb| vbooti| vKernel start
```

## 3.2 FDT 到内核 OF 树对象的展开

DTB 在启动阶段仍是 Flattened Device Tree（二进制扁平结构），内核通过 `early_init_dt_scan` 扫描内存、chosen、bootargs、reserved-memory 等关键节点，随后构建 OF（Open Firmware）节点对象树，为后续 platform device 创建提供基础。

```
DTB blob |vearly_init_dt_scan | vunflatten_device_tree | vstruct device_node tree
```

#  第四章：compatible 如何驱动 probe

## 4.1 从 DT node 到 platform_device

很多人以为 DTS 节点存在，驱动就会自动 probe，实际上中间还隔着完整 OF 创建设备链路。内核通过 `of_platform_populate` 遍历总线节点，把符合条件的 node 转成 `platform_device` 。只有设备对象创建成功，才会进入 driver match。

```
DT node | vof_platform_populate | vplatform_device
```

## 4.2 compatible 匹配驱动的核心机制

驱动中的 `of_match_table` 定义兼容字符串列表，platform bus 在 `platform_match` 中调用 `of_match_device` 完成匹配。一旦 `compatible` 对上，内核就会进入 probe。

```
staticconststruct of_device_id xxx_of_match = { { .compatible = "vendor,dev" }, {}};
```

```
compatible string |vof_match_device | vdriver probe
```

#  第五章：phandle、时钟、中断和 GPIO 依赖

## 5.1 phandle 如何串起硬件依赖关系

设备树强大的地方，不是单节点描述，而是节点间引用关系。 `phandle` 允许一个节点引用另一个节点，例如 MAC 引用 PHY、LCD 引用背光、设备引用 regulator。它本质上是树节点之间的静态依赖图。

```
ethernet node|+--> phy-handle ---> phy node|+--> clocks -------> clk node
```

## 5.2 中断、GPIO、时钟树的传递链路

驱动里常见问题不是 compatible 不匹配，而是依赖资源读取失败。例如：

- `interrupt-parent` 决定 IRQ 域
    
- `clocks` 决定输入时钟源
    
- `reset-gpios` 控制硬件复位
    
- `pinctrl` 决定 IO 复用
    

任何一个 property 缺失，都可能导致 probe defer。

```
probe|+-- parse irq+-- parse clock+-- parse gpio+-- parse pinctrl| vresource ready ?
```

#  第六章：Overlay 与动态设备扩展

## 6.1 为什么 overlay 能动态插入设备

Overlay 的本质是在运行时向现有 OF 树打补丁，新增 node 或修改 property，非常适合 FPGA、可插拔模组、摄像头扩展板等场景。它不是重新加载内核，而是动态修改设备树对象并触发设备创建。

```
base dtb|+-- overlay.dtbo| vmergeto live tree
```

## 6.2 overlay 常见陷阱

overlay 最常见问题包括：target path 错误、phandle 冲突、fragment 覆盖错误、资源重复申请。尤其 GPIO 和 regulator 节点，若 base tree 已被占用，overlay 插入后很容易导致 probe fail 或资源 busy。

```
overlay load| vmerge fragment|+-- success -> create device+-- fail -> rollback
```

#  第七章：驱动调试与线上排障方法

## 7.1 DTS 改了为什么驱动没起来

核心排查顺序：

1. 检查 dtb 是否真正被加载
    
2. 确认 `/proc/device-tree` 节点存在
    
3. 核对 compatible
    
4. 检查 status 是否 `okay`
    
5. 查看 dmesg 是否 deferred probe
    
6. 检查 clocks / irq / gpio 依赖
    

```
节点不存在? -> dtb 未生效节点存在但未 probe? -> compatible / statusprobe defer? -> clock gpio irq dependency
```

## 7.2 工程级高效排障方法论

成熟 BSP 团队不会只靠“改 DTS 试试看”，而是建立标准化排查路径：

- `fdtdump` 校验 dtb
    
- `dtc -I dtb -O dts` 反编译核对
    
- `dmesg | grep OF`
    
- `/sys/firmware/devicetree/base`
    
- `debugfs` 查看 pinctrl / clk
    
- ftrace 跟踪 probe defer
    

最终目标不是会写节点，而是建立 **从 DTS 到驱动资源链路的完整心智模型** 。这样无论是ARM、飞腾、瑞芯微、NXP 还是 Marvell 平台 BSP，效率都会明显提升。

最后设备设备树的

总流程图：

```
DTS -> DTC -> DTB -> U-Boot load -> Kernel early scan -> OF tree -> platform_device -> compatible match -> probe -> parse irq/clk/gpio -> driver ready
```