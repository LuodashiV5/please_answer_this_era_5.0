# 换芯片再也不用怕！使用C语言实现多态与继承构建统一的外设接口驱动

原创 进击的小芯 

 

不知道各位小伙伴有没有经历过这样的场景：

-  已经稳定生产的产品突然因为芯片缺货问题需要切换主芯片；
- 一套代码需要稍微更改应用在不同形态的产品上；

然而打开工程发现代码是这样的：

![img](https://mmbiz.qpic.cn/mmbiz_png/LFl23WoliaP3XpFwK1pZXOYuExnSobOlSG37E0Vw5HiaicKia9ne7iaaJLsO9vcfv81lax9RzzXXRdATBXKeZyAaJWg/640?wx_fmt=png&from=appmsg)

这样的：

![img](https://mmbiz.qpic.cn/mmbiz_png/LFl23WoliaP3XpFwK1pZXOYuExnSobOlSAKnRRKyVtcuSkDgl5kTiaMUpnFricZMo8zoEMeFzP5M2Pr38Vsu4zpGQ/640?wx_fmt=png&from=appmsg)

还有这样的：

![img](https://mmbiz.qpic.cn/mmbiz_png/LFl23WoliaP3XpFwK1pZXOYuExnSobOlSRZzGxuc4Sib0Zy3ff2JtyiaHibLHLzO80slQbrj88fWCmMKSLMLSKiaFGw/640?wx_fmt=png&from=appmsg)

图片中的代码问题是什么？

- **外部设备驱动代码和芯片底层代码和紧耦合在一起；如果芯片变动每个外部设备的驱动代码都要修改；**
- **每个外设单独编写接口导致代码冗余；**

今天这篇文章教你使用C语言构建可维护性强的层次化代码结构，由上述传统的编程方式转向面向接口编程：

- **设备驱动代码与芯片接口代码松耦合，代码结构层次清晰，一目了然！**
- **统一上层调用接口，产品迭代更换芯片时不再头大！**

## 一、概念介绍

在嵌入式开发中，外设驱动往往与具体芯片紧密绑定：UART、SPI、I2C 等接口的寄存器定义和调用方式各不相同。传统做法是为每个使用接口的外设编写独立的驱动接口，这在过去的产品开发中尚且应付的过来；但是在当下，新的芯片层出不穷，功能需求日益增长的背景下，传统的编程方法导致代码难以复用，硬件变更时代码迁移效率低下，已经不能满足当前的开发需求；为了解决这一问题，可以借鉴面向对象的思想，在 C 语言中应用多态与继承的编程范式，构建统一的外设驱动接口。

此编程方法的核心思想是：

- **抽象接口**：定义统一的函数指针表（vtable），屏蔽底层差异；
- **多态实现**：不同外设驱动实现同样的接口函数，调用时无需关心具体类型。实现接口统一；
- **继承与扩展**：通过 `void*` 指针和上下文结构体，实现类似继承的效果，便于扩展和维护。

废话不多说，直接上代码！

## 二、实现方式

### 1. 抽象接口定义

```c
// drv_comm.h —— 抽象接口
#pragma once
#include <stddef.h>
#include <stdint.h>
//定义统一的公共接口虚函数表
typedefstruct{
    int (*ctrl)(void *pdev,_Bool s);
    void (*send)(void *pdev, uint8_t* buf, int len);
    void (*recv)(void *pdev, uint8_t* buf, int len);
}CommVtb_t;

// 基类结构体
typedefstruct{
    //具体接口的虚函数表
    CommVtb_t* vt;
    //公共属性
    void* hw_handle;//硬件操作句柄
    _Bool s;        //状态
}CommDev_t;

static inline int comm_send(void* pdev, void* buf, int len)
{
  int send_cnt = pdev->vt->send(pdev, buf, len);
  return send_cnt;
}
static inline int comm_recv(void* pdev, uint8_t* buf, int len)
{
  int recv_cnt = pdev->vt->recv(pdev, buf, len);
  return recv_cnt;
}
static inline int comm_ctrl(void* pdev, _Bool s)
{
  int ret = pdev->vt->ctrl(pdev, s);
  return ret;
}
```

### 2. UART接口多态实现

drv_usart.h/c文件实现：

```c
//dev_usart.h
#ifndef _DRV_USART_H
#define _DRV_USART_H
#include "drv_comm.h"

// drv_usart.h —— USART 派生类定义
typedef struct {
    CommDev_t base;       //结构体首部需放基类，不然无法实现类型转换；
    uint32_t baudrate;     //派生类专属属性
    /*专属方法*/
    int (*set_baud)(void *pdev, uint32_t baud);
} CommDev_Usart_t;

int CommDev_UsartInit(CommDev_Usart_t *u, void *usart_handle, uint32_t baud);
#endif


// drv_usart.c
#include "drv_usart.h"
#include "stm32f1xx_ll_usart.h"
/** 基类的通用方法在此实现 */
static int ctrl(void *pdev, _Bool s)
{
    if(s){
        LL_USART_Enable(((CommDev_Usart_t*)pdev)->base.hw_handle);
    }else{
        LL_USART_Disable(((CommDev_Usart_t*)pdev)->base.hw_handle);
    }
    ((CommDev_Usart_t*)pdev)->base.s  = s;
}

static int send(void *pdev, uint8_t* buf, int len)
{
  int send_cnt=0;
  ;//调用底层的LL库或HAL库发送数据
  return send_cnt;
}

static int recv(void *pdev, uint8_t* buf, int len)
{
  int recv_cnt=0;
  ;//调用底层的LL库或HAL库接收数据
  return recv_cnt;
}

/** 派生类的专属方法 **/
static int set_baudrate(void *pdev, uint32_t baud)
{
    CommDev_Usart_t *dev = (CommDev_Usart_t*)pdev;
    LL_USART_SetBaudRate(dev->base.hw_handle, 36000000, baud);
    return 0;
}

//每一个Usart实例对象共享同一份vt
static const CommVtb_t vt = {
    .ctrl = ctrl,
    .send = send,
    .recv = recv
};

int CommDev_UsartInit(CommDev_Usart_t* u, void* usart_handle, uint32_t baud)
{
  u->base.vt = (CommVtb_t*)&vt;//绑定基类公共方法虚函数表
  u->base.hw_handle = usart_handle;
  u->set_baud = set_baudrate;//绑定派生类的专属方法
  //调用LL库初始化对应的Usart接口；
  ...
}
```

### 3. SPI接口多态实现

drv_spi.h/c头文件实现：

```c
#ifndef _DRV_SPI_H
#define _DRV_SPI_H
#include "drv_comm.h"
// drv_spi.h —— SPI 派生类
typedef struct {
    CommDev_t base;       //基类
    uint32_t bus_clk;     //属性
    /*专属方法*/
    int (*set_clk)(void *pdev, uint32_t bus_clk);
} CommDev_Spi_t;
 
int CommDev_SpiInit(CommDev_Spi_t *d, void *spi_handle, uint32_t bus_clk);
#endif


//drv_spi.c
#include "drv_spi.h"
#include "stm32f1xx_ll_spi.h"
/** 基类的通用方法在此实现 */
static int ctrl(void *pdev, _Bool s)
{
    if(s){
        LL_SPI_Enable(((CommDev_Spi_t*)pdev)->base.hw_handle);
    }else{
        LL_SPI_Disable(((CommDev_Spi_t*)pdev)->base.hw_handle);
    }
    ((CommDev_Spi_t*)pdev)->base.s  = s;
}

static int send(void *pdev, uint8_t* buf, int len)
{
    int send_cnt=0;
    if(buf){
        for(;len;){
            LL_SPI_TransmitData8(((CommDev_Spi_t*)pdev)->base.hw_handle, *buf);
            len--;
            buf++;
        }
    }
    return send_cnt;
}

static int recv(void *pdev, uint8_t* buf, int len)
{
    int recv_cnt = 0;
    if(buf){
        for(;len;){
             *buf = LL_SPI_ReceiveData8(((CommDev_Spi_t*)pdev)->base.hw_handle);
            len--;
            buf++;
        }
    }
    return recv_cnt;
}

/** 派生类的专属方法 **/
static int spidev_set_clk(void *pdev, uint32_t baud)
{
    CommDev_Spi_t *dev = (CommDev_Spi_t*)pdev;
    LL_SPI_SetBaudRatePrescaler(dev->base.hw_handle, baud);
    return 0;
}

//每一个实例对象共享同一份vt
static const CommVtb_t vt = {
    .ctrl = ctrl,
    .send = send,
    .recv = recv
};

int CommDev_SpiInit(CommDev_Spi_t* d, void* spi_handle, uint32_t clk)
{
    d->base.vt = (CommVtb_t*)&vt;
    d->base.hw_handle = spi_handle;
    d->base.s = ENABLE;
    d->set_clk = spidev_set_clk;//绑定专属方法
    //初始化具体接口
    //LL_SPI_Init(spi_handle,...);
}
```

### 4. 使用示例

```c
#include "drv_usart.h"
#include "drv_spi.h"
#include "drv_comm.h"
int main(void) {
    CommDev_Usart_t uart1;
    CommDev_Spi_t spi1;
    CommDev_t* devs[]={(CommDev_t*)&uart1, (CommDev_t*)&spi1};
    
    CommDev_UsartInit(&uart1, USART1, 115200);
    CommDev_SpiInit(&spi1, SPI1, 10000000);
    
    comm_send(devs[0], NULL, 0);//使用USART发送
    comm_send(devs[1], NULL, 0);//使用SPI发送
    comm_ctrl(devs[0],DISABLE);// 关闭USART
    comm_ctrl(devs[1],DISABLE);// 关闭SPI
    return 0;
}
```

通用接口会根据不同的实例对象执行不同外设的实现接口，**所有的细节隐藏在具体的接口实现中**；芯片更换后只需改写底层实现，不需要改动上层应用代码，这就是多态的威力！

### 5. 与传统编程方式对比

#### 5.1 UART实现

```c
int uart_init(const char *port, uint32_t baud);
int uart_open(void);
int uart_close(void);
int uart_write(const uint8_t *buf, size_t len);
int uart_read(uint8_t *buf, size_t len);
```

#### 5.2 SPI接口实现

```c
int spi_init(int cs_pin);
int spi_open(void);
int spi_close(void);
int spi_write(const uint8_t *buf, size_t len);
int spi_read(uint8_t *buf, size_t len);
```

调用层必须显式调用不同外设的函数，无法统一。

### 6. 使用多态方式改写文章开头的代码

使用上述多态结构代码改写文章开头的外设驱动代码后：

1.使用SPI接口的设备驱动代码：

```c
void W25QXX_Read(u8* pBuffer,u32 ReadAddr,u16 NumByteToRead)   
{                                               
    /*
    W25QXX_CS=0;                            //使能器件  
    u16 i;
    SPI1_ReadWriteByte(W25X_ReadData);         //发送读取命令   
    SPI1_ReadWriteByte((u8)((ReadAddr)>>16));  //发送24bit地址    
    SPI1_ReadWriteByte((u8)((ReadAddr)>>8));   
    SPI1_ReadWriteByte((u8)ReadAddr);   
    for(i=0;i<NumByteToRead;i++)
    { 
        pBuffer[i]=SPI1_ReadWriteByte(0XFF);   //循环读数  
    }
    W25QXX_CS=1;
    */
//使用统一接口改写    uint8_t command[] = {W25X_ReadData, (u8)((ReadAddr)>>16), (u8)((ReadAddr)>>8), (u8)ReadAddr}
    pdev_spi->cs_enable();//调用专属方法：使能片选
    comm_send(pdev_spi, command, sizeof(command));//使用共用接口发送数据
    comm_recv(pdef_spi, pBuffer, NumByteToRead);//使用共用接口接收数据
    pdev_spi->cs_disable();//调用专属方法：释放片选
}  
```

2.使用USART的设备驱动代码示例：

```c
if(u8_SendLength != 0)
{
    CheckSum = CRC_Check(&u8_SendBuf[1],(u8_SendLength-1));        
    u8_SendBuf[u8_SendLength] = (uint8_t)(CheckSum>>8);
    u8_SendBuf[u8_SendLength+1] = (uint8_t)(CheckSum&0xff);
    //USART_Send_Buf(u8_SendBuf,(u8_SendLength + 3));    
    comm_send(pdev_usart, u8_SendBuf, u8_SendLength + 3);//使用USART发送    
    u8_SendLength = 0;
}
```

两种设备调用统一的通信接口，实现与底层芯片外设代码解耦，更换芯片时不用修改上层外部设备的驱动代码。

## 三、两种编程方式对比

| 特性     | 传统结构化编程                | 多态与继承接口                        |
| -------- | ----------------------------- | ------------------------------------- |
| 接口形式 | 每个外设独立函数集            | ✅统一接口结构体（CommVtb_t）          |
| 调用方式 | 必须知道具体函数名            | ✅统一调用方式（comm_send、comm_recv） |
| 扩展性   | 新增外设需重写函数集          | ✅新增外设只需创建新实例               |
| 可移植性 | 换芯片需修改调用层            | ✅换芯片只需替换底层实现               |
| 代码冗余 | UART、SPI、I2C 等函数签名重复 | ✅接口统一，减少冗余                   |
| 抽象层次 | 面向硬件寄存器                | ✅面向接口抽象                         |

## 四、工程实践注意事项

在实际工程中，除了接口抽象，还需要注意以下几点：

- 内存管理：
    - 使用 malloc 分配的上下文必须在 close 或 destroy 时释放，避免内存泄漏。
    - 建议在接口中增加 destroy 函数，统一管理生命周期。
- 句柄所有权：
    - 统一由驱动模块管理句柄，避免外部随意 free。
    - 可以通过 opaque handle（不透明句柄）进一步隐藏内部结构。
- 接口注册中心：
    - 在大型项目中，可以设计一个 驱动注册表，通过名字查找并加载对应驱动。
    - 例如：driver_find("uart1", &ops)，实现统一调度。
- 错误处理与健壮性：
    - 所有接口函数应返回错误码，避免调用层因硬件异常崩溃。
    - 建议定义统一的错误码枚举，例如 DRV_OK、DRV_ERR_TIMEOUT、DRV_ERR_BUSY。
- 可移植性与跨平台：
    - 将硬件相关部分隔离在实现文件中，公共接口保持一致。这样在更换芯片或平台时，只需替换实现文件。
- 性能与优化：
    - 对于高性能场景，可以在接口内部使用 DMA、零拷贝等优化手段。接口层保持抽象，优化细节由具体实现决定。

## 五、总结

通过 UART 与 SPI 的对比可以看到：

- 传统方式：简单直接，但扩展性差，代码紧耦合，平台迁移成本高。
- 多态接口：统一接口抽象，调用层无需关心具体外设类型，真正做到 换芯片不怕，驱动可插拔。

这种方法不仅是技术上的优化，更是编程思维的升级：从“面向硬件”转向“面向接口”，让嵌入式系统开发更高效、更优雅、更工程化。

以上就是本篇文章的全部内容，如果对你有帮助，欢迎点赞转发推荐，有疑问的欢迎在评论区留言。