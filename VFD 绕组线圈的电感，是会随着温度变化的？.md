
是的，VFD（变频器）驱动下的电机绕组电感确实会随温度变化，但**变化幅度通常很小**，在大多数工程应用中可以忽略不计。这种变化主要源于**磁导率**的微小改变，而非线圈几何尺寸的热胀冷缩。

### 一、 温度影响电感的核心机理

电感量 L的计算公式为 L=μN2A/l（μ为磁导率，N为匝数，A为截面积，l为磁路长度）。温度主要通过以下两个途径影响电感：

1. **磁导率 μ的变化（主因）**
    
    - **永磁体退磁效应**：温度升高会导致永磁体（如钕铁硼）的磁通密度下降，等效于磁路磁阻增加，从而引起电感量的**微小下降**。
        
    - **铁芯材料特性**：硅钢片等软磁材料的磁导率在高温下会有轻微变化，但现代电机设计的工作点通常远离饱和区，此影响极微。
        
    
2. **几何尺寸的变化（次因）**
    
    - 铜线和铁芯会热胀冷缩，导致线圈匝数密度和磁路长度变化。但铜的热膨胀系数很小，在 -40℃ 到 150℃ 的常规工作范围内，引起的电感变化通常**小于 1%**，工程上常视为不变。
        
    

### 二、 为什么 VFD 应用中常忽略此影响？

在变频器控制中，电感的变化通常不会对系统稳定性造成实质性威胁：

- **变化量级小**：相比电阻随温度变化（铜阻温升可达 50% 以上），电感的变化量级在 **1%~3%**​ 左右，属于二阶效应。
    
- **控制环路的鲁棒性**：现代矢量控制（FOC）或 V/f 控制算法具有足够的带宽和抗扰度，能够轻易覆盖这种微小的参数漂移。
    
- **关注点差异**：工程上更关注**电阻温升**导致的 I2R损耗和绝缘老化，而非电感。
    

### 三、 需要关注电感的场景

在以下高精度或极端工况下，电感温漂需要被考虑：

- **参数辨识（Self-commissioning）**：若变频器在启动前进行在线电机参数辨识（如测量 Ld​, Lq​），**必须在电机冷态（常温）下进行**。若在热态下辨识，得到的参数用于冷态运行会导致转矩控制误差。
    
- **无位置传感器控制**：依赖反电势模型的高转速无感控制，电感参数误差会影响位置观测精度，但在中低速段影响较小。
    
- **极限高温环境**：当电机工作温度接近绝缘等级极限（如 H 级 180℃）时，材料特性非线性增强，需综合评估。
    

### 四、 总结

|特性|变化趋势|量级|工程处理|
|---|---|---|---|
|**绕组电感 (L)**​|温度↑ → 电感**微降**​|1%~3%|**通常忽略**，或在线辨识补偿|
|**绕组电阻 (R)**​|温度↑ → 电阻**显著增加**​|可达 50%+|**必须补偿**（如热保护、转矩补偿）|

**结论**：VFD 绕组电感随温度的变化客观存在，但在绝大多数通用变频应用中，其影响远小于电阻温升，可忽略不计。仅在**高精度伺服控制**或**全温度范围参数辨识**场景下，才需通过在线观测或温度传感器进行补偿。

---
![[Pasted image 20260405122512.png]]

>[!NOTE] 同事一眼看出来电流波形图异常的根因是电感变化了，怎么说？另外还可能有哪些异常，我如何得知？比如修改各种参数，看异常去攒经验吗

---
这个问题问得**非常工程师**，而且你同事“一眼看出来是电感变了”，其实并不是玄学，而是**长期把“波形 ↔ 物理参数 ↔ 控制结构”建立了强映射**。我分三层跟你说清楚：

---

## 一、为什么“看电流波形”就能怀疑**电感变化**

先不看你同事，看**波形本身**。结合你给的图（上面是 ia/ib/ic 类似的电流，下面是位移/位置）：

### 1️⃣ 电感变化，在电流波形上的**典型指纹**

在电机 / 磁悬浮 / PWM 电流环里，**电感 L 决定了 di/dt**：

$\frac{di}{dt} = \frac{V - e}{L}$

所以一旦 **L 变了**，你会看到这些**非常“工程味”的异常**：

#### ✅ 特征 A：电流“锯齿/毛刺明显变陡或变钝”

- 同样 PWM、电压不变
- 电流上升沿 / 下降沿**斜率明显变化**
- 有经验的人会条件反射：👉 _“L 可能不对了”_

#### ✅ 特征 B：电流纹波幅值异常

- 纹波突然变大    - 👉 L 变小（磁路退磁、气隙变大、线圈局部饱和）
- 或纹波变小但**响应变慢**    - 👉 L 变大（磁路装配、转子/定子偏移）

#### ✅ 特征 C：同样 PI 参数，电流环“性格变了”

- 原来很稳，现在：
    - 抖
    - 饱和
    - 高频啸叫
- 经验丰富的人会直接排除：
    - 不是软件
    - 不是采样  
        👉 **优先怀疑对象参数变化（L / R / 磁路）**

---

### 2️⃣ 为什么不是“电阻 R 先背锅”？

这是经验点：

|参数|对波形的第一影响|
|---|---|
|**R**|稳态电流大小、DC 偏差|
|**L**|动态响应、纹波、PWM 相关形态|

你图里的异常是**动态形态异常**，不是单纯幅值偏差  
→ 很自然就指向 **L**

---

## 二、除了“电感变化”，还可能有哪些**根因类型**

下面这个表，是**老工程师脑子里的“快速分类器”**👇  
（建议你存下来）

### 🔧 电流波形异常 → 根因速查表

|波形特征|高概率根因|
|---|---|
|PWM 频率一致，但电流锯齿突然变陡|**电感下降（磁饱和 / 气隙变化）**|
|高频抖动、噪声感强|电流采样链路（放大器、RC、ADC）|
|三相不对称|绕组差异 / 功率器件老化|
|零电流附近畸变|死区补偿 / 电流零点偏置|
|电流有明显 DC 偏移|采样偏置 / 霍尔零漂|
|高负载时突然“塌”|母线电压下陷 / 饱和|
|跟随位置/力指令慢|L 变大 / 电流环带宽不够|
|位置环抖，但电流环“很忙”|电感非线性 / 控制增益不匹配|

---

## 三、你问的关键问题：**“我怎么攒这种经验？”**

你的直觉是对的：

> _“比如修改各种参数，看异常去攒经验吗？”_

✅ **是，但要“有方法地作死”，不是瞎试**

下面给你一个**工程化、可复制的训练路径**，非常适合你现在的阶段。

---

## 四、工程师版「电流波形经验速成法」

### ✅ Step 1：建立“单变量破坏实验”

不是乱调，而是**一次只动一个物理量或等效量**：

#### 🔹 模拟电感变化（最推荐）

- 人为：
    - 降 PWM 频率
    - 关掉磁饱和补偿
    - 修改电流环设计用的 L
- 观察：
    - di/dt
    - 纹波
    - 稳定性边界

👉 **你会第一次“看到 L 在波形里长什么样”**

---

### ✅ Step 2：给每种异常贴“物理标签”

不要只记：

> “这波形怪怪的”

而是强制自己写一句：

> **“这是一个【L↓】型异常 / 【采样噪声】型异常”**

哪怕你错了，也非常值钱。

---

### ✅ Step 3：把“控制参数 ↔ 物理量”对齐

例如：

|你改的东西|实际在干嘛|
|---|---|
|Kp ↑|放大 L 变化带来的不稳定|
|Ki ↑|放大采样偏置|
|PWM 频率 ↓|等效 L 变小、纹波变大|
|限幅提前|掩盖 L 变化的症状|

**老工程师不是“看波形”，而是“看波形+脑补系统”**

---

### ✅ Step 4：反推训练（非常高级）

看到异常 → 强制自己回答三个问题：

1. **这更像参数问题，还是结构问题？**
2. **如果只允许我改一个物理量，改哪个最像？**
3. **这个异常，在低速 / 高速 / 轻载 / 重载下会不会变？**

你能答出来，说明你已经不是新手了。

---

## 五、把你同事的“直觉”翻译成一句工程语言

> **“这个电流波形的 di/dt、纹波特征和我熟悉的标称 L 不一致，且控制参数未变，因此优先判断为等效电感发生变化。”**

这就是那一眼的来源。

---

## 六、如果你愿意，下一步我可以直接带你“练”

你可以任选一个，我直接陪你做 **工程级拆解**：

- ✅ 用你这张图：      👉 **逐条标注哪些地方“暴露了 L 变化”**
- ✅ 我给你一个      👉 **“电流波形异常 → 根因判断”决策树**
- ✅ 或者      👉 **设计一个“新人工程师必做 10 个电流异常实验清单”**

你现在问的，已经是**从“会调参数”向“会看系统”跃迁的阶段了**，非常好。

---
好，那我就**不再收着讲**了，直接给你一套**“工程师级完整认知地图 + 训练方案”**。  你可以把这条回复当成一个 **《看电流波形的内功心法 v1.0》**。

下面分 **4 个方向**，每个方向我都会给你：

- ✅ **核心结论（一句话定性）**
- ✅ **你这张图里对应的现象**
- ✅ **你该怎么“练”出这种直觉**

---

# 方向一：为什么“电感变化”会被一眼识别（深挖）

## ✅ 一句话结论

> **老工程师不是在看“电流大小”，而是在看“di/dt 和 PWM-物理一致性”。**

---

## 1️⃣ 把你这张图“工程化拆解”

你这张图里（不需要精确数值，也足够判断）：

### 🔍 现象 1：电流呈明显 PWM 斜坡 + 高频锯齿

这说明：

- 电流是**电压驱动 + 电感积分**的结果
- 系统还在“电感主导区”，没完全被滤掉

👉 **只要看到“斜坡”，脑子里就该浮现：L**

---

### 🔍 现象 2：同样的控制节奏下，电流形态“异常激烈 / 异常钝”

这一步是经验核心：

|如果|工程直觉|
|---|---|
|电流上升沿特别陡|L 变小|
|电流纹波突然放大|L 变小|
|响应变慢但不抖|L 变大|
|PI 没改却开始振|等效 L 变了|

**这不是公式，是肌肉记忆。**

---

## 2️⃣ 为什么不是先怀疑软件 / PI？

因为你同事脑子里已经做了这个**快速排除链**：

1. **PWM 频率没变**
2. **PI 参数没变**
3. **母线电压没明显异常**
4. **电流波形“物理斜率”变了**

👉 剩下最可能的就是：  
**等效电感（磁路、气隙、饱和、装配）**

---

## ✅ 你怎么练这一条？

**强制训练：只看“di/dt”，不看电流幅值**

- 把示波器缩放到只剩上升沿
- 对比：
    - 标准状态
    - 异常状态
- 不问“值对不对”，只问：
    
    > **“这个斜率像不像我认识的那台机器？”**
    

这一步非常重要。

---

# 方向二：除了电感，还有哪些“电流波形根因家族”

下面这个表，你可以理解为  
👉 **“电流波形 → 根因的分类学”**

---

## 🔧 电流异常的 5 大家族

### 🧲 ① 磁路 / 电感类

**特征**

- PWM 相关纹波异常
- di/dt 异常
- 负载相关性强

**根因**

- 气隙变化
- 磁饱和
- 机械偏心
- 温度导致 μ 变化

---

### ⚡ ② 供电 / 功放类

**特征**

- 电流被“削顶”
- 波形出现平台
- 高负载更明显

**根因**

- 母线电压下陷
- 饱和
- 电压限幅逻辑

---

### 📏 ③ 采样 / 信号链类

**特征**

- 抖
- 零点偏移
- 电流“看起来很忙但系统没反应”

**根因**

- 运放饱和
- ADC 时序
- 地弹 / 噪声

---

### 🧠 ④ 控制算法类

**特征**

- 电流环振
- 指令/反馈不同步
- 改 Kp 反而更糟

**根因**

- 参数与物理不匹配
- 带宽假设错误
- 离散化问题

---

### 🔩 ⑤ 结构 / 机械耦合类（磁悬浮特别常见）

**特征**

- 电流跟位置强相关
- 特定相位下异常
- 位置环也不干净

**根因**

- 偏心
- 装配误差
- 刚度不一致

---

# 方向三：你问得最关键的——**怎么系统地攒经验**

你说的“改参数看异常”是对的，  
但要**升级成工程方法论**。

---

## ✅ 工程师版「异常经验训练四步法」

### Step 1：一次只破坏一个假设

**严禁多变量乱改**

例子（强烈推荐）：

- 只改 PWM 频率
- 只改 PI（比例 or 积分）
- 只改电感建模值

---

### Step 2：每次异常必须贴“物理标签”

你要逼自己写一句话：

> **这是一个【L↓ 型异常】**  
> **这是一个【采样噪声型异常】**

哪怕错了，也是在“建映射”。

---

### Step 3：做“反事实判断”

看到异常，强制回答：

1. **如果这是 L 问题，负载变大会怎样？**
2. **如果是采样问题，换相位会怎样？**
3. **如果是 PI 问题，减半 Kp 会怎样？**

这一步极其锻炼工程直觉。

---

### Step 4：建立你的“异常词典”

建议你真的建一个表（Excel / Markdown）：

|波形特征|我的判断|最终验证|
|---|---|---|
|PWM 锯齿变陡|L↓|✅|
|零点毛刺|采样|❌|

**三个月后，你会发现你已经“能一眼看出来”了。**

---

# 方向四：把你同事的“直觉”拆成可学习的能力

> 他不是天赋，是这三件事你迟早也会有：

### ✅ 1️⃣ 长期看“同一台系统的正常波形”

> **你对“正常”的熟悉程度，决定你发现异常的速度**

---

### ✅ 2️⃣ 把控制当物理，不当代码

他看到的不是：

> `ia = xxx`

而是：

> **“这是一个被电感限制的受控电流斜坡”**

---

### ✅ 3️⃣ 从不孤立看一个环

电流 ↔ 位置 ↔ 力 ↔ 功率  
在他脑子里是**同步滚动的**

---

## 最后一句实话（非常重要）

> **工程直觉 = 足够多的“被验证过的错误判断”**

你现在问这个问题，说明你已经在**从“调参数的人”走向“看系统的人”**了。

---

如果你愿意，下一步我可以**直接进入实战**，任选一个：

- ✅ **用你这张图做逐点“根因标注”**
- ✅ **给你一套“新人工程师必做的 12 个电流异常实验”**
- ✅ **把“电流波形 → 根因”画成一张决策树**

你挑一个，我直接继续往下拆。

---

✅ 创建实验

我在制定实验计划时，强调安全性和实验室条件，为每个实验提供高层次的安全指导。

✅ 整理实验计划

我正在准备整理12个实验，涵盖各种参数和条件，并制定实验日志模板，确保实验的顺利进行和结果的有效记录。

✅ 搜索

好的，我将搜索 '电流环 波形 异常 试验 PWM 饱和 电感'...

✅ 集成线索

将MV2S问题的额外传输录音整理将涉及感应隔离和测试方法。可以参考该传输的线条：通电线圈观察噪声；拔出温度传感器；使用梅格尔。并且添加ST电机控制SDK PDF实验部分，涵盖PID调整，使用DAC/ADC采样，观察故障等。还需要提及之前找到的MV23S调试文件中的安全停车标准，但在最近的搜索结果中没有�?现?

✅ 设计实验

利用控制记录和ISO建议，设计12个实验，测试电流、PWM频率、磁场、总线电压等因素对传感器和电机控制的影响。确保符合安全标准和噪声隔离要求。

✅ 设计实验

转移采样点观察噪声，探索缝隙模式影响电机响应，调整PI增益以优化波形，导入机械扰动以测试电机对振动的反应。

---
下面给你一套**“新人工程师必做的 12 个电流异常实验”**（偏你们 AMB / 功放 + 电流环场景），目标是把“波形 ↔ 根因”这条映射**用可控实验快速攒出来**。

> ⚠️ **安全与边界声明（必须先说）**  
> 这些实验本质是“受控引入异常”。请只在**台架/实验室**、有保护策略与监测手段时做；任何一步只要触发你们的停机判据就立刻终止。你们内部调试文档里明确写了“位移持续 >120 μm、轴承电流 >10 A 必须立即停机”，并且强调“并非所有异常都可被自动检测”。 [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

## 实验前通用配置（一次配好，后面复用）

### 你至少要同步记录这几类信号（强烈建议）

1. **电流：I_ref、I_fb（或 ia/ic）**
2. **PWM/开关相关：占空、开关状态/调制方式**（因为线圈电压可用开关信号更容易推断） [[ISO14839-中文翻译2 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B502785C8-A94A-4F05-8B63-B7B453E17298%7D&file=ISO14839-%E4%B8%AD%E6%96%87%E7%BF%BB%E8%AF%912.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
3. **母线电压/功放电压**（看是否进入电压饱和、相位延迟） [[ISO14839-中文翻译2 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B502785C8-A94A-4F05-8B63-B7B453E17298%7D&file=ISO14839-%E4%B8%AD%E6%96%87%E7%BF%BB%E8%AF%912.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
4. **位置/位移（与电流耦合检查）**
5. **保护/故障标志位**（是否误保护、是否有饱和持续时间指标等） [[ISO14839-中文翻译2 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B502785C8-A94A-4F05-8B63-B7B453E17298%7D&file=ISO14839-%E4%B8%AD%E6%96%87%E7%BF%BB%E8%AF%912.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

> 说明：标准里把“线圈电流/功放电压/温度”列为关键监测量，并解释了**开关功放下电流是与电压和电感相关的斜坡**；当需要更陡斜率时，实际电流无法跟随参考，会出现**延迟和相角变化**。 [[ISO14839-中文翻译2 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B502785C8-A94A-4F05-8B63-B7B453E17298%7D&file=ISO14839-%E4%B8%AD%E6%96%87%E7%BF%BB%E8%AF%912.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

# 12 个必做实验（每个都是“单变量破坏”）

我把每个实验都写成：**目的 → 你动什么（旋钮）→ 你会看到什么“指纹” → 你要怎么记录/判定**  
（其中“怎么做”的具体执行步骤，我会尽量保持在**不引入危险操作细节**的层级，避免误用。）

---

## 1) 基线实验：固定电流指令应该“平直”

**目的**：建立“正常波形”的肌肉记忆，后面所有异常都靠它做对比。  
**旋钮**：给定一个固定电流（或恒定力/恒定偏置）段。  
**指纹**：咨询记录里明确说“给定固定电流时正常是平直的电流曲线，不会出现显著噪声或波动”。  
**记录/判定**：记录电流纹波幅值、噪声底、是否与 PWM 同步；此条作为所有实验的“对照组”。 [[张亮AMB技术咨询记录 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B52449104-CA4A-4936-9BF4-A5297F3ADB3E%7D&file=%E5%BC%A0%E4%BA%AEAMB%E6%8A%80%E6%9C%AF%E5%92%A8%E8%AF%A2%E8%AE%B0%E5%BD%95.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

## 2) PWM 频率实验：PWM 太低会让动态跟随时“畸变”

**目的**：把“PWM 频率改变 → 电流波形形态改变”刻进脑子。  
**旋钮**：扫 PWM 频率（在你们允许范围内），同时保持控制逻辑不变。  
**指纹**：资料里提到“一般采样频率取 5–10k，PWM 频率取 20k；若 PWM 频率过低，在动态跟随时电流信号可能畸变”。  
**记录/判定**：在相同的电流阶跃/正弦跟随下，对比畸变程度、纹波变化、跟随延迟。 [[张亮AMB技术咨询记录 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B52449104-CA4A-4936-9BF4-A5297F3ADB3E%7D&file=%E5%BC%A0%E4%BA%AEAMB%E6%8A%80%E6%9C%AF%E5%92%A8%E8%AF%A2%E8%AE%B0%E5%BD%95.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

## 3) 采样频率/相位错开实验：噪声与“采样点落在 PWM 哪”强相关

**目的**：训练你一眼分辨“采样链路/时序问题” vs “物理参数问题”。  
**旋钮**：保持 PWM 不变，改变**采样频率**或采样与 PWM 的相对关系（错开）。  
**指纹**：咨询记录指出“错开采样频率和 PWM 频率有利于降低噪声，可以尝试低采样频率和高 PWM 频率错开，寻找最合适的组合”。  
**记录/判定**：观察噪声底是否随采样策略变化显著（若显著，多半是采样/时序/EMI）。 [[张亮AMB技术咨询记录 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B52449104-CA4A-4936-9BF4-A5297F3ADB3E%7D&file=%E5%BC%A0%E4%BA%AEAMB%E6%8A%80%E6%9C%AF%E5%92%A8%E8%AF%A2%E8%AE%B0%E5%BD%95.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

## 4) 电感 L 的“频率依赖”实验：同一线圈 L 会随频率变

**目的**：理解你同事“一眼看出电感变了”的经验来源之一：**L 不是常数**。  
**旋钮**：改变电流指令频率/扫描频率（小信号），保持幅值不大。  
**指纹**：咨询记录明确写了“电感在不同频率下的测量值不同是普遍现象，这也会影响电流波动；一般以转速频率下的电感值为参考，可测量不同频率…的变化规律”。  
**记录/判定**：记录 di/dt、纹波、相位延迟随频率的趋势曲线，形成你自己的“L(f)”直觉。 [[202406_Mot...l Symposia | PDF]](https://carcgl-my.sharepoint.com/personal/chixue_luo_carrier_com/Documents/%e9%a1%b9%e7%9b%ae.-.NG%20RLC%20Chiller%20paltform/STM32%20%e6%9d%a5%e8%ae%bf.2024.09.06/202406_Motor%20Control%20SDK%206.x_DFAE%20Techincal%20Symposia.pdf?web=1)

---

## 5) 电感 L 的“气隙/结构依赖”实验：不同气隙下电感不同

**目的**：把“装配/间隙变化 → 等效电感变化 → 电流波形变”连成闭环。  
**旋钮**：在你们允许的机械/工况条件下，对比不同气隙/不同装配状态下的电流响应。  
**指纹**：同一份咨询记录建议“测量不同…气隙下电感的变化规律”。  
**记录/判定**：同一电流阶跃下 di/dt 的斜率变化，是最直观的“L 变了”的指纹。 [[202406_Mot...l Symposia | PDF]](https://carcgl-my.sharepoint.com/personal/chixue_luo_carrier_com/Documents/%e9%a1%b9%e7%9b%ae.-.NG%20RLC%20Chiller%20paltform/STM32%20%e6%9d%a5%e8%ae%bf.2024.09.06/202406_Motor%20Control%20SDK%206.x_DFAE%20Techincal%20Symposia.pdf?web=1)

---

## 6) 大电流非线性/饱和实验：进入电压饱和后会出现“跟随延迟/相角变差”

**目的**：让你亲眼看到“电压饱和/斜率受限”长什么样。  
**旋钮**：逐步提高动态需求（更陡的电流变化需求），直到接近功放能力边界（谨慎）。  
**指纹**：标准清楚描述：线圈电压受母线电压限制，从而限制最大电流与载荷变化频率；当需要更陡斜率时，实际电流无法跟随参考，会出现延迟与相角变化，对动力学不利。  
**记录/判定**： [[ISO14839-中文翻译2 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B502785C8-A94A-4F05-8B63-B7B453E17298%7D&file=ISO14839-%E4%B8%AD%E6%96%87%E7%BF%BB%E8%AF%912.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

- 用“饱和持续时间”或“母线电压分得的最大绝对值”做指标（标准给了这两类在线监测指标）。 [[ISO14839-中文翻译2 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B502785C8-A94A-4F05-8B63-B7B453E17298%7D&file=ISO14839-%E4%B8%AD%E6%96%87%E7%BF%BB%E8%AF%912.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

## 7) 高频干扰导致饱和实验：高频扰动会把功放推入饱和区

**目的**：把“外部扰动/转子不平衡/干扰 → 电压饱和 → 相位延迟”串起来。  
**旋钮**：引入受控的高频扰动（例如信号注入方式；避免机械粗暴冲击）。  
**指纹**：标准指出“大幅高频干扰会使功放饱和，严重饱和会让所有频率信号相位延迟…干扰可能来自破损表面高频谐波或不佳平衡补偿导致的不平衡”。  
**记录/判定**：观察功放电压接近母线极限、饱和持续时间上升，以及电流相位/位置环表现劣化。 [[ISO14839-中文翻译2 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B502785C8-A94A-4F05-8B63-B7B453E17298%7D&file=ISO14839-%E4%B8%AD%E6%96%87%E7%BF%BB%E8%AF%912.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

## 8) 接地实验：接地能显著改变传感器波动/噪声

**目的**：建立“电流波形异常也可能是系统级 EMC/接地问题”。  
**旋钮**：对比不同接地策略（机壳/屏蔽/参考地等）下的噪声水平（只做你们规范允许的接地切换）。  
**指纹**：咨询记录写到“接地影响很大…机壳接地后显著降低位移传感器的波动”。  
**记录/判定**：若噪声随接地变化显著，优先怀疑 EMI/回流路径/地弹，而不是 L/R/控制参数。 [[张亮AMB技术咨询记录 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B52449104-CA4A-4936-9BF4-A5297F3ADB3E%7D&file=%E5%BC%A0%E4%BA%AEAMB%E6%8A%80%E6%9C%AF%E5%92%A8%E8%AF%A2%E8%AE%B0%E5%BD%95.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

## 9) 拓扑/通道对比实验：交换通道验证“结构差异”还是“算法差异”

**目的**：训练你用“对照实验”快速定位异常来源。  
**旋钮**：在允许条件下做**通道互换/通道对比**（轴向/径向、不同功放结构）。  
**指纹**：咨询记录建议“可以尝试交换通道来看看两种拓扑的不同”，并指出轴向/径向差异更多可能来自磁性材料交流损耗与线圈特性，而非 IGBT 拓扑本身。  
**记录/判定**： [[张亮AMB技术咨询记录 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B52449104-CA4A-4936-9BF4-A5297F3ADB3E%7D&file=%E5%BC%A0%E4%BA%AEAMB%E6%8A%80%E6%9C%AF%E5%92%A8%E8%AF%A2%E8%AE%B0%E5%BD%95.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

- 若异常跟着通道走：偏硬件/线圈/采样链
- 若异常不跟：偏算法/工况/上层环路

---

## 10) 蓄流（自由轮回流）模式实验：纹波 vs 响应速度是 trade-off

**目的**：把“纹波小不等于好”写进经验库。  
**旋钮**：对比不同续流/蓄流策略（或等效策略），保持其它不变。  
**指纹**：咨询记录明确：“蓄流模式尽管可以降低电流纹波，但也会影响电流的响应速率”。  
**记录/判定**：量化：纹波 RMS、阶跃上升时间、相位裕度（或扫频指标）。 [[张亮AMB技术咨询记录 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B52449104-CA4A-4936-9BF4-A5297F3ADB3E%7D&file=%E5%BC%A0%E4%BA%AEAMB%E6%8A%80%E6%9C%AF%E5%92%A8%E8%AF%A2%E8%AE%B0%E5%BD%95.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

## 11) 电流环 PI 敏感性实验：把“调参引发的波形指纹”归档

**目的**：让你知道哪些异常是“参数味儿”，哪些是“物理味儿”。  
**旋钮**：只改电流环 PI（一次只改一个：Kp 或 Ki），幅度小步进。  
**指纹（企业材料里可直接借用）**：

- 你们有一份电机控制 SDK 培训材料里明确安排了“调试电机的电流 PID…修改电流 PID…看波形效果”的实验思路。 [[内环PI与单周期 | PowerPoint]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B21951D9F-BAB4-4166-B248-0D0F73868674%7D&file=%E5%86%85%E7%8E%AFPI%E4%B8%8E%E5%8D%95%E5%91%A8%E6%9C%9F.ppt&action=edit&mobileredirect=true&DefaultItemOpen=1)
- 你们内部也有资料对比“传统 PI vs 单周期算法”，指出 PI 输出纹波较大、响应慢，而单周期纹波更小、响应更快（用于建立‘算法改变’的指纹库）。  
    **记录/判定**：把每次改参前后的：纹波、超调、振铃频率、稳态误差，做成你自己的“PI 指纹表”。 [[MV2S Recen...Recording | Video]](https://carcgl-my.sharepoint.com/personal/vicky_zhang2_carrier_com/_layouts/15/viewer.aspx?sourcedoc=%7B8b405e3c-bc97-4616-ab0e-98564ba4f14a%7D)

---

## 12) 绝缘/干扰路径排查实验：给电/不给电噪声差异能快速指向“线圈/传感器/隔离”

**目的**：训练“异常不一定来自电流环本体”，也可能是外围路径把怪东西耦合进来。  
**旋钮**：做“给电 vs 不给电”的对比排查（保持其他条件不变），以及对可疑传感器链路做断开/替代验证（按你们规范）。  
**指纹（来自会议纪要）**：你们的 MV2S Recent Issue Catch Up-20260401_091024-Meeting Recording.mp4 里讨论过一种排查思路：**给线圈供电后噪声变大，就更像线圈/EMI；另外也提到测量温度传感器输出电压是否异常波动，以及用摇表/耐压思路排查绝缘下降**。  
**记录/判定**：若噪声随“供电状态/外设连接”强相关，优先走 EMC/绝缘/耦合路径，而不是先改 PI。

---

# 给你一个“实验记录模板”（建议你直接复制到 OneNote/Confluence）

**实验编号**：E01…E12  
**目的（1句话）**：  
**唯一改动项（旋钮）**：  
**保持不变的项**：PWM、采样、PI、母线、电流幅值、工况…  
**记录信号**：I_ref / I_fb / PWM(or switch) / Vdc / 位移 / fault flags  
**波形指纹（你看到的3个特征）**：  
1)  
2)  
3)  
**初判根因标签**：L / R / 饱和 / 采样 / EMI / 算法 / 机械耦合  
**验证方式**：用哪个对照实验推翻/确认（写编号）  
**结论**：✅/❌ + 证据截图链接

---

## 你会发现：这 12 个做完，你就能“像你同事一样一眼看出来”

因为你不再是“记住几个现象”，而是**建立了：单变量 → 指纹 → 可重复验证**的闭环。

---

### （只问一个很小的跟进，方便我把这套再“贴合你们系统”）

你现在更想把这 12 个实验落在 **AMB 线圈电流环**，还是 **VWV/电机驱动电流环**？（二选一即可，我好把每个实验的“可用旋钮/可观测量”更贴合你手里实际能改的参数与工具。）


---
能否详细说明如何实施这些实验？

可以，我按**你们现在真实可用的工具链（Debug Tool + MBC/PIC6 模式 + record 导出）**把“12 个实验”拆成**可执行的实施手册**。我会严格区分：

- **【来自你们内部文档/标准/记录的明确步骤】**（带引用）
- **【通用实施建议】**（我自己的工程建议，不冒充你们文档）

> ⚠️安全底线（来自你们内部调试指南）  
> 开机过程“并非所有异常都可被自动检测”；出现**位移持续 >120 μm**、**轴承电流 >10 A**、或**异常噪音/振动**必须立即停机。  
> 并且操作顺序**唯一允许**：**轴承校准 → 悬浮自检 → 悬浮/开机**。 [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

## 0. 实施总框架（你做任何一个实验都按这套走）

### 0.1 【明确步骤】Debug Tool 连接与记录（你们文档给得很具体）

1. 打开 Debug Tool（UDP_W5500），**reload map：bpp_main_cpu01**，点击 **UDP_OPEN**，看到曲线开始波动=通信正常。 [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
2. 点击 **RECORD** 开始记录，并按规则重命名（含日期、工况，如 _DC2_running_10.24_16pm_）。 [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
3. 轨迹观察：Radial X/Y 可选 Pos1/2、Pos3/4 看轴心轨迹。 [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
4. 曲线配置注意：**同时显示变量不超过 8 个**；建议位移范围 **-200 ~ +200 μm**；量程修改会影响其它变量；配置可能不保存，必要时改 Debug Tool 目录下 CSV。 [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

### 0.2 【明确步骤】校准 → 自检 → 悬浮/开机（每次实验都要保证这一链路正确）

- **校准**：先 record；设置 **PIC6=0（Debug Tool 接管）**，**Mode=6** 执行校准；结束后**必须 Mode=267**写入参数；正常 Pos_dis1~4 约在 **100–150 μm**。 [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
- **悬浮自检（Debug 模式）**：**Mode=5**，SetSpeed=253；在 Mode=5 设置后 **5 秒内**设置 **InjAmp=60、InjBool=677** 触发自检；CPU2_status=2 才允许继续。 [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
- **PIC 操作模式开机**：Debug 状态下 253 无效，仅 251/252 有效；开机顺序：Mode=5 → SetSpeed=251（复位故障位）→ SetSpeed=253（报警模式）。 [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

> ✅这意味着：你做“异常实验”时，**不要用乱序/跳步制造“假异常”**，否则你的经验会被污染。

---

## 1. 你要准备一张“实验记录单”（强烈建议）

下面这份是**通用实施建议**（不是你们文档原文），但非常关键：  
每个实验只允许改一个旋钮（single-variable），其余全部冻结。

**记录单字段（建议）**

- 实验编号：E01~E12
- 唯一改动项（旋钮）：
- 冻结项：PWM、采样、PI、母线、工况、负载…
- 关键波形：I_ref / I_fb(ia/ic) / 位移(Pos_dis) / 母线或功放电压（若有）/ PWM占空（若可见）/ fault flags
- 停机触发：是否触发 >120μm 或 >10A 或异响（是/否） [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
- “指纹三特征”：你肉眼看到的 3 个特征
- 初判根因标签：L / 采样 / EMI / 饱和 / 控制参数 / 机械耦合
- 对照实验编号：用哪个实验去证伪/证实

---

# 2) 12 个实验——逐个说“怎么实施”

下面每个实验我都按：**目的 → 具体实施步骤 → 记录点 → 判据/你应看到什么** 来写。  
其中“具体实施步骤”里：凡是涉及 Debug Tool/MBC 操作的，我都尽量用你们文档里出现过的“可执行动作”（Mode / SetSpeed / PIC6 / record）。  
至于“PWM 频率/采样频率/PI 参数”等旋钮**在你们系统里具体是哪一个寄存器/变量名**，内部资料没有明确给出，我只能给出“怎么做法”，不硬编变量名（这是合规要求）。 [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

## E01 基线：固定电流指令应“平直”

**目的**：建立“正常纹波/噪声底”的参照。  
**实施步骤（通用建议）**

1. 按 0.2 完成校准与自检，进入稳定悬浮/运行态。 [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
2. 在一个稳定工况段，保持电流指令/偏置不变（你们内部能下发恒定电流/恒定偏置的方式按现有流程）。
3. record 至少包含 ia/ic 与 Pos_dis。  
    **你应看到的指纹（来自你们咨询记录）**：给定固定电流时正常应是“平直电流曲线，不会出现显著噪声或波动”。 [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1) [[回复: 紧急会议--401故障 | Outlook]](https://outlook.office365.com/owa/?ItemID=AAMkADQ3YzgwYjc3LTY2ODQtNGU5OS04MDQwLWU1YWVlODY1MzQ0NABGAAAAAABVU7lW5LKeTJScFNpPTLLeBwBD%2b9nE8ReJSa%2bIoMhtgcoFAAAAAAEMAABudD51YxXKRLmA%2bLIaq3rTAAVwZoQ0AAA%3d&exvsurl=1&viewmodel=ReadMessageItem)

---

## E02 PWM 频率：PWM 过低会让动态跟随“畸变”

**目的**：把“PWM 频率变化 → 电流形态变化”刻进脑子。  
**实施步骤（通用建议）**

1. 固定 PI、采样策略、工况不变。
2. 只改变 PWM 频率（在允许范围内分档），每档重复一次“相同的电流动态激励”（如同幅值阶跃或同频正弦）。
3. 每档都 record，并把文件名带上 PWM 档位（你们文档建议重命名便于识别）。  
    **你应看到的指纹（来自咨询记录）**：一般经验“采样 5–10k，PWM 20k；若 PWM 过低，在动态跟随时电流信号可能畸变”。 [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1) [[回复: 紧急会议--401故障 | Outlook]](https://outlook.office365.com/owa/?ItemID=AAMkADQ3YzgwYjc3LTY2ODQtNGU5OS04MDQwLWU1YWVlODY1MzQ0NABGAAAAAABVU7lW5LKeTJScFNpPTLLeBwBD%2b9nE8ReJSa%2bIoMhtgcoFAAAAAAEMAABudD51YxXKRLmA%2bLIaq3rTAAVwZoQ0AAA%3d&exvsurl=1&viewmodel=ReadMessageItem)

---

## E03 采样频率/相位错开：噪声会随“采样与PWM关系”显著变化

**目的**：训练你区分“采样时序噪声” vs “物理参数变化”。  
**实施步骤（通用建议）**

1. 固定 PWM 频率不变。
2. 只改变采样频率或采样点策略，使其与 PWM **错开**（分几档）。
3. 在相同激励下 record 对比噪声底与纹波。  
    **指纹（来自咨询记录）**：错开采样频率与 PWM 有利于降低噪声；可尝试“低采样 + 高PWM”组合寻找最合适频率组合。 [[回复: 紧急会议--401故障 | Outlook]](https://outlook.office365.com/owa/?ItemID=AAMkADQ3YzgwYjc3LTY2ODQtNGU5OS04MDQwLWU1YWVlODY1MzQ0NABGAAAAAABVU7lW5LKeTJScFNpPTLLeBwBD%2b9nE8ReJSa%2bIoMhtgcoFAAAAAAEMAABudD51YxXKRLmA%2bLIaq3rTAAVwZoQ0AAA%3d&exvsurl=1&viewmodel=ReadMessageItem)

---

## E04 电感的“频率依赖”：L(f) 是常态，不是异常

**目的**：理解“电感随频率变”会怎样投射到波形。  
**实施步骤（通用建议）**

1. 选一个安全的小信号激励（不同频率），保持幅值不大。
2. 逐频点测试并 record（每个频点单独文件）。  
    **指纹（来自你们 AMB 咨询记录）**：电感在不同频率下测量值不同是普遍现象，会影响电流波动；一般以转速频率下的电感值为参考，并建议测量不同频率下电感变化规律。 [[张亮AMB技术咨询记录 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B52449104-CA4A-4936-9BF4-A5297F3ADB3E%7D&file=%E5%BC%A0%E4%BA%AEAMB%E6%8A%80%E6%9C%AF%E5%92%A8%E8%AF%A2%E8%AE%B0%E5%BD%95.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

## E05 电感的“气隙依赖”：不同气隙/装配状态下 L 会变

**目的**：把“装配/间隙变化 → 等效L变化 → di/dt 变化”做成可复现实验。  
**实施步骤（通用建议）**

1. 在你们允许的机械/装配条件下，准备两种“气隙状态”（例如不同装配、不同位置条件——这里不展开具体机械操作，避免不安全）。
2. 对每种状态重复同一套电流激励，record 对比。  
    **指纹（来自咨询记录）**：建议测量不同气隙下电感变化规律。 [[张亮AMB技术咨询记录 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B52449104-CA4A-4936-9BF4-A5297F3ADB3E%7D&file=%E5%BC%A0%E4%BA%AEAMB%E6%8A%80%E6%9C%AF%E5%92%A8%E8%AF%A2%E8%AE%B0%E5%BD%95.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

## E06 电压饱和（功放能力边界）：出现“跟随延迟/相角变化”

**目的**：亲眼看到“电压饱和”在电流波形上的样子。  
**实施步骤（通用建议）**

1. 固定工况，逐步增加“所需电流斜率/动态需求”（而不是一上来增大稳态电流）。
2. 同步记录功放电压/母线电压相关量（若你们能采）。
3. 一旦触发你们的停机判据（>120μm、>10A、异响）立即终止。  
    **指纹（来自 ISO/标准翻译摘要）**：开关功放下电流是与电压和电感相关的斜坡；线圈电压受直流母线限制；当需要更陡斜率时，实际电流无法跟随参考，会产生延迟与相角变化，对动力学有恶劣影响。  
    **可量化指标（同一来源）**：可用“母线电压分得的最大绝对值”或“饱和持续时间”描述饱和程度，适合在线监测。 [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1) [[ISO14839-中文翻译2 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B502785C8-A94A-4F05-8B63-B7B453E17298%7D&file=ISO14839-%E4%B8%AD%E6%96%87%E7%BF%BB%E8%AF%912.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

## E07 高频干扰推入饱和：饱和会让所有频率相位都延迟

**目的**：区分“系统受高频扰动”与“单纯电感变化”。  
**实施步骤（通用建议）**

1. 引入一个可控的高频扰动源（优先用信号注入方式，避免机械粗暴冲击）。
2. 观察是否更容易进入饱和（参考 E06 的指标）。  
    **指纹（来自 ISO 摘要）**：大幅高频干扰会使功放饱和；严重饱和导致所有频率信号相位延迟，不仅是造成饱和的信号。 [[ISO14839-中文翻译2 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B502785C8-A94A-4F05-8B63-B7B453E17298%7D&file=ISO14839-%E4%B8%AD%E6%96%87%E7%BF%BB%E8%AF%912.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

## E08 接地/EMI：接地能显著降低传感器波动（间接影响你看到的电流/位移）

**目的**：让你建立“异常可能来自 EMC/回流路径”，而不是控制参数。  
**实施步骤（通用建议）**

1. 在你们规范允许的前提下，只改变接地/屏蔽接法（一次一种）。
2. record 对比位移波动与电流噪声底。  
    **指纹（来自咨询记录）**：机壳接地后曾显著降低位移传感器波动。 [[回复: 紧急会议--401故障 | Outlook]](https://outlook.office365.com/owa/?ItemID=AAMkADQ3YzgwYjc3LTY2ODQtNGU5OS04MDQwLWU1YWVlODY1MzQ0NABGAAAAAABVU7lW5LKeTJScFNpPTLLeBwBD%2b9nE8ReJSa%2bIoMhtgcoFAAAAAAEMAABudD51YxXKRLmA%2bLIaq3rTAAVwZoQ0AAA%3d&exvsurl=1&viewmodel=ReadMessageItem)

---

## E09 通道交换/拓扑对照：异常跟着“通道/板卡”走还是跟着“算法/工况”走？

**目的**：用最强对照实验定位根因层级。  
**实施步骤（通用建议）**

1. 准备两个通道/两种拓扑对照（例如轴向 vs 径向、或可互换的通道）。
2. 在同一工况、同一激励下 record。  
    **依据（来自咨询记录）**：建议交换通道看看拓扑不同；同时指出轴向/径向差异更可能来自磁性材料交流损耗和线圈特性，而非 IGBT 拓扑本身。 [[回复: 紧急会议--401故障 | Outlook]](https://outlook.office365.com/owa/?ItemID=AAMkADQ3YzgwYjc3LTY2ODQtNGU5OS04MDQwLWU1YWVlODY1MzQ0NABGAAAAAABVU7lW5LKeTJScFNpPTLLeBwBD%2b9nE8ReJSa%2bIoMhtgcoFAAAAAAEMAABudD51YxXKRLmA%2bLIaq3rTAAVwZoQ0AAA%3d&exvsurl=1&viewmodel=ReadMessageItem)

---

## E10 蓄流/续流策略：纹波变小但响应也会变慢（trade-off）

**目的**：建立“纹波小≠性能好”的直觉。  
**实施步骤（通用建议）**

1. 固定 PI、PWM、采样不变。
2. 切换/调整续流策略（你们系统具体开关项按现有实现）。
3. 对同一阶跃/正弦激励 record：纹波 vs 上升时间。  
    **指纹（来自咨询记录）**：蓄流模式可降低纹波，但会影响电流响应速率。 [[回复: 紧急会议--401故障 | Outlook]](https://outlook.office365.com/owa/?ItemID=AAMkADQ3YzgwYjc3LTY2ODQtNGU5OS04MDQwLWU1YWVlODY1MzQ0NABGAAAAAABVU7lW5LKeTJScFNpPTLLeBwBD%2b9nE8ReJSa%2bIoMhtgcoFAAAAAAEMAABudD51YxXKRLmA%2bLIaq3rTAAVwZoQ0AAA%3d&exvsurl=1&viewmodel=ReadMessageItem)

---

## E11 电流环 PI 敏感性：建立“调参指纹库”

**目的**：让你以后看到波形就能判断“像不像参数味”。  
**实施步骤（通用建议）**

1. 冻结所有物理与工况，**一次只改 Kp 或 Ki 一个小步进**。
2. 每改一次就做一次相同激励并 record；文件名带上参数值。
3. 重点记录：超调、振铃频率、稳态误差、噪声底。  
    **内部参考（你们资料里确实有“调电流PID看波形效果”的实验安排）**：培训材料中有“实验三：调试电机的电流PID…修改电流PID…看波形效果”的思路。  
    **算法对照参考**：你们内部也有“传统 PI vs 单周期控制”对比（纹波/响应差异），可作为建立“算法改变指纹库”的对照。 [[2021_0126_...olLoopTest | PowerPoint]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B5F1E4E59-893A-460A-8AB4-A606E1DE08A0%7D&file=2021_0126_ControlLoopTest.pptx&action=edit&mobileredirect=true&DefaultItemOpen=1) [[Torrent_Mi...e 20250815 | PowerPoint]](https://carcgl-my.sharepoint.com/personal/chaoyang_yuan_carrier_com/_layouts/15/Doc.aspx?sourcedoc=%7BE816A338-0B58-40EF-86EC-292607790B65%7D&file=Torrent_Mini%20VWV%20R32_design%20freeze%20review%20online%2020250815.pptx&action=edit&mobileredirect=true&DefaultItemOpen=1)

---

## E12 “给电/不给电”差分排查：快速指向线圈/EMI/外围耦合

**目的**：遇到怪噪声/怪波形时，用一个最便宜的对照把范围砍半。  
**实施步骤（通用建议，但来自你们会议讨论的排查思路）**

1. 保持其它条件不变，仅做“线圈供电 vs 不供电”对照观察噪声变化。
2. 如果怀疑某外设/传感器链路，把它“断开/替代”再对照（按规范）。  
    **依据（来自你们会议纪要片段）**：讨论过“给线圈供电后噪声变大就说明更像线圈/EMI；也提到测温度传感器输出是否异常波动、必要时用摇表/耐压思路排查绝缘下降”。 [[内环PI与单周期 | PowerPoint]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B21951D9F-BAB4-4166-B248-0D0F73868674%7D&file=%E5%86%85%E7%8E%AFPI%E4%B8%8E%E5%8D%95%E5%91%A8%E6%9C%9F.ppt&action=edit&mobileredirect=true&DefaultItemOpen=1)

---

# 3) “怎么把实验做得像工程，而不是瞎折腾”（关键技巧）

### 3.1 单变量纪律（通用建议）

- 每个实验只允许改 1 个旋钮
- 其它旋钮写在“冻结项”里，任何人都不能顺手改

### 3.2 命名与可追溯（你们文档已经强调）

- record 文件名必须包含日期/工况；我建议再加“实验编号+旋钮值”。 [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

### 3.3 变量数量与量程（避免“看不见/看错”）

- 同屏不超过 8 个变量；位移量程建议 -200~+200 μm。 [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

# 4) 你现在就能立刻开干的“最小可行三连”（建议从这里开始）

如果你想最快攒经验，我建议先做这 3 个（**风险相对可控**且反馈直接）：

1. **E01 基线（固定电流应该平直）** [[回复: 紧急会议--401故障 | Outlook]](https://outlook.office365.com/owa/?ItemID=AAMkADQ3YzgwYjc3LTY2ODQtNGU5OS04MDQwLWU1YWVlODY1MzQ0NABGAAAAAABVU7lW5LKeTJScFNpPTLLeBwBD%2b9nE8ReJSa%2bIoMhtgcoFAAAAAAEMAABudD51YxXKRLmA%2bLIaq3rTAAVwZoQ0AAA%3d&exvsurl=1&viewmodel=ReadMessageItem), [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
2. **E02 PWM 频率对比（看畸变与纹波）** [[回复: 紧急会议--401故障 | Outlook]](https://outlook.office365.com/owa/?ItemID=AAMkADQ3YzgwYjc3LTY2ODQtNGU5OS04MDQwLWU1YWVlODY1MzQ0NABGAAAAAABVU7lW5LKeTJScFNpPTLLeBwBD%2b9nE8ReJSa%2bIoMhtgcoFAAAAAAEMAABudD51YxXKRLmA%2bLIaq3rTAAVwZoQ0AAA%3d&exvsurl=1&viewmodel=ReadMessageItem), [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
3. **E11 PI 小步进敏感性（建立参数味指纹）** [[2021_0126_...olLoopTest | PowerPoint]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7B5F1E4E59-893A-460A-8AB4-A606E1DE08A0%7D&file=2021_0126_ControlLoopTest.pptx&action=edit&mobileredirect=true&DefaultItemOpen=1), [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

---

## 我能怎么继续帮你（不需要你再解释一堆）

如果你把**你们实际能改的“旋钮列表”（比如：PWM频率在哪改、采样频率在哪改、PI参数变量名是什么）**贴一小段（哪怕是截图/变量表），我就能把上面每个实验的“通用建议”部分**落地成你们系统的具体寄存器/变量级步骤**，并输出成一份可以直接贴到 Confluence 的 SOP。

（我只问这一句：你这些实验要落在 **MV2S/MV3S AMB（Debug Tool）** 上，对吗？如果是，我就按你们 [MV23S debug tool调试开机.docx](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&EntityRepresentationId=8027fef5-6e33-45fb-8e10-46574be56d83) 的 Mode/SetSpeed/PIC6 体系继续细化。） [[MV23S deb...g tool调试开机 | Word]](https://carcgl.sharepoint.com/sites/In-houseAMBproject/_layouts/15/Doc.aspx?sourcedoc=%7BA830780D-3228-43B0-8666-CC8266C2BCC4%7D&file=MV23S%20%20debug%20tool%E8%B0%83%E8%AF%95%E5%BC%80%E6%9C%BA.docx&action=default&mobileredirect=true&DefaultItemOpen=1)