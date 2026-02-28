|          |     |     |     |     |
| -------- | --- | --- | --- | --- |
| **本期栏目** |     |     |     |     |
| 网站       | 工具  | 教程  | 算法  | 项目  |

哈喽，我是专注开发**AI机电设备的铨能君。**后台问得最多的一个问题是：老板安排任务给他，要自己开发一个机械臂，**该从哪个开源项目入门？这里我整理了一些，它们提供了丰富的资源、工具和社区支持，大家根据自己的需求来收藏使用吧。**

---

## 01、AR4（Annin Robotics）：桌面级“工业风”6轴开源臂，资料最成体系

```
https://anninrobotics.com/downloads/
```

从机械/电气到控制软件一整套都给得很全：控制应用（含 Python 源码）、Teensy/Arduino 固件、3D 打印 STL、装配手册等一站式下载。

![Image](https://mmbiz.qpic.cn/mmbiz_png/FWeGULlrFyDSqTsSgp1qzFicuv5QjpYmvAxaeAqqSswwEKd8W9J2iaocMQCwHggD2Zr1cWpFP8kicWIC1QG0xQROA/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=6)

---

## 02、Dummy-Robot（稚晖君）：超迷你但全栈硬核，学习与二开价值拉满

```
https://github.com/peng-zhihui/Dummy-Robot
```

核心固件/算法结构讲得很细，适合照着学“机械臂控制与工程组织方式”；同时仓库内容覆盖固件、算法、驱动等关键模块。

![Image](https://mmbiz.qpic.cn/mmbiz_png/FWeGULlrFyDSqTsSgp1qzFicuv5QjpYmvhpZbUnyqibGPOQsOzdPbu6ic02efIx7Q2hAC1UeXU1nNGSvgQWKeCrKw/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=7)

---

## 03、Niryo One：3D 打印 6 轴开源协作臂（教学/实验室常用生态）

```
STL结构件仓库：https://github.com/NiryoRobotics/niryo_one
```

官方直接开源可打印 STL，并提供完整 ROS 软件栈（GPLv3），非常适合教学与快速验证。

![Image](https://mmbiz.qpic.cn/mmbiz_png/FWeGULlrFyDSqTsSgp1qzFicuv5QjpYmvh0CeqGFMPMsOaU18wtEBYZ6w2mQKJoUSzmERXfPPVbEWb1haVHb2bg/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=8)

---

## 04、BCN3D Moveo：经典开源 3D 打印机械臂（CAD/STL/BOM/固件齐全）

```
https://github.com/BCN3D/BCN3D-Moveo
```

仓库明确包含 CAD、STL、BOM、固件以及用户/装配手册，属于“可复现度非常高”的老牌项目。

![Image](https://mmbiz.qpic.cn/mmbiz_png/FWeGULlrFyDSqTsSgp1qzFicuv5QjpYmv846XTYia0eqhRt52jly6ViaWjmVHm093PbDibj4bkRY9xG93wcQK9q07A/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=9)

---

## 05、PAROL6：高性能 3D 打印桌面 6 轴（带 GUI / Python API / ROS2 仿真）

```
https://github.com/PCrnjak/PAROL6-Desktop-robot-arm
```

定位更偏“桌面工业路线”：控制软件、GUI、STL 开源，并提供 BOM、装配说明、Python API、ROS2/MoveIt 仿真入口。

![Image](https://mmbiz.qpic.cn/mmbiz_png/FWeGULlrFyDSqTsSgp1qzFicuv5QjpYmvAVhkiaUHV2W9Vy5DiavIsq3tLX3WhEKmZV3dB9xzvZ3wvC5mHwbkfmYA/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=10)

---

## 06、SO-ARM100 ：标准开源机械臂（含 STEP/STL/仿真/可接 LeRobot）

```
https://github.com/TheRobotStudio/SO-ARM100
```

仓库包含 STEP/STL/Simulation 等目录，并给出“按 BOM 自配 + 3D 打印 + 装配指南”的完整路线；设计目标就是做“开放、可复现、可做 AI 数据采集/训练”的标准臂。

![Image](https://mmbiz.qpic.cn/mmbiz_png/FWeGULlrFyDSqTsSgp1qzFicuv5QjpYmveSsGicQWKHLchBGMubsaFdlUVkbu2s66maxFdXG7VfFUTr54gNWuBUw/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=11)

---

## 07、MeArm：口袋级 4DOF 开源机械臂（低成本、教育友好）

```
https://github.com/MeArm/MeArm
```

经典入门项目：仓库提供切割文件与装配说明，定位就是低成本、可复现、适合 STEAM 教学与快速动手。

![Image](https://mmbiz.qpic.cn/mmbiz_png/FWeGULlrFyDSqTsSgp1qzFicuv5QjpYmvmClhZEEtTNSP1rf62MmkXSvq5uoZUYc2OxQ95rAunUibsVxqY5Lkpug/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=12)

---

如果你只想先收藏两个“最值”的标杆，我建议就从下面这俩开始：**AR4**（更偏“桌面工业协作臂平台”）和 **Dummy-Robot**（更偏“硬核全栈作品范本”）。如果你想做“更像工业机械臂的桌面平台”（规划+仿真+控制链路完整），选 AR4。但你想系统学习“一个完整机械臂项目如何产品化落地”（硬件+固件+上位机+工具链），选 Dummy。桌面级机械臂这块，**AR4** 和 **Dummy-Robot** 真的是两个完全不同的“顶级路线”：一个偏“工程平台”，一个偏“全栈作品”。你只要把其中一个吃透，后面再上协作臂/工业臂的学习曲线都会顺很多。下一篇我可以写成「Dummy**从0到1开发教程**」或「**Dummy视觉抓取二次开发教程**」，你们更想先看哪个？

|   |   |   |
|---|---|---|
|![Image](https://mmbiz.qpic.cn/mmbiz_jpg/FWeGULlrFyDSqTsSgp1qzFicuv5QjpYmv9Aib1VpkbdIugQ659txkAHliaML9vxibEsKzc4xQBaAHWpFgyQufKL0wg/640?wx_fmt=jpeg&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=13)|![Image](https://mmbiz.qpic.cn/mmbiz_jpg/FWeGULlrFyDSqTsSgp1qzFicuv5QjpYmvWxRQVDzAc4jeeibjMARUDmtibOZWa9ichTOPGW78YSBWdsfqzc9nZOIQg/640?wx_fmt=jpeg&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=14)|![Image](https://mmbiz.qpic.cn/mmbiz_jpg/FWeGULlrFyDSqTsSgp1qzFicuv5QjpYmvQvfSZcDbsntmqjWUglf3sLWNdPc6yWD9Vm03ibQEJgKhWia29jFc6oPw/640?wx_fmt=jpeg&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=15)|

  

**注意**：本文为个人学习记录与使用体验分享，仅供交流参考。文中信息可能随版本更新而变化，请以官网/项目文档为准；由此产生的任何操作风险与后果请自行评估与承担。