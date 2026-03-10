大家好，我是bug菌~
过完元宵，这年是真的过完了，假期时间也是过得一溜烟，跟几个同事聊天都带着满满负能量，状态慢慢找吧~![](https://res.wx.qq.com/t/wx_fed/we-emoji/res/assets/newemoji/2_05.png)
今天给大家分享一个MCU中非常有意思的玩法 ： 模块单独编译下载并固化到flash中，以后就只要烧录应用程序并调用位置上的函数即可。
我相信你肯定遇到过这样的两种场景:
第一种场景 : 程序工程很大，下载又比较慢，每次修改少量代码都需要全量编译和下载，非常影响调试效率，如果只要更新部分模块烧录文件就好了。
第二种场景：你希望把核心的代码保护起来，只是提供给第三方开发者以编译好的二进制使用，同时保护知识产权，无需他们了解细节。
那么bug菌就还是以经典的STM32为例，介绍如何在MDK开发环境下，通过分散加载文件（scatter file）和库文件的方式，实现代码模块的分离与复用。
1
准备烧录lib工程
以STM32为例，使用STM32CubeMX生成基础MDK工程包含基本的串口初始化和时钟配置，并编写好CRC相关的代码，下面是一个简单的示例
**mycrc32.h**
```c
#ifndef __CRC32_H
#define __CRC32_H

#include <stdint.h>

/* CRC32 标准多项式 */
#define CRC32_POLY    0x04C11DB7

/* 函数声明 */
void mycrc32_init(uint32_t *crc_table);
uint32_t mycrc32_calculate(uint32_t crc, uint32_t *crc_table, uint8_t *data, uint32_t len);

#endif
```
**mycrc32.c**
```c
#include "mycrc32.h"

/**
  * @brief  生成CRC32查表法所需的表格
  * @param  crc_table: 长度为256的uint32_t数组指针
  * @retval 无
  */
void mycrc32_init(uint32_t *crc_table)
{
    uint32_t c;
    int i, j;
    
    for (i = 0; i < 256; i++) {
        c = (uint32_t)i << 24;
        for (j = 0; j < 8; j++) {
            if (c & 0x80000000) {
                c = (c << 1) ^ CRC32_POLY;
            } else {
                c = (c << 1);
            }
        }
        crc_table[i] = c;
    }
}

/**
  * @brief  计算CRC32值
  * @param  crc: 初始值，通常为0xFFFFFFFF
  * @param  crc_table: 由crc32_init生成的表格
  * @param  data: 待计算数据指针
  * @param  len: 数据长度
  * @retval 计算后的CRC32值
  */
uint32_t mycrc32_calculate(uint32_t crc, uint32_t *crc_table, uint8_t *data, uint32_t len)
{
    uint32_t i;
    uint8_t index;
    uint8_t *pdata = data;
    
    for (i = 0; i < len; i++) {
        index = (uint8_t)((crc >> 24) ^ (*pdata));
        crc = (crc << 8) ^ crc_table[index];
        pdata++;
    }
    
    return crc;
}
```
当然我们还是需要验证下CRC算法的正确性，代码有问题到时候肯定没法得到正确的CRC校验计算结果了，所以以后对于模块化的代码也尽可能成熟和稳定以免调试问题等等带来不少困难。
```c
/* CRC测试数组 */
uint8_t test_data[] = "STM32 CRC32 Test";
uint32_t crc_table[256];
uint32_t crc_result;

/* 测试函数 */
void crc_test_demo(void)
{
    /* 初始化CRC表 */
    mycrc32_init(crc_table);
    
    /* 计算CRC32 */
    crc_result = mycrc32_calculate(0xFFFFFFFF, crc_table, test_data, sizeof(test_data)-1);
    crc_result ^= 0xFFFFFFFF;  /* 标准CRC32最后需要取反 */
    
    printf("myCRC32 result: 0x%08X\r\n", crc_result);
}
```
根据输出结果，可以与标准的CRC工具进行结果对比。
2
分散加载并烧录
生成的模块代码需要被主程序调用，那我们总要知道程序烧录到哪里了，此时我们就需要把这些函数指定放在某个我们预先分区的位置，并且还需要知道他们的地址，此时就需要在工程中修改分散加载文件。
如下是一个分散加载文件直接告诉了我们程序的内存分区情况和程序链接情况，我们准备将mycrc算法固定在flash的0x08020000  
```c
LR_IROM1 0x08000000 0x00020000 {    ; 主程序区，128KB
  ER_IROM1 0x08000000 0x00020000 {
    *.o (RESET, +First)
    *(InRoot$$Sections)
    .ANY (+RO)
  }
  RW_IRAM1 0x20000000 0x00005000 {  ; RAM区，20KB
    .ANY (+RW +ZI)
  }
}

LR_CRC 0x08020000 0x00020000 {      ; CRC算法专用区，128KB
  CRC_REGION 0x08020000 {
    mycrc.o (+RO)                      ; 将mycrc.c编译生成的目标文件放到此区域
  }
}
```
编译完成后，可以通过程序生成.map文件中找到暴露给别人的接口函数放到的具体为位置,查找mycrc32init和mycrc32_calculate的地址，应分别位于0x08020000和0x080200XX附近。
下载程序后，这两个函数就固化在了指定的Flash区域,只要你不擦除，代码会一直存放在此处，其它程序都能够调用该函数。
3
应用程序如何用？
在新的工程中，我们不再需要mycrc.c了，但需要一个“跳转表”来调用固化好的函数，如:
**crc_call.h**
```c
#ifndef _CRC_CALL_H
#define _CRC_CALL_H

#include <stdint.h>

// 函数指针声明
typedef void (*crc_init_func)(uint32_t, uint32_t*);
typedef uint32_t (*crc_calc_func)(uint32_t, uint32_t*, void*, int);

// 调用接口
void mycrc32_init_call(uint32_t poly, uint32_t *crc_table);
uint32_t mycrc32_calc_call(uint32_t crc, uint32_t *crc_table, void *input, int len);

#endif

```
**crc_call.c**
```c
#include "crc_call.h"

// 固化函数的绝对地址（需与分散加载文件中定义一致）
#define CRC_INIT_ADDR    (0x08020000)
#define CRC_CALC_ADDR    (0x08020054)  // 实际地址请查看第一次工程的map文件

// 函数指针转换
#define CRC_INIT_FUNC    ((crc_init_func)CRC_INIT_ADDR)
#define CRC_CALC_FUNC    ((crc_calc_func)CRC_CALC_ADDR)

void mycrc32_init_call(uint32_t poly, uint32_t *crc_table)
{
    if (CRC_INIT_FUNC != NULL) {
        CRC_INIT_FUNC(poly, crc_table);
    }
}

uint32_t mycrc32_calc_call(uint32_t crc, uint32_t *crc_table, void *input, int len)
{
    if (CRC_CALC_FUNC != NULL) {
        return CRC_CALC_FUNC(crc, crc_table, input, len);
    }
    return0;
}
```
这样我们在新的工程中将`crc_call.c/h`添加到工程中,在main函数中直接调用，比如
```c
#include "crc_call.h"
#include <stdio.h>

uint32_t crc_table[256];

int main(void)
{
    // 初始化串口等外设...省略
    
    mycrc32_init_call(0x4C11DB7, crc_table);
    uint32_t crc = 0xFFFFFFFF;
    crc = mycrc32_calc_call(crc, crc_table, "123456789", 9);
    
    printf("myCRC from fixed code: 0x%08X\r\n", crc);
    
    while(1);
}
```
编译下载后，检查运行结果应与第一次工程是否一致，一致没有跑飞就表示成功了。
这样一来开发底层驱动、模块、算法的工作可以与应用程序开发工作分离，两个人专业的人分别去干专业的事情。
但这中间也有一些额外要注意的点：
1、比如前面提到的调试问题。
2、两部分程序的编译选项最好保持一致，避免调用约定不一致。
3、对于模块修改后编译可能存在map文件中各地址变化的问题，可以通过固定的跳转表进一步优化。
4、还有固化程序中如果用到一些私有ram还的进一步区分处理。
bug菌就只是在这里抛个砖了~
**最后**
      好了，今天就跟大家分享这么多了，如果你觉得有所收获，一定记得点个**赞**~