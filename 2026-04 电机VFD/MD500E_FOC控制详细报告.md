# MD500E FOC控制详细分析报告

## 目录
1. [FOC控制概述](#1-foc控制概述)
2. [FOC控制架构](#2-foc控制架构)
3. [核心数据结构](#3-核心数据结构)
4. [坐标变换](#4-坐标变换)
5. [PI控制器](#5-pi控制器)
6. [SVPWM算法](#6-svpwm算法)
7. [弱磁控制](#7-弱磁控制)
8. [同步机解耦控制](#8-同步机解耦控制)
9. [速度环控制](#9-速度环控制)
10. [电流环控制](#10-电流环控制)
11. [磁极位置检测](#11-磁极位置检测)
12. [编码器Z信号处理](#12-编码器z信号处理)
13. [FOC控制流程图](#13-foc控制流程图)
14. [关键参数计算](#14-关键参数计算)

---

## 1. FOC控制概述

### 1.1 FOC（磁场定向控制）简介

FOC（Field Oriented Control）磁场定向控制是一种高性能的电机控制方法，通过将三相交流电流转换到旋转坐标系中，实现对电机转矩和磁通的独立控制。

### 1.2 MD500E FOC控制特点

- **支持多种电机**: 异步机(IM)、永磁同步机(PMSM)
- **多种控制模式**: 
  - FVC（带编码器矢量控制）
  - SVC（无传感器矢量控制）
- **高性能特性**:
  - 电流环PI控制（32位精度）
  - 速度环自适应PI
  - 弱磁控制
  - 解耦控制
  - MTPA（最大转矩电流比）控制

### 1.3 FOC相关文件

| 文件路径 | 功能描述 |
|----------|----------|
| `02_motor/vc/MotorPmsmMain.c` | 同步机FOC控制主程序 |
| `02_motor/vc/MotorVCMain.c` | 矢量控制主程序（速度环、电流环） |
| `02_motor/pwm/Svpwm1.c` | SVPWM空间矢量调制 |
| `02_motor/pwm/MotorPWM.c` | PWM输出和死区补偿 |
| `02_motor/encoder/MotorEncoder.c` | 编码器处理 |

---

## 2. FOC控制架构

### 2.1 系统架构图

```
┌─────────────────────────────────────────────────────────────────┐
│                      FOC控制系统架构                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────────┐      ┌──────────────┐      ┌──────────────┐ │
│  │  速度给定     │      │  速度环ASR   │      │  电流环ACR   │ │
│  │ FreqSet ─────┼──────┼──────> IqRef │──────┼──────> Vq    │ │
│  └──────────────┘      └──────────────┘      └──────────────┘ │
│         │                       │                       │      │
│         │                       │                       │      │
│         ▼                       ▼                       ▼      │
│  ┌──────────────┐      ┌──────────────┐      ┌──────────────┐ │
│  │ 速度反馈     │      │   IdRef      │      │   Vd        │ │
│  │ SpeedFeed    │      │ (弱磁控制)    │      │ (解耦补偿)   │ │
│  └──────────────┘      └──────────────┘      └──────────────┘ │
│         │                       │                       │      │
│         │                       ▼                       ▼      │
│         │              ┌──────────────┐      ┌──────────────┐ │
│         │              │   电流反馈    │      │  反Park变换  │ │
│         └──────────────┼────── Id,Iq  │◀─────┼──── Vα,Vβ   │ │
│                        └──────────────┘      └──────────────┘ │
│                                 │                       │      │
│                                 ▼                       ▼      │
│                        ┌──────────────┐      ┌──────────────┐ │
│                        │  Clarke/Park │      │   SVPWM      │ │
│                        │  变换         │      │   调制       │ │
│                        └──────────────┘      └──────────────┘ │
│                                 │                       │      │
│                                 ▼                       ▼      │
│                        ┌──────────────────────────────┐       │
│                        │      PWM输出 (U,V,W)         │       │
│                        └──────────────────────────────┘       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### 2.2 控制环结构

```
                    ┌────────────────────────────────────────┐
                    │           速度环 (ASR)                  │
                    │  输入: FreqSet - SpeedFeed              │
                    │  输出: IqRef (转矩电流给定)              │
                    │  周期: 0.5ms                           │
                    └────────────────────┬───────────────────┘
                                         │
                                         ▼
                    ┌────────────────────────────────────────┐
                    │           电流环 (ACR)                  │
                    │  Id环: IdRef - Id → Vd                  │
                    │  Iq环: IqRef - Iq → Vq                  │
                    │  周期: ~30us (ADC中断)                  │
                    └────────────────────┬───────────────────┘
                                         │
                                         ▼
                    ┌────────────────────────────────────────┐
                    │         SVPWM调制                       │
                    │  输入: Vd, Vq                          │
                    │  输出: Ta, Tb, Tc (占空比)             │
                    │  周期: ~30us                           │
                    └────────────────────┬───────────────────┘
                                         │
                                         ▼
                    ┌────────────────────────────────────────┐
                    │         PWM输出模块                    │
                    │  三相PWM: EPWM1/2/3                    │
                    │  死区补偿                               │
                    └────────────────────────────────────────┘
```

---

## 3. 核心数据结构

### 3.1 电流环数据结构

```c
// 32位精度PID控制器（用于电流环）
typedef struct {
    int32 KP;              // 比例系数 (Q24格式)
    int32 KI;              // 积分系数 (Q24格式)
    int32 Err;             // 误差
    int32 Integral;        // 积分累加
    int32 Out;             // 输出
    int32 OutMax;         // 输出上限
    int32 OutMin;         // 输出下限
} PID32_STRUCT;

// Id电流环 (励磁电流环)
extern PID32_STRUCT gImAcrQ24;

// Iq电流环 (转矩电流环)
extern PID32_STRUCT gItAcrQ24;
```

### 3.2 速度环数据结构

```c
// 速度环结构体
typedef struct ASR_STRUCT_DEF {
    PID_STRUCT Asr;               // PID参数
    s32 FreqSet;                  // 设定频率 (0.01Hz)
    s32 FreqFeed;                 // 反馈频率
    s32 FreqFeedFilter;           // 滤波反馈频率
    s32 Total;                    // 积分累加
    s32 Out;                      // 输出
    s16 Kp;                       // 当前使用的Kp
    s16 Ki;                       // 当前使用的Ki
    s16 KPHigh;                   // 高频段Kp
    s16 KPLow;                    // 低频段Kp
    s16 KIHigh;                   // 高频段Ki
    s16 KILow;                    // 低频段Ki
    s16 KPZero;                   // 零伺服Kp
    s16 KIZero;                   // 零伺服Ki
    Uint SwitchHigh;              // 高频切换点
    Uint SwitchLow;               // 低频切换点
    Uint SwitchZero;              // 零伺服切换点
    Uint Mode;                    // 模式: 0=普通, 1=零伺服
    s16 PosTorqueLimit;          // 正向转矩限制
    s16 NegTorqueLimit;          // 反向转矩限制
    s16 TorqueLimit;             // 转矩限制
} ASR_STRUCT;

// 全局速度环
extern ASR_STRUCT gAsr;
```

### 3.3 MT轴系电流数据结构

```c
// MT坐标系电流结构 (Q24格式)
typedef struct MT_STRUCT_Q24 {
    s32 M;            // 励磁电流 (Id)
    s32 T;            // 转矩电流 (Iq)
} MT_STRUCT_Q24;

// 设定电流
extern MT_STRUCT_Q24 gIMTSet;

// 实际应用电流
extern MT_STRUCT_Q24 gIMTSetApply;

// MT坐标系电压
typedef struct MT_STRUCT {
    s16 M;            // 励磁电压 (Vd)
    s16 T;            // 转矩电压 (Vq)
} MT_STRUCT;

// 设定电压
extern MT_STRUCT gUMTSet;
```

### 3.4 输出电压结构

```c
// 极坐标电压结构
typedef struct AMPTHETA_STRUCT {
    Uint Amp;             // 电压幅值
    Uint Theta;           // 电压角度
    Uint ThetaOld;        // 上次电压角度
} AMPTHETA_STRUCT;

// 输出电压结构
typedef struct OUT_VOLT_STRUCT {
    s16 VoltApply;           // 实际输出电压
    s16 VoltFilter;          // 滤波后电压
    s16 VoltPhaseApply;      // 电压相位
    s16 VoltPhaseApply1;    // 电压相位1
    s16 VoltPhaseApply2;    // 电压相位2
    s16 LimitOutVoltPer;    // 限制电压百分比
    s16 MaxOutVoltPer;      // 最大输出电压百分比
} OUT_VOLT_STRUCT;

extern OUT_VOLT_STRUCT gOutVolt;
```

### 3.5 PWM输出结构

```c
// PWM输出结构
typedef struct PWM_OUT_STRUCT {
    Uint gPWMPrdApply;       // PWM周期
    Uint gPWMPrd;           // PWM周期(原始)
    s16 PwmDutyA;          // A相占空比
    s16 PwmDutyB;          // B相占空比
    s16 PwmDutyC;          // C相占空比
    s16 ModulationIndex;   // 调制指数
    s16 Sector;            // 扇区
    Uint USet;             // U相设置值
    Uint VSet;             // V相设置值
    Uint WSet;             // W相设置值
    Uint PWMMode;          // PWM模式
    Uint Ratio;            // 调制比
} PWM_OUT_STRUCT;

extern PWM_OUT_STRUCT gPWM;
```

### 3.6 同步机弱磁结构

```c
// PMSM弱磁控制结构
typedef struct PMSM_FLUX_WEAK_STRUCT {
    s16 IdMax;              // Id最大负值
    s16 FreqMax;           // 弱磁最大频率
    s16 SalientRate;       // 凸极率 (Lq/Ld)
    s16 SalientRateCoff;   // 凸极率系数
    s16 AdId;              // Id调整值
    s32 AdIdIntg;          // Id积分
    s16 AdFreq;            // 频率调整值
    s32 AdFreqIntg;        // 频率积分
    s16 IdForTorq;         // 转矩电流
    s16 IqLpf;             // Iq滤波值
    s16 VoltLpf;           // 电压滤波值
    s16 PmsmMaxTorqCtrlEnable; // 最大转矩控制使能
    s16 IdMixAdjFlag;     // Id混合调整标志
    s16 FluxWeakFlag;     // 弱磁标志
} PMSM_FLUX_WEAK_STRUCT;

extern PMSM_FLUX_WEAK_STRUCT gPmFluxWeak;
```

---

## 4. 坐标变换

### 4.1 Clarke变换 (3s/2s)

**功能**: 将三相静止坐标系(ABC)转换为两相静止坐标系(αβ)

**变换公式**:
```
| Iα |   |  1      0       1   | | Iu |
| Iβ | = | -1/2   √3/2    1   | | Iv |
| I0 |   | -1/2  -√3/2    1   | | Iw |
```

**简化公式** (假设三相电流平衡, Iu+Iv+Iw=0):
```
Iα = Iu
Iβ = (Iv - Iw) / √3 = (2*Iv + Iu) / √3
```

**代码实现** (在ADC中断中):
```c
// 电流采样 (在ADC中断中)
int32 Iu = AdcResult.ADCRESULT0;  // A相电流
int32 Iv = AdcResult.ADCRESULT1;  // B相电流
int32 Iw = AdcResult.ADCRESULT2;  // C相电流

// Clarke变换 (简化形式)
// Iα = Iu
// Iβ = (2*Iv + Iu) / √3 ≈ (2*Iv + Iu) * 5787
int32 Ialpha = Iu;
int32 Ibeta = (2*Iv + Iu) * 5787;  // 5787 = 1/√3 (Q15格式)
```

### 4.2 Park变换 (2s/2r)

**功能**: 将两相静止坐标系(αβ)转换为两相旋转坐标系(dq)

**变换公式**:
```
| Id |   |  cosθ   sinθ | | Iα |
| Iq | = | -sinθ   cosθ | | Iβ |
```

**代码实现**:
```c
// Park变换
// 输入: Ialpha, Ibeta (两相静止坐标)
//       Theta (转子角度)
// 输出: Id, Iq (两相旋转坐标)

int32 cosTheta = _IQcos(Theta);
int32 sinTheta = _IQsin(Theta);

// Id = Iα*cosθ + Iβ*sinθ
int32 Id = _IQmpy(Ialpha, cosTheta) + _IQmpy(Ibeta, sinTheta);

// Iq = -Iα*sinθ + Iβ*cosθ
int32 Iq = _IQmpy(-Ialpha, sinTheta) + _IQmpy(Ibeta, cosTheta);
```

### 4.3 反Park变换 (2r/2s)

**功能**: 将两相旋转坐标系(dq)转换回两相静止坐标系(αβ)

**变换公式**:
```
| Vα |   |  cosθ  -sinθ | | Vd |
| Vβ |   |  sinθ   cosθ | | Vq |
```

**代码实现**:
```c
// 反Park变换
// 输入: Vd, Vq (两相旋转坐标电压)
//       Theta (转子角度)
// 输出: Valpha, Vbeta (两相静止坐标电压)

int32 cosTheta = _IQcos(Theta);
int32 sinTheta = _IQsin(Theta);

// Vα = Vd*cosθ - Vq*sinθ
int32 Valpha = _IQmpy(Vd, cosTheta) - _IQmpy(Vq, sinTheta);

// Vβ = Vd*sinθ + Vq*cosθ
int32 Vbeta = _IQmpy(Vd, sinTheta) + _IQmpy(Vq, cosTheta);
```

### 4.4 电压重构（反解SVPWM）

**功能**: 从三相PWM占空比重构输出电压，用于电压前馈和解耦计算

```c
// 计算UVW相电压
void CalUVWVoltSet(int Phase)
{
    s32 m_U, m_V, m_W;
    s32 m_Coff;
    s32 m_HalfTc;
    s32 m_Data;
    s32 m_Zero;

    // 正弦和余弦
    sin = qsin(Phase);
    cos = qsin(16384 - Phase);

    // 半载波周期
    m_HalfTc = (s16)(gPWM.gPWMPrdApply >> 1);

    // 减去半周期得到占空比
    m_U = -gPWM.USet - m_HalfTc;
    m_V = -gPWM.VSet - m_HalfTc;
    m_W = -gPWM.WSet - m_HalfTc;

    // 零序分量
    m_Zero = (m_U + m_V + m_W) / 3;
    m_U = (m_U - m_Zero) * m_Data >> 14;
    m_V = (m_V - m_Zero) * m_Data >> 14;
    m_W = (m_W - m_Zero) * m_Data >> 14;

    // 电压系数 (考虑母线电压)
    m_Coff = (3550L * (s32)gUDC.uDC) / (gMotorInfo.Votage * 10);

    // UVW电压
    gVoltUVW.U = ((s32)m_U * (s32)m_Coff) >> 14;
    gVoltUVW.V = ((s32)m_V * (s32)m_Coff) >> 14;
    gVoltUVW.W = ((s32)m_W * (s32)m_Coff) >> 14;

    // αβ坐标电压
    gVoltUVW.Alph = ((s32)gVoltUVW.U * 23170L) >> 15;
    gVoltUVW.Beta = ((s32)(gVoltUVW.V - gVoltUVW.W) * 13377L) >> 15;

    // dq坐标电压 (用于电压前馈)
    gVoltUVW.UdQ = ((cos * (s32)(gVoltUVW.Alph)) + 
                    (sin * (s32)(gVoltUVW.Beta))) >> 15;
    gVoltUVW.UqQ = ((-(sin * (s32)(gVoltUVW.Alph)) + 
                    (cos * (s32)(gVoltUVW.Beta)))) >> 15;
}
```

---

## 5. PI控制器

### 5.1 32位PI控制器（电流环）

```c
// 32位PI控制器 (用于电流环)
void PID32(PID32_STRUCT *pid)
{
    int32 pTerm, iTerm;
    int32 m_Max, m_Min;

    // 计算比例项
    pTerm = _IQmpy(pid->KP, pid->Err);

    // 计算积分项
    iTerm = _IQmpy(pid->KI, pid->Err);

    // 积分抗饱和处理
    if ((pTerm > pid->OutMax) && (pid->Integral > 0)) {
        pid->Integral -= (pid->Integral >> 8) + 1;
        iTerm = 0;
    } else if ((pTerm < pid->OutMin) && (pid->Integral < 0)) {
        pid->Integral -= (pid->Integral >> 8) + 1;
        iTerm = 0;
    }

    // 积分累加
    pid->Integral += iTerm;

    // 计算输出
    pid->Out = pid->Integral + pTerm;

    // 输出限幅
    if (pid->Out > pid->OutMax) {
        pid->Out = pid->OutMax;
    } else if (pid->Out < pid->OutMin) {
        pid->Out = pid->OutMin;
    }
}
```

### 5.2 电流环控制实现

```c
// FVC矢量控制的电流环
void VCCsrControl(void)
{
    // Id环 (励磁电流环)
    gImAcrQ24.Max = ((s32)m_MaxVolt << 12);  // 输出上限
    gImAcrQ24.Min = -gImAcrQ24.Max;           // 输出下限
    gImAcrQ24.Deta = gIMTSetApply.M - gIMTQ24.M;  // 误差

    PID32(&gImAcrQ24);  // PI控制
    gUMTSet.M = (int)(gImAcrQ24.Out >> 12);  // 输出Vd

    // Iq环 (转矩电流环)
    gItAcrQ24.Max = ((s32)m_MaxVolt << 12);
    gItAcrQ24.Min = -gItAcrQ24.Max;
    gItAcrQ24.Deta = gIMTSetApply.T - gIMTQ24.T;  // 误差

    PID32(&gItAcrQ24);  // PI控制
    gUMTSet.T = (int)(gItAcrQ24.Out >> 12);  // 输出Vq

    // 同步机解耦控制
    if (SYNC_FVC == gCtrMotorType) {
        if (gPmDecoup.EnableDcp == 1) {
            // 电压前馈
            gUMTSet.T += gPmDecoup.EMF;
        } else if (gPmDecoup.EnableDcp == 2) {
            // 反电动势前馈
            gUMTSet.T += gPmDecoup.RotVq;
            gUMTSet.M += gPmDecoup.RotVd;
        }
    }

    // 输出限幅
    gUMTSet.M = __IQsat(gUMTSet.M, m_MaxVolt, -m_MaxVolt);
    gUMTSet.T = __IQsat(gUMTSet.T, m_MaxVolt, -m_MaxVolt);

    // 计算电压幅值
    m_Long = (((long)gUMTSet.M * (long)gUMTSet.M) +
               ((long)gUMTSet.T * (long)gUMTSet.T));
    gUAmpTheta.Amp = (Uint)qsqrt(m_Long);

    // 防饱和处理
    if (gUAmpTheta.Amp > (u16)m_MaxVolt) {
        // 电压饱和处理...
    }

    // 计算电压角度
    gUAmpTheta.Theta = atan(gUMTSet.M, gUMTSet.T);
    gOutVolt.VoltPhaseApply = gUAmpTheta.Theta;
}
```

---

## 6. SVPWM算法

### 6.1 SVPWM原理

SVPWM（Space Vector Pulse Width Modulation）空间矢量脉宽调制，将电压矢量映射到360度平面，使用8个基本电压矢量（6个非零矢量 + 2个零矢量）合成任意电压矢量。

**基本电压矢量**:
- U0(000), U7(111) - 零矢量
- U1(100), U2(110), U3(010), U4(011), U5(001), U6(101) - 非零矢量

### 6.2 扇区判断

```c
// SVPWM扇区计算
void OutPutPWM1(void)
{
    u16 m_iSec30;
    u16 m_iSec60;
    u16 m_iSec120;
    u16 m_iRamainPhase;
    u16 SinAlfa, SinAlfa120;

    // 获取电压相位
    phase = gPhase.OutPhase + 32768;

    // 计算30度扇区 (0-11)
    m_iSec30 = ((u32)phase * 12) >> 16;
    m_iSec60 = m_iSec30 >> 1;       // 60度扇区 (0-5)
    m_iSec120 = m_iSec60 >> 1;      // 120度扇区 (0-2)
    m_iRamainPhase = phase - ((u32)m_iSec120 << 16) / 3;
}
```

### 6.3 矢量作用时间计算

```c
// 计算基本矢量作用时间
SinAlfa120 = qsin(21845 - m_iRamainPhase);  // sin(120°-θ)
SinAlfa = qsin(m_iRamainPhase);               // sin(θ)

// 过调制处理
if (gRatio < 6000) {
    Ratio = gRatio;
} else {
    m_long = gRatio - 6000;
    m_long = m_long * m_long / 182;
    Ratio = m_long + 6000;
}

if (Ratio > 28000) Ratio = 28000;

// 计算Ta, Tb时间
if (Ratio < 31000) {
    m_iTALength = ((u32)SinAlfa120 * (u32)Ratio) >> 15;
    m_iTALength = (m_iTALength * (u32)gPWM.gPWMPrdApply) >> 12;

    m_iTBLength = ((u32)SinAlfa * (u32)Ratio) >> 15;
    m_iTBLength = (m_iTBLength * (u32)gPWM.gPWMPrdApply) >> 12;
} else {
    // 输出六阶梯波 (过调制)
    if (m_iRamainPhase < 5461) {
        m_iTALength = gPWM.gPWMPrdApply;
        m_iTBLength = 0;
    } else if (m_iRamainPhase >= 16384) {
        m_iTALength = 0;
        m_iTBLength = gPWM.gPWMPrdApply;
    } else {
        m_iTALength = gPWM.gPWMPrdApply;
        m_iTBLength = gPWM.gPWMPrdApply;
    }
}
```

### 6.4 七段式SVPWM

```c
// 七段式SVPWM发波
if (m_iTmaxLength > gPWM.gPWMPrdApply) {
    // 过调制处理
    // ... 见下文代码
} else {
    // 正常SVPWM发波

    // CPWM模式 (连续PWM)
    if (gPWM.PWMModle == MODLE_CPWM) {
        m_iZeroLength = ((gPWM.gPWMPrdApply - m_iTmaxLength) >> 1);
        m_iTALength += m_iZeroLength;
        m_iTBLength += m_iZeroLength;
    }
    // DPWM模式 (不连续PWM)
    else {
        m_iZeroLength = 0;
        // DPWM处理...
    }

    // 根据120度扇区分配Ta, Tb, Tc
    switch (m_iSec120) {
        case 0:
            m_iTaLength = m_iTALength;
            m_iTbLength = m_iTBLength;
            m_iTcLength = m_iZeroLength;
            break;
        case 1:
            m_iTaLength = m_iZeroLength;
            m_iTbLength = m_iTALength;
            m_iTcLength = m_iTBLength;
            break;
        case 2:
            m_iTaLength = m_iTBLength;
            m_iTbLength = m_iZeroLength;
            m_iTcLength = m_iTALength;
            break;
    }
}

// 输出到PWM寄存器
gPWM.USet = m_iTaLength;
gPWM.VSet = m_iTbLength;
gPWM.WSet = m_iTcLength;
```

### 6.5 PWM输出和死区补偿

```c
// PWM输出函数
void OutPutVolt(void)
{
    // 赋值PWM占空比
    gPWM.U = gPWM.USet;
    gPWM.V = gPWM.VSet;
    gPWM.W = gPWM.WSet;

    // 死区补偿
    if (gExtendCmd.bit.DeadCompMode == DEADBAND_COMP_280) {
        CalDeadBandComp();
    } else if (gExtendCmd.bit.DeadCompMode == DEADBAND_COMP_380) {
        HVfDeadBandComp();
    }

    DeadBandComp();  // 应用死区补偿
    SendPWM();       // 发送到PWM寄存器
}

// 死区补偿
void DeadBandComp(void)
{
    if (gRatio != 0) {
        if (ZERO_VECTOR_U != gPWM.gZeroLengthPhase) {
            gPWM.U += (long)gDeadBand.CompU;
        }
        if (ZERO_VECTOR_V != gPWM.gZeroLengthPhase) {
            gPWM.V += (long)gDeadBand.CompV;
        }
        if (ZERO_VECTOR_W != gPWM.gZeroLengthPhase) {
            gPWM.W += (long)gDeadBand.CompW;
        }
    }

    // 限幅
    gPWM.U = __IQsat(gPWM.U, gPWM.gPWMPrdApply, 0);
    gPWM.V = __IQsat(gPWM.V, gPWM.gPWMPrdApply, 0);
    gPWM.W = __IQsat(gPWM.W, gPWM.gPWMPrdApply, 0);
}

// 发送到PWM模块
void SendPWM(void)
{
    // 写入PWM周期
    EPwm1Regs.TBPRD = gPWM.gPWMPrdApply;
    EPwm2Regs.TBPRD = gPWM.gPWMPrdApply;
    EPwm3Regs.TBPRD = gPWM.gPWMPrdApply;

    // 写入比较值
    EPwm1Regs.CMPA.half.CMPA = gPWM.U;
    EPwm2Regs.CMPA.half.CMPA = gPWM.V;
    EPwm3Regs.CMPA.half.CMPA = gPWM.W;
}
```

---

## 7. 弱磁控制

### 7.1 弱磁控制原理

当电机转速超过额定转速时，逆变器输出电压达到饱和，此时需要通过降低励磁电流(Id)来维持电压平衡，实现更高转速运行。

```
恒转矩区: Id = 0 (表贴式) 或 Id < 0 (凸极式MTPA)
恒功率区: Id < 0 (弱磁运行)
```

### 7.2 弱磁控制结构

```c
// 同步机自动调整方式弱磁控制
s32 PmsmFwcAdjMethod(void)
{
    s32 m_s32;
    s16 m_Deta;
    u16 m_DetaAbs, m_Ki;

    // 电压误差计算
    if (gFluxWeak.CoefFlux != 0) {
        m_Deta = gOutVolt.LimitOutVoltPer - gPmFluxWeak.VoltLpf;
        m_Deta = __IQsat(m_Deta, 200, -1000);
        m_DetaAbs = abs(m_Deta);

        // 积分调整
        m_Ki = gFluxWeak.CoefFlux * m_DetaAbs;
        m_Ki = (m_Ki > 20000) ? 20000 : m_Ki;

        m_s32 = gPmFluxWeak.AdIdIntg + (((s32)m_Ki * (s32)m_Deta));
        m_s32 = (m_s32 > 0) ? 0 : m_s32;

        // 积分限幅
        gPmFluxWeak.AdIdIntg = (m_s32 > -((long)gPmFluxWeak.IdMax) << 15) ?
                               m_s32 : (-((long)gPmFluxWeak.IdMax) << 15);
        gPmFluxWeak.AdId = gPmFluxWeak.AdIdIntg >> 15;
    } else {
        gPmFluxWeak.AdId = 0;
    }

    // 最大转矩电流比控制
    if (gPmFluxWeak.AdId > -20) {
        gPmFluxWeak.IdForTorq = PmsmMaxTorqCtrl();
    }

    // 合成Id给定
    m_s32 = gPmFluxWeak.AdId + gPmFluxWeak.IdForTorq;
    m_s32 = Max(m_s32, -gPmFluxWeak.IdMax);
    return (m_s32 << 12);
}
```

### 7.3 MTPA（最大转矩电流比）控制

```c
// 凸极电机最大转矩电流比控制
// 凸极率(Lq/Ld) > 1.5时才进行MTPA控制
s16 PmsmMaxTorqCtrl(void)
{
    s16 m_Id;
    s32 m_Current, m_s32;

    m_s32 = gIMTSetApply.T >> 12;  // Iq给定值
    gPmFluxWeak.IqLpf = Filter16(m_s32, gPmFluxWeak.IqLpf);

    // 凸极率 > 1.5 且使能MTPA
    if ((gPmFluxWeak.SalientRate > 15) &&
        (gPmFluxWeak.PmsmMaxTorqCtrlEnable == 1) &&
        (gCtrMotorType == SYNC_FVC)) {
        // 计算Id
        m_Current = ((s32)gMotorExtPer.FluxRotor << 12) /
                    ((s32)(gMotorExtPer.LQ - gMotorExtPer.LD));
        m_Current = (m_Current * gPmFluxWeak.SalientRateCoff) / 100;
        m_s32 = m_Current * m_Current +
                (s32)gPmFluxWeak.IqLpf * gPmFluxWeak.IqLpf;
        m_Id = (s16)(m_Current - qsqrt(m_s32));
    } else {
        m_Id = 0;
    }

    return m_Id;
}
```

### 7.4 弱磁参数初始化

```c
// 弱磁参数初始化
void ResetParForPmsmFwc(void)
{
    u32 m_Current;

    gPmFluxWeak.IqLpf = 0;

    // 调整法弱磁变量初始化
    gPmFluxWeak.AdId = 0;
    gPmFluxWeak.AdIdIntg = 0;
    gPmFluxWeak.IdMixAdjFlag = 0;
    gPmFluxWeak.AdFreq = 0;
    gPmFluxWeak.AdFreqIntg = 0;

    // 计算Id最大负值
    m_Current = (((u32)gMotorExtPer.FluxRotor << 13) / gMotorExtPer.LD) - 1000;
    m_Current = ((m_Current > 4500UL) ? 4500UL : m_Current);
    if (gMotorInfo.Current > gInvInfo.InvCurrent) {
        m_Current = (((u32)gInvInfo.InvCurrent) * m_Current) / gMotorInfo.Current;
    }
    gPmFluxWeak.IdMax = (s16)m_Current;

    // 最大弱磁频率
    gPmFluxWeak.FreqMax = (s16)(gMotorInfo.FreqPer / 2);

    // 计算凸极率
    gPmFluxWeak.SalientRate = ((u32)gMotorExtInfo.LQ * 10UL) / gMotorExtInfo.LD;
}
```

---

## 8. 同步机解耦控制

### 8.1 解耦原理

永磁同步机dq轴之间存在交叉耦合：
- d轴电压受q轴电流变化影响
- q轴电压受d轴电流和转速影响

解耦控制通过前馈补偿消除耦合效应，提高控制性能。

### 8.2 解耦计算

```c
// 同步机解耦计算
void PmDecoupleDeal(void)
{
    long temp;

    // 滤波处理
    gPmDecoup.Omeg = Filter2(gRotorSpeed.SpeedApply, gPmDecoup.Omeg);
    gPmDecoup.Isd = Filter2((gIMTQ24.M >> 12), gPmDecoup.Isd);
    gPmDecoup.Isq = Filter2((gIMTQ24.T >> 12), gPmDecoup.Isq);

    // d轴交叉耦合项
    temp = (long)gPmDecoup.Isd * gMotorExtPer.LD >> 13;
    gPmDecoup.PhiSd = temp + gMotorExtPer.FluxRotor;
    gPmDecoup.RotVq = (long)gPmDecoup.Omeg * gPmDecoup.PhiSd >> 15;

    // q轴交叉耦合项
    gPmDecoup.PhiSq = (long)gPmDecoup.Isq * gMotorExtPer.LQ >> 13;
    gPmDecoup.RotVd = -(long)gPmDecoup.Omeg * gPmDecoup.PhiSq >> 15;

    // 给定值前馈
    gPmDecoup.IsdSet = gIMTSetApply.M >> 12;
    gPmDecoup.IsqSet = gIMTSetApply.T >> 12;

    temp = (long)gPmDecoup.IsdSet * gMotorExtPer.LD >> 13;
    gPmDecoup.PhiSdSet = temp + gMotorExtPer.FluxRotor;
    gPmDecoup.RotVqSet = (long)gPmDecoup.Omeg * gPmDecoup.PhiSdSet >> 15;

    // 反电动势
    gPmDecoup.EMF = (s32)gMotorExtPer.FluxRotor * gPmDecoup.Omeg >> 15;

    gPmDecoup.PhiSqSet = (long)gPmDecoup.IsqSet * gMotorExtPer.LQ >> 13;
    gPmDecoup.RotVdSet = -(long)gPmDecoup.Omeg * gPmDecoup.PhiSqSet >> 15;
}
```

### 8.3 解耦控制应用

```c
// 在电流环中应用解耦
void VCCsrControl(void)
{
    // ... Id/Iq PI控制 ...

    // 同步机解耦控制
    if (SYNC_FVC == gCtrMotorType) {
        if (gPmDecoup.EnableDcp == 1) {
            // 电压前馈模式
            gUMTSet.T += gPmDecoup.EMF;
        } else if (gPmDecoup.EnableDcp == 2) {
            // 反电动势前馈模式
            gUMTSet.T += gPmDecoup.RotVq;  // q轴解耦
            gUMTSet.M += gPmDecoup.RotVd;  // d轴解耦
        }
    }

    // 输出限幅
    gUMTSet.M = __IQsat(gUMTSet.M, m_MaxVolt, -m_MaxVolt);
    gUMTSet.T = __IQsat(gUMTSet.T, m_MaxVolt, -m_MaxVolt);
}
```

---

## 9. 速度环控制

### 9.1 速度环PI控制

```c
// 矢量控制的速度环
void VcAsrControl(void)
{
    s32 m_Long, m_Deta;

    // 设置转矩限制
    if (gRotorSpeed.SpeedApply >= 0) {
        gAsr.Asr.Max = gAsr.PosTorqueLimit;
        gAsr.Asr.Min = -gAsr.NegTorqueLimit;
    } else {
        gAsr.Asr.Max = gAsr.NegTorqueLimit;
        gAsr.Asr.Min = -gAsr.PosTorqueLimit;
    }

    // 速度误差
    m_Long = (s32)gMainCmd.FreqSetApply - (s32)gRotorSpeed.SpeedApply;
    gAsr.Asr.Deta = __IQsat(m_Long, 16383, -16383);

    // 从机转矩控制
    if ((gSubCommand.bit.VCFolFlag == 1) && (1 == gMainCmd.Command.bit.TorqueCtl)) {
        m_Deta = ((long)gRotorSpeed.FreWindow << 15) / gBasePar.FullFreq01;
        gAsr.Asr.KI = 0;
        if (gAsr.Asr.Deta > m_Deta) {
            gAsr.Asr.Deta -= m_Deta;
        } else if (gAsr.Asr.Deta < (-m_Deta)) {
            gAsr.Asr.Deta += m_Deta;
        } else {
            gAsr.Asr.Deta = 0;
        }
    }

    // PID控制
    PID((PID_STRUCT *)&gAsr.Asr);

    // 输出转矩电流给定
    gVCPar.AsrOut = gAsr.Asr.Out >> (16 - 12);

    // 处理主从转矩叠加
    if ((gSubCommand.bit.VCFolFlag == 1) && (1 == gMainCmd.Command.bit.TorqueCtl)) {
        m_Deta = ((long)gVCPar.TorMasToFol << 12) / 1000;
        m_Deta = (gVCPar.AsrOut >> 12) + m_Deta;
        m_Deta = __IQsat(m_Deta, gAsr.Asr.Max, gAsr.Asr.Min);
        gIMTSet.T = m_Delta << 12;
    } else {
        gIMTSet.T = gVCPar.AsrOut;
    }
}
```

### 9.2 零伺服模式

```c
// 零伺服速度环
void VcAsrControl1(void)
{
    s32 m_Max, m_Min;
    s32 m_DetaFreq;
    s64 m_KpOut, m_KiOut, m_Out;
    s32 m_DetaPos;

    // 设置转矩限制
    if (gRotorSpeed.SpeedApply >= 0) {
        m_Max = (s32)gAsr.PosTorqueLimit << 16;
        m_Min = -(s32)gAsr.NegTorqueLimit << 16;
    } else {
        m_Max = (s32)gAsr.NegTorqueLimit << 16;
        m_Min = -(s32)gAsr.PosTorqueLimit << 16;
    }

    // 频率给定平滑
    m_DetaFreq = ((s32)gMainCmd.FreqSet << 9) - gAsr.FreqSet;
    if (abs(m_DetaFreq) < (1L << 9)) {
        gAsr.FreqSet = ((s32)gMainCmd.FreqSet << 9);
    } else {
        gAsr.FreqSet += (m_DetaFreq >> 1);  // 平滑滤波
    }

    // 频率误差
    m_DetaFreq = gAsr.FreqSet - gAsr.FreqFeedFilter;
    gMainCmd.DetaFreq = m_DetaFreq >> 9;
    m_DetaFreq = __IQsat(m_DetaFreq, (16383L << 9), (-16383L << 9));

    // 比例项
    m_KpOut = (gAsr.Kp * (s64)m_DetaFreq) >> 5;

    // 积分项 (位置环)
    DINT;
    m_DetaPos = gAsr.PosSet - gPGData.RefPos;
    if (gAsr.OutFlag * m_DetaPos > 0) {
        gAsr.PosSet = gPGData.RefPos + gAsr.DetaPos;
        m_DetaPos = gAsr.DetaPos;
    }
    gAsr.DetaPos = m_DetaPos;
    m_KiOut = ((s64)gAsr.DetaPos * (s64)gAsr.KiPos) >> 3;
    EINT;

    // 总输出
    m_Out = m_KpOut + m_KiOut;

    // 输出限幅
    if (m_Out < m_Min) {
        m_Out = m_Min;
        gAsr.OutFlag = -1;
    } else if (m_Out > m_Max) {
        m_Out = m_Max;
        gAsr.OutFlag = 1;
    }
    gAsr.Out = m_Out;
    gIMTSet.T = gAsr.Out >> 4;
}
```

### 9.3 速度环参数自适应

```c
// 速度环参数准备
void PrepareAsrPar(void)
{
    int m_AbsFreq, m_FreqUp;
    int m_DetaKP, m_DetaKI, m_DetaFreq;

    // 准备高低频段参数
    gAsr.KPHigh = gVCPar.ASRKpHigh << 8;
    gAsr.KPLow = gVCPar.ASRKpLow << 8;
    gAsr.KPLow = (s16)((s32)gAsr.KPLow * gAsr.KPLowCoff / 10L);

    // 计算KI
    if ((gVCPar.ASRKpHigh >> 5) >= gVCPar.ASRTIHigh) {
        gAsr.KIHigh = 32767;
    } else {
        gAsr.KIHigh = ((Ulong)gVCPar.ASRKpHigh << 10) / gVCPar.ASRTIHigh;
    }

    if ((gVCPar.ASRKpLow >> 5) >= gVCPar.ASRTILow) {
        gAsr.KILow = 32767;
    } else {
        gAsr.KILow = ((Ulong)gVCPar.ASRKpLow << 10) / gVCPar.ASRTILow;
    }

    // 切换频率点
    gAsr.SwitchHigh = ((Ulong)gVCPar.ASRSwitchHigh << 15) / gBasePar.FullFreq;
    gAsr.SwitchLow = ((Ulong)gVCPar.ASRSwitchLow << 15) / gBasePar.FullFreq;

    // 根据频率选择参数
    m_AbsFreq = abs(gMainCmd.FreqSyn);
    if (m_AbsFreq <= gAsr.SwitchLow) {
        gAsr.Asr.KP = gAsr.KPLow;
        gAsr.Asr.KI = gAsr.KILow;
    } else if (m_AbsFreq >= gAsr.SwitchHigh) {
        gAsr.Asr.KP = gAsr.KPHigh;
        gAsr.Asr.KI = gAsr.KIHigh;
    } else {
        // 线性插值
        m_FreqUp = m_AbsFreq - gAsr.SwitchLow;
        m_DetaFreq = gAsr.SwitchHigh - gAsr.SwitchLow;
        gAsr.Asr.KP = ((long)m_DetaKP * m_FreqUp) / m_DetaFreq + gAsr.KPLow;
        gAsr.Asr.KI = ((long)m_DetaKI * m_FreqUp) / m_DetaFreq + gAsr.KILow;
    }
}
```

---

## 10. 电流环控制

### 10.1 电流环PI参数计算

```c
// 电流环参数计算
void IPMCalAcrPIDCoff(void)
{
    u32 m_Long;
    Uint m_UData, m_BaseL;
    Ulong m_Ulong;

    // 电感基值计算
    m_BaseL = ((Ulong)gMotorInfo.Votage * 3678) / gMotorInfo.Current;
    m_BaseL = ((Ulong)m_BaseL * 5000) / gBasePar.FullFreq01;

    // 反电动势系数
    m_UData = ((Ulong)gMotorExtReg.RsPm * (Ulong)gMotorInfo.Current) /
              gMotorInfo.Votage;
    gMotorExtPer.Rpm = ((Ulong)m_UData * 18597) >> 14;

    // d/q轴电感标幺化
    m_Ulong = (((Ulong)gMotorExtReg.LD << 15) + m_BaseL) >> 1;
    gMotorExtPer.LD = m_Ulong / m_BaseL / 10;
    m_Ulong = (((Ulong)gMotorExtReg.LQ << 15) + m_BaseL) >> 1;
    gMotorExtPer.LQ = m_Ulong / m_BaseL / 10;

    // Kp = Ls / (2 * TDelay)
    m_Long = (53050UL * (u32)(gMotorExtPer.LD >> 1)) / gBasePar.FullFreq01;
    gPmParEst.IdKp = (u16)Min(m_Long, 8000);
    gPmParEst.IqKp = gPmParEst.IdKp;

    // Ki = Rs / 3
    gPmParEst.IdKi = (gMotorExtPer.Rpm / 3);
    gPmParEst.IqKi = gPmParEst.IdKi;

    // 参数辨识时调整参数
    if (gUVCoff.RsTune == 2) {
        if (gBasePar.FcSetApply > C_DOUBLE_ACR_MAX_FC) {
            gImAcrQ24.KP = (long)gPmParEst.IdKp * gBasePar.FcSetApply / 100;
            gItAcrQ24.KP = (long)gPmParEst.IqKp * gBasePar.FcSetApply / 100;
            gImAcrQ24.KI = (s32)gPmParEst.IdKi;
            gItAcrQ24.KI = (s32)gPmParEst.IqKi;
        } else {
            gImAcrQ24.KP = (long)gPmParEst.IdKp * gBasePar.FcSetApply / 50;
            gItAcrQ24.KP = (long)gPmParEst.IqKp * gBasePar.FcSetApply / 50;
            gImAcrQ24.KI = (s32)gPmParEst.IdKi;
            gItAcrQ24.KI = (s32)gPmParEst.IqKi;
        }
    }
}
```

### 10.2 载波频率自适应参数

```c
// 载波频率自适应参数
void PrepPmsmCsrPrar(void)
{
    long ImKp, ImKi, ItKp, ItKi;
    long m_Long, m_Long1;

    // 高于二次谐波频率时减弱PI
    if (gBasePar.FcSetApply > C_DOUBLE_ACR_MAX_FC) {
        ImKp = (long)gVCPar.AcrImKp * gBasePar.FcSetApply / 100L;
        ItKp = (long)gVCPar.AcrItKp * gBasePar.FcSetApply / 100L;
        ImKi = gVCPar.AcrImKi / 2;
        ItKi = gVCPar.AcrItKi / 2;
    } else {
        ImKp = (long)gVCPar.AcrImKp * gBasePar.FcSetApply / 50;
        ItKp = (long)gVCPar.AcrItKp * gBasePar.FcSetApply / 50;
        ImKi = gVCPar.AcrImKi;
        ItKi = gVCPar.AcrItKi;
    }

    gImAcrQ24.KP = Min(ImKp, 16383);
    gItAcrQ24.KP = Min(ItKp, 16383);
    gImAcrQ24.KI = ImKi;
    gItAcrQ24.KI = ItKi;

    // 保存参数
    gPmCsr2.Kp = gImAcrQ24.KP;
    m_Long = gImAcrQ24.KI >> 5;
    if (m_Long < 1) m_Long = 1;
    gPmCsr2.KiM = m_Long;
}
```

---

## 11. 磁极位置检测

### 11.1 初始位置检测

```c
// 永磁同步机磁极初始位置角检测阶段
void RunCaseIpmInitPos(void)
{
    if ((gError.ErrorCode.all != 0) ||
       (gMainCmd.Command.bit.Start == 0)) {
        DisableDrive();
        SynInitPosDetSetPwm(6);
        gIPMInitPos.Step = 0;
        ResetADCEndIsr();
        TurnToStopStatus();
        FlyingStartInitDeal();
        return;
    }

    switch (gMainStatus.SubStep) {
        case 1:
            SetADCEndIsr(ADCEndIsrTune_POLSE_POS);
            if (gIPMInitPos.Step == 0) {
                gIPMInitPos.Step = 1;
                gMainStatus.SubStep++;
            } else {
                gIPMInitPos.Step = 0;
            }
            break;

        case 2:
            if (gIPMInitPos.Step == 0) {  // 中断辨识完成
                // 计算补偿位置
                gIPMPos.CompPos = ((Ulong)gIPMPos.CompPosFun << 16) / 3600;
                gIPMPos.InitPos = gIPMPos.InitPos + gIPMPos.CompPos;

                // 设置位置
                SetIPMPos((Uint)gIPMPos.InitPos);
                SetIPMRefPos((Uint)gIPMPos.InitPos);
                SvcSetRotorPos(gIPMPos.InitPos);

                // 检查上次掉电位置
                if (abs((int)(gIPMPos.PowerOffPosDeg - gIPMPos.RotorPos)) < 3641) {
                    SetIPMPos((Uint)gIPMPos.PowerOffPosDeg);
                    SetIPMRefPos((Uint)gIPMPos.PowerOffPosDeg);
                }

                gIPMInitPos.Flag = 1;
                DisableDrive();
                ResetADCEndIsr();
                if (gUVCoff.RsTune == 2) {
                    IPMCalAcrPIDCoff();
                }
                gMainStatus.SubStep++;
            }
            break;

        case 3:
            InitSetPWM();
            InitSetAdc();
            SetInterruptEnable();
            gMainStatus.SubStep++;
            break;

        case 4:
            PrepareParForRun();
            gMainStatus.RunStep = STATUS_STOP;
            gMainStatus.SubStep = 0;
            gMainStatus.PrgStatus.all = 0;
            break;

        default:
            gError.ErrorCode.all |= ERROR_TUNE_FAIL;
            gError.ErrorInfo[4].bit.Fault1 = 4;
            break;
    }
}
```

### 11.2 Ld/Lq电感计算

```c
// LD、LQ轴电感计算函数
void SynCalLdAndLq(void)
{
    long m_L0, m_L1, m_L2, m_DetaL;

    // 计算平均电感
    m_L0 = (gIPMInitPos.LPhase[0] + gIPMInitPos.LPhase[1] +
            gIPMInitPos.LPhase[2]) / 6;

    // 计算电感差
    m_L1 = (((llong)(gIPMInitPos.LPhase[0] - gIPMInitPos.LPhase[2])) << 14) / 28378L;
    m_L2 = gIPMInitPos.LPhase[1] - m_L0 * 2;
    m_DetaL = -(((long)qsqrt(m_L1 * m_L1 + m_L2 * m_L2)) / 2);

    // 计算d/q轴电感
    gIPMInitPos.Ld = (u16)(m_L0 + m_DetaL);
    gIPMInitPos.Lq = (u16)(m_L0 - m_DetaL);

    // 保存到寄存器
    gMotorExtReg.LD = gIPMInitPos.Ld;
    gMotorExtReg.LQ = gIPMInitPos.Lq;
}
```

---

## 12. 编码器Z信号处理

### 12.1 Z信号中断处理

```c
// 编码器Z信号中断处理
interrupt void PG_Zero_isr(void)
{
    if ((*EQepRegs).QFLG.bit.IEL == 1) {
        EALLOW;
        (*EQepRegs).QCLR.bit.IEL = 1;
        (*EQepRegs).QCLR.bit.INT = 1;
        PieCtrlRegs.PIEACK.all = PIEACK_GROUP5;
        EDIS;

        // Z信号滤波处理
        if ((gIPMZero.zFilterCnt < 4) || (gIPMPos.ZIntFlag == 1)) {
            return;
        }
        gIPMZero.zFilterCnt = 0;

        // 记录Z信号数据
        gIPMPos.ZSigNumSet++;
        gIPMPos.ZBakRotorPos = gIPMPos.RotorPos;
        gIPMPos.QepCntBak = GetQepCnt();
        gIPMPos.QepCntPosCalBak = gIPMPos.QepCntPosCal;

#if (AIRCOMPRESSOR == 0)
        gIPMPos.ZBakUVW = Get_UVW_PG_U() + Get_UVW_PG_V() + Get_UVW_PG_W();
#else
        gIPMPos.ZBakUVW = 0;
#endif
    }
}
```

### 12.2 Z信号位置校正

```c
// 计算Z信号发生时的位置偏差
void IPMPosCalZWindage(void)
{
    s32 m_DetaCnt;
    s32 m_Data;

    // 计算Z信号发生时与最近计算位置之间的计数偏差
    m_DetaCnt = (s32)(gIPMPos.QepCntBak - gIPMPos.QepCntPosCalBak);

    // 转换为角度
    m_Data = ((m_DetaCnt << 14) * (s32)gMotorExtInfo.Poles);
    gIPMPos.ZPosWindage = (u16)(m_Data / (s32)gPGData.PulseNum);
}

// 基准位置到达时编码器位置角校正
void IPMPosAdjustZIndex(void)
{
    s16 m_DetaPos;
    s32 m_DetaPosShow;
    static u16 m_ZErrCnt = 0;

    // 无新Z信号则返回
    if (gIPMPos.ZSigNum == gIPMPos.ZSigNumSet) {
        return;
    }

    // UVW编码器校验
    if (gPGData.PGType == PG_TYPE_UVW) {
        if ((gIPMPos.ZBakUVW & 0x03) != 1) {
            return;
        }
    }
    gIPMPos.ZSigNum = gIPMPos.ZSigNumSet;

    // 计算位置偏差
    IPMPosCalZWindage();

    m_DetaPos = (s16)(gIPMPos.RotorZero -
               (gIPMPos.ZBakRotorPos + gIPMPos.ZPosWindage));
    gIPMPos.ZBakDetaPos = m_DetaPos;

    // 错误处理
    if ((abs(m_DetaPos) > C_MAX_DETA_POS_Z) &&
        (gIPMPos.ZResetFlag == C_Z_RESET_POS_LIMIT) &&
        (gMainStatus.RunStep != STATUS_GET_PAR)) {
        m_ZErrCnt++;
        if (m_ZErrCnt > 5) {
            m_ZErrCnt = 0;
            gError.ErrorCode.all |= ERROR_ENCODER;
            gError.ErrorInfo[4].bit.Fault3 = 6;
        }
        return;
    } else {
        m_ZErrCnt = 0;
    }

    gIPMPos.ZDetaPos = m_DetaPos;
}
```

---

## 13. FOC控制流程图

### 13.1 主控制流程

```
                        ┌─────────────────┐
                        │   ADC中断       │
                        │ (约30us周期)    │
                        └────────┬────────┘
                                 │
                                 ▼
                    ┌────────────────────────┐
                    │   1. 电流采样          │
                    │   Iu, Iv, Iw          │
                    └────────┬───────────────┘
                                 │
                                 ▼
                    ┌────────────────────────┐
                    │   2. Clarke变换        │
                    │   Iα, Iβ              │
                    └────────┬───────────────┘
                                 │
                                 ▼
                    ┌────────────────────────┐
                    │   3. Park变换          │
                    │   Id, Iq               │
                    └────────┬───────────────┘
                                 │
                                 ▼
                    ┌────────────────────────┐
                    │   4. 弱磁控制          │
                    │   IdRef = f(转速)      │
                    └────────┬───────────────┘
                                 │
                    ┌────────────┴────────────┐
                    ▼                         ▼
          ┌─────────────────┐     ┌─────────────────┐
          │   5a. Id环PI    │     │   5b. Iq环PI    │
          │   IdRef → Vd    │     │   IqRef → Vq    │
          └────────┬────────┘     └────────┬────────┘
                   │                        │
                   └────────────┬───────────┘
                                ▼
                    ┌────────────────────────┐
                    │   6. 解耦补偿          │
                    │   Vd += f(Iq, ω)       │
                    │   Vq += f(Id, ω, ψf)  │
                    └────────┬───────────────┘
                                 │
                                 ▼
                    ┌────────────────────────┐
                    │   7. 电压限幅          │
                    │   Vd, Vq 限幅          │
                    └────────┬───────────────┘
                                 │
                                 ▼
                    ┌────────────────────────┐
                    │   8. 反Park变换        │
                    │   Vα, Vβ              │
                    └────────┬───────────────┘
                                 │
                                 ▼
                    ┌────────────────────────┐
                    │   9. SVPWM计算        │
                    │   Ta, Tb, Tc          │
                    └────────┬───────────────┘
                                 │
                                 ▼
                    ┌────────────────────────┐
                    │   10. 死区补偿        │
                    │   更新占空比          │
                    └────────┬───────────────┘
                                 │
                                 ▼
                    ┌────────────────────────┐
                    │   11. PWM输出         │
                    │   EPWM1/2/3          │
                    └────────────────────────┘
```

### 13.2 速度环流程

```
                    ┌─────────────────────┐
                    │   0.5ms 周期任务    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  获取速度给定        │
                    │  FreqSet            │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  获取速度反馈        │
                    │  SpeedFeed          │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  计算速度误差        │
                    │  Error = Set - Feed │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  选择PI参数          │
                    │  根据频率段选择      │
                    │  Kp, Ki             │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  PI计算              │
                    │  输出转矩电流IqRef  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  转矩限制           │
                    │  正/负向限制        │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  输出给电流环       │
                    └─────────────────────┘
```

---

## 14. 关键参数计算

### 14.1 电流环参数

**理论公式**:
```
Kp = Ls / (2 * Tdelay)
Ki = Rs / 3
```

其中:
- Ls: 定子电感
- Rs: 定子电阻
- Tdelay: 电流环滞后时间 (~150μs)

**代码实现**:
```c
// Kp = 53050 * Ls / FullFreq (Q12格式)
m_Long = (53050UL * (u32)(gMotorExtPer.LD >> 1)) / gBasePar.FullFreq01;
gPmParEst.IdKp = (u16)Min(m_Long, 8000);

// Ki = Rs / 3 (Q16格式)
gPmParEst.IdKi = (gMotorExtPer.Rpm / 3);
```

### 14.2 速度环参数

**参数计算**:
```
Kp = Kp_func * (f_base/10) * 8
Ki = (Kp_func/Ki_func) * Kp * 1600 / f_base
```

**频率分段**:
- SwitchLow: 低频段切换点
- SwitchHigh: 高频段切换点
- SwitchZero: 零伺服切换点

### 14.3 调制比

```c
// 调制比计算
gRatio = (gOutVolt.VoltApply * 1000) / gUDC.uDC;

// 调制比范围
// 0-6000: 线性区
// 6000-28000: 过调制区
// > 28000: 六阶梯波
```

---

## 15. 总结

### 15.1 FOC控制核心要点

1. **坐标变换**: Clarke → Park → 反Park
2. **双环控制**: 速度环(外环) → 电流环(内环)
3. **PI控制器**: 32位精度，支持积分抗饱和
4. **SVPWM**: 七段式，过调制处理，死区补偿
5. **弱磁控制**: 电压闭环，自动调整Id
6. **解耦控制**: 消除dq轴交叉耦合

### 15.2 关键变量索引

| 变量名 | 类型 | 说明 |
|--------|------|------|
| `gIMTSet.M` | s32 | Id给定 (Q24) |
| `gIMTSet.T` | s32 | Iq给定 (Q24) |
| `gIMTQ24.M` | s32 | Id反馈 (Q24) |
| `gIMTQ24.T` | s32 | Iq反馈 (Q24) |
| `gUMTSet.M` | s16 | Vd输出 |
| `gUMTSet.T` | s16 | Vq输出 |
| `gAsr.Asr` | PID_STRUCT | 速度环PID |
| `gImAcrQ24` | PID32_STRUCT | Id电流环 |
| `gItAcrQ24` | PID32_STRUCT | Iq电流环 |
| `gPWM.USet/VSet/WSet` | Uint | PWM占空比 |
| `gOutVolt.VoltApply` | s16 | 输出电压幅值 |
| `gOutVolt.VoltPhaseApply` | s16 | 输出电压相位 |

### 15.3 调试建议

1. **先调电流环**: 固定Id=0，给定Iq，观察电流波形
2. **再调速度环**: 电流环稳定后，加入速度环
3. **观察波形**: 
   - 示波器观察U/V/W相电流波形
   - 观察Id/Iq是否解耦
   - 检查编码器Z信号是否准确
4. **参数调整原则**:
   - Kp: 响应速度
   - Ki: 稳态误差
   - 先调Kp使系统响应快而不振荡
   - 再调Ki消除稳态误差

---

**文档版本**: 1.0  
**生成日期**: 2026年4月1日  
**适用项目**: MD500E FOC控制  
**硬件平台**: TMS320F28035
