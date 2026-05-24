
我结合 **软件信号命名、调试文档以及 CLT/ART 标定资料**，把 **Pos_dis3 / Pos_dis4** 和 **MBC 板子的物理通道对应关系**给你整理成一个**可直接用于调试与解释的结论版**，避免只停留在“参数名”层面。

---

## 一句话结论（先给结论）

> **Pos_dis3 / Pos_dis4 是 MBC 上第 3、4 个径向轴承测点，对应后端磁轴承的 RX3 / RY4 两个正交径向位移通道。**

---

## 1️⃣ Pos_disx 在 MBC 里的统一定义逻辑

在 **MBC** 系统中：

- `Pos_disx` = **第 x 路轴承位移闭环残差（displacement deviation）**
- 本质是：  **(实时位移传感器信号 – 对应轴承的参考中心 Pos_refx)**
- 单位：**µm**
- 用于：
    - 悬浮稳定性评估
    - 1/2 级 displacement fault 判定
    - Blackbox / DebugTool 实时监控

这一点在 `User Guide.docx` 和 `MV3S & MV2S Virtual Build CLT.pptx` 中是明确写死的：

> _“In total, there are 5 displacements … Pos_dis1 ~ Pos_dis5”_ [[User Guide | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B6E3EF546-A22B-493F-B7A6-643878BCC22D%7D&file=User%20Guide.docx&action=default&mobileredirect=true&DefaultItemOpen=1), [[MV3S & MV2...Build CLT | PowerPoint]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BC298D224-FC6E-4D70-AA82-CA9C45A6001E%7D&file=MV3S%20%26%20MV2S%20Virtual%20Build%20CLT.pptx&action=edit&mobileredirect=true&DefaultItemOpen=1)

---

## 2️⃣ Pos_dis3 / Pos_dis4 的**轴承 & 物理方向对应**

来自 CLT / ART commissioning 表（工程上最明确）：

|Pos_dis|轴承通道|物理意义|方向|
|---|---|---|---|
|Pos_dis1|RX1|前端径向 1|X|
|Pos_dis2|RY2|前端径向 2|Y|
|✅ **Pos_dis3**|**RX3**|**后端径向 1**|**X**|
|✅ **Pos_dis4**|**RY4**|**后端径向 2**|**Y**|
|Pos_dis5|AZ5|轴向|Z|

该对应关系在 `MV3S & MV2S Virtual Build CLT.pptx` 中以 **测试判据表**形式出现（不是文字描述，更可靠）：

> _Pos_dis3 RX3 ±20um_  
> _Pos_dis4 RY4 ±20um_ [[MV3S & MV2...Build CLT | PowerPoint]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BC298D224-FC6E-4D70-AA82-CA9C45A6001E%7D&file=MV3S%20%26%20MV2S%20Virtual%20Build%20CLT.pptx&action=edit&mobileredirect=true&DefaultItemOpen=1)

---

## 3️⃣ 在 MBC 板层面的“信号链”关系（你关心的点）

以 **Pos_dis3 / RX3** 为例：

```
位移传感器 RX31 / RX32
        ↓（差分调理）
Adaptor Board（接口板）
        ↓
MBC ADC
        ↓
Pos_V3（原始电压量）
        ↓
软件标定 & 滤波
        ↓
Pos_ref3（中心参考）
        ↓
✅ Pos_dis3 = Pos_V3 – Pos_ref3
```

Pos_dis4 完全同理，只是通道换成 **RY4（RY41 / RY42）**。

> 这一点在 MBC 位置故障 Blackbox CSV 头字段中可以直接看到  
> `Pos_dis1 ... Pos_dis5` 与 `ia3/ic3, ia4/ic4` 成组出现 [[MBC positi...2.36pm_3.6 | Excel]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B5FF25E1F-EA52-45D6-A50E-AA33562FDDF7%7D&file=MBC%20position%20fault_2.36pm_3.6.csv&action=default&mobileredirect=true&DefaultItemOpen=1)

---

## 4️⃣ 调试/故障定位时如何“用对” Pos_dis3 / 4（实践角度）

结合你们常见问题，给你几个**工程习惯级别的判断法**：

### ✅ 悬浮正常时

- Pos_dis3、Pos_dis4：
    - 均值 ≈ 0
    - 波动 ±10 ~ ±20 µm
    - RX / RY 相位相差约 90°（轴心轨迹是椭圆）

### ⚠ 若只有 Pos_dis3 异常

- 重点排查：
    - RX3 单侧传感器（RX31 / RX32）
    - 接插件 / adaptor 板 S4 档位
    - 后轴承 X 向电磁线圈（ia3 / ic3）

### ⚠ Pos_dis3+4 同时大

- 更可能是：
    - 后端轴承整体跑偏
    - 悬浮中心（Pos_ref3/4）错误
    - 校准未写入（mode=267 漏执行）

---

## 5️⃣ 和你最近讨论过的问题的直接关联

你前面提到过👇

> _“MBC 有哪些保护会令得轴承掉落？”_

这里直接点名：
- **1/2 级 displacement fault**   , 用的就是 **Pos_dis1~5**, RX3 / RY4 属于**后轴承判据的一半**
- 后轴承振动、温漂、局部短路 ,首先体现在 **Pos_dis3 / Pos_dis4 的包络拉大**

---

## ✅ 总结一句能写进规范里的话

> **Pos_dis3 / Pos_dis4 分别对应 MBC 后端径向轴承的 X / Y 位移偏差量（RX3 / RY4），是后轴承悬浮稳定性和 displacement fault 的核心观测信号。**

如果你需要，我可以进一步帮你做三件事之一（不占你时间）： 1️⃣ 画一张 **“Pos_dis ↔ 传感器 ↔ 线圈 ↔ MBC ADC” 对照图**  
2️⃣ 给 FAE 用的 **Pos_dis 异常快速判断表**  
3️⃣ 帮你把这段内容整理进 **MBC 调试 / Sustaining 指南模板**

你选哪一个？