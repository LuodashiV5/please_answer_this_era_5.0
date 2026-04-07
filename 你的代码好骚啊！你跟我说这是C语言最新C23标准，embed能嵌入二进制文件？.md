

# 源码中直接嵌入外部文件：C23终于知道我当年吃的苦啦？

**在C源码中直接嵌入外部文件**这一提案，前后历经**整整5年**的讨论与打磨，最终在2022年7月的投票中通过，正式确定纳入C23标准。

关于该特性的详细解读可查看 [这篇文章] (https://thephd.dev/finally-embed-in-c23#)。简单来说，该特性为C语言提供了**唯一的、标准化的官方方案**，让开发者可以直接在源代码中嵌入任意外部文件的内容，无需借助第三方工具或自定义宏实现：

![C23文件嵌入语法示例](https://mmbiz.qpic.cn/mmbiz_png/43Iiaw2PwY0wcicB8OwGpUIFwTOibUSrywwdySERPWic2pVf1DT2WZvgkYI3vlyLhxfO3PM96C6wAyD5qdh1LzcoBA/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)

# 为什么这个需求要吵 5 年？

如果你写过固件、驱动、嵌入式、或者任何“单文件多资源可执行程序”，你一定做过这些事：

- 把图片、字体、音频塞进程序
    
- 把 HTML / CSS / JS 打包进二进制
    
- 把 FPGA bitstream 编进 ELF
    
- 把默认配置、许可证嵌入可执行文件
    

**这些应用的实现都没有统一方法。**

**支持派**认为现有的 xxd 或链接器方法要么导致编译极其缓慢，因为存在处理巨大的数组，要么不具备跨平台通用性。

**反对派**认为预处理器应该只处理“文本”，不应该去读取“二进制文件”。他们担心这会增加编译器的复杂性，并引入由于文件编码、路径搜索等带来的各种边缘问题。

## 过去大家是怎么干的？

方案五花八门，但都有问题：

| 做法                              | 问题                   |
| ------------------------------- | -------------------- |
| `xxd -i  / 自写脚本                 | 构建链路复杂、不可移植          |
| `ld -r -b binary`               | 地址可能不对齐，需要调用者移动到对齐位置 |
| `objcopy --binary-architecture` | 完全依赖工具链              |
| `.incbin`                       | GNU 汇编私货             |
| 链接脚本                            | 平台强相关                |

xxd 将二进制转成数组。

```
$ echo abc > /tmp/a.bin
$ xxd -i /tmp/a.bin  > /tmp/a.h
$ cat /tmp/a.hunsigned char _tmp_a_bin[] = {  0x61, 0x62, 0x63, 0x0a};unsigned int _tmp_a_bin_len = 4;
```

ld 将二进制打包到对象文件，并生成符号标签

```
echo abc > a.bin
echo def > b.bin
ld -r -b binary -o bin.o a.bin b.bin
gcc -c main.c -o main.o
gcc main.o bin.o -o a.out -Wl,-z,noexecstack
```

main.c

```c
#include <stdio.h>
extern char _binary_a_bin_start[];
extern char _binary_a_bin_end[];
extern char _binary_b_bin_start[];
extern char _binary_b_bin_end[];
int main() {    
	size_t a_size = (size_t)(_binary_a_bin_end - _binary_a_bin_start);    
	for (size_t i = 0; i < a_size; i++) 
	{        
	putchar(_binary_a_bin_start[i]);    
	}    
	return 0;
}
```

main.c引用的4个变量来源于bin文件，ld在创建bin文件时，自动生成_binary_xxx_end地址参数。

![Image](https://mmbiz.qpic.cn/mmbiz_png/43Iiaw2PwY0yzdgKib4321Xej5HJpTJ0OpHyI0bKeUZRlK8gevAKnVdatUICCsvWocGU70iaGUFM2xdTM2mztPoAw/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=2)

这些方案都**能用**，但没有一个是 **C 语言标准的一部分**。

而 **#embed** 的出现，并不是“发明新能力”，而是：

> **终于把一个用了几十年的现实需求，写进了标准。**

  

# #embed 到底是什么？

这是最容易被误解的一点，它不是 `#define` 那样的宏文本替换。 `#embed` 和 `#include` 一样，发生在 **预处理阶段**，但行为完全不同：

| 对比           | `#include` | `#embed` |
| ------------ | ---------- | -------- |
| 输入           | 文本         | 任意二进制    |
| 输出           | 预处理后的文本    | 整数常量     |
| 能嵌 WAV / PNG | ❌          | ✅        |
| 自动加 `\0`     | ❌          | ❌        |
| 是否标准         | C89 起      | C23      |

可以这样理解，#include 是“把文件当源码拼进来”，#embed

 是“把文件当数据塞进对象里”


# 直观感受embed的便捷

下面直接上实验，嵌入一个wav文件到可执行文件。

- 用 Python 生成一个会发音“多-瑞-米”的 doremi.wav
    
- 用 C23 #embed 把它嵌进程序、再原样拷贝生成 out.wav
    
- 最后再比较doremi.wav和out.wav的MD5
    
在我的环境中：

- clang-19：支持 -std=c23，#embed 可用
    
- gcc-12：-std=c2x 尚不支持 #embed
    
```
`all:`    
	`# python 生成 doremi.wav`    
	`./build-wav.py`    
	`# 拷贝`    
	`clang-19 -std=c23 copy.c -o copy`    
	`./copy`
```

  

```c
#include <stdio.h>
#include <stdint.h>
constexpr uint8_t doremi_wav[] = { #embed "doremi.wav" };
int main(void){
	FILE *f = fopen("out.wav", "wb");    
	fwrite(doremi_wav, 1, sizeof doremi_wav, f);    
	fclose(f);    
	puts("out.wav written from embedded data");
}
```

  

[C语言为什么还没灭亡，甚至推出新标准C23，静静地看着一代代“革命性语言”来了又去](https://mp.weixin.qq.com/s?__biz=Mzg3MTU0NjEzOQ==&mid=2247489270&idx=1&sn=5496aa396acced5eb05187e339b4a326&scene=21#wechat_redirect)

- 冲鸭鸭鸭鸭冲鸭鸭鸭鸭

    支持一下运算符重写吧。这样就好做q格式和接口了。要不然写的麻烦代码也丑

    你做dsp领域的？现在dsp还有生存空间吗？现在的主流处理器内部带有dsp和浮点运算呀

    回复 **小米吃饱**：要么精度尴尬，要么计算费劲。不如q格式来的稳定。比如我们有项目需要用到q52，哪个都不好搞，如果可以在某些情况下重写加减乘除法，并且让其局限在某几个文件中。我们代码可以写的比较好看
    
    回复 **小米吃饱**：dsp当然有生存空间，不过随着mcu功能越来越多，逐渐两者会越来越没有边界的

    回复 **冲鸭鸭鸭鸭冲鸭鸭鸭鸭**：如果仅为了q格式编写得漂亮，可以用python编写预处理，识别q变量，转换q变量运算符到宏，生成新的c代码（a.c --> a.q.c）最后编译a.q.c，参考qt得扩展c++语法，用moc预编译h文件的流程

    回复 **程序员写个解**：阿卡姆剃刀法则啊
    
    回复 **冲鸭鸭鸭鸭冲鸭鸭鸭鸭**：差不多，反正你需求也不算太高，刚刚用AI写了一个，局部变量勉强能用
    
    回复 **程序员写个解**：一股qt的味道啊![😂](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAQAAAC1HAwCAAAAC0lEQVR42mNkYAAAAAYAAjCB0C8AAAAASUVORK5CYII=)，这也是个好办法，就是麻烦了点
    
    
    本质就是帮你做了把二进制文件转数组这个过程。以前塞网页进去的都干过这活，脏累而且代码看起来极其恶心
    
    软件工程“泔水”
    
    二进制文件直接嵌入图片，网页，二进制代码，这很简单啊，天天干这活，为啥觉得很奇怪？那写个小工具把文件直接转化为一个源文件，里面一个大数组保存文件不就行了？
    
    没用过ucgui、或没搞linux的绝对不知道怎么干，我搞过mfc几年，文件嵌入全由IDE处理，本来属于选修，c23标准有embed嵌入文件就是必修课

    让你改资源文件你不就炸了？
    
    回复 **但泽**：重新转化编译，生成新内核下载升级
    
    回复 **程序员写个解**：这样都要必修

    常量大数组
    
    还要升级编译器
    
    确实骚
    
    对于嵌入式开发来说很好用的功能
    
    这不是微软干过的rc？

    你指的是vc++的 .rc文件？功能一样，机理没了解过
    
    回复 **程序员写个解**：对 微软几十年前玩过 现在才慢吞吞变成标准
    
    早该支持了，之前搞字库贼麻烦，C语言更新还是太谨慎了
    
    今天看了个标题PureBasic还是更新Baisc语法扩张
    
    需要用open才能读？ 那裸机是不能用了
    
    程序员写个解
  
    这是编译阶段，不需要open，裸机可用
    
    编译进去了，对程序员来说，和extern一个外部的数组没区别
    
    回复 **Enigma**：是相当于一个const 数组了吧？
    
    我用ld+“链接脚”本还行
    
    太麻烦了，还要算位置和空间

    [@元宝](https://mp.weixin.qq.com/s?__biz=Mzg3MTU0NjEzOQ==&mid=2247489647&idx=1&sn=782b9f9b97e2baa04b35e06cefee5eff&key=daf9bdc5abc4e8d0b5f39b386e024579ff7ee92c74965990d64a31e570526c6ed2f48a8f88e113cb8924211052ceb461996e02f32b7ffa32ac11ab633c432146d0fe9b4a8bfebf2ab91c58dbc3b5c3883a672377f811e325ba8446ff5d63088139978fcc19686aaf425d3db192aeee6baccc57f4f1f461759e4f8150976cdad6&ascene=0&uin=OTEzOTQ0NDYy&devicetype=UnifiedPCWindows&version=f254181d&lang=zh_CN&countrycode=CN&exportkey=n_ChQIAhIQkXE6iCsWHGvimJnBS9o01xLoAQIE97dBBAEAAAAAAEYhE0dlVoUAAAAOpnltbLcz9gKNyK89dVj0Bsi%2BPj0isd2xGLuaQATA%2B%2BDfIy78STnEHiEei06kr9lrAv9%2BdppUBv%2F%2BT1jhMYTfXFLViG6VwROOQrgsbk17FBtNCtyQnIu27F1UTSoTR3i4UP7QHlLlBn%2BGd6zF27n289fidHB54GFTWglKNTdEYIqhOz%2FtlmZUL8ta3MpE66K1E3BWL1J%2FoV7ciR7c1YuaR4ORvDR0tgNJIdXNyAK4Osk8dGSF2OFkmbWyl6yA7glQuvDsD5Loh6ZVcrmWXz%2BbhKU%3D&acctmode=0&pass_ticket=42UnEi2Z9XIPzO109FFx1ckXBODc0XmMocChyiId3H3f%2BPORv5vt%2BTZ2UInZ0OXm&wx_header=0)除此之外c23还有什么更新亮点
    
    C23实用更新：二进制字面量(0b)和数字分隔符(')让位操作一目了然；typeof助力泛型宏设计；nullptr终结空指针隐患；#elifdef/#elifndef简化跨平台编译；_Decimal64满足金融计算精度需求。这些特性既保留了C的高效本质，又大幅提升了开发体验。
    
    
    回复 **元宝**：你不是被微信屏蔽了吗哈哈哈哈哈，自己人打自己人，腾讯内部管理怕是已经失控了
    
    带个小型文件系统,比如littlefs，通过串口或USB管理内部或外部flash的资源，可能会占用一些mcu内部的资源，但是这样比直接嵌bin或转数组方便很多吧？
    
    在没有文件系统的场景嵌入资源文件比较方便。有文件系统的不如直接文件操作，另外多进程embed同一个文件是不是不能共享调用
    
    c++能用吗
    
    以前要用个什么工具转成数组。
    
    std::embed也快点啊
    
    c要是能有显式的尾调用就好了，这样就可以安心的搞一搞c的函数式编程了。
    
    ---
    # C语言为什么还没灭亡，甚至推出新标准C23，静静地看着一代代“革命性语言”来了又去

原创 吴解君 程序员写个解

 _2026年1月16日 07:00_ _广西_ 19人

    一门语言能活半个世纪，要么是博物馆里的化石，要么是地基里的钢筋。  
C语言显然是后者，它不构建花哨的楼阁，而是深埋进操作系统与硬件之间，做那根控制内存布局和IO时序的承重梁。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/43Iiaw2PwY0wcicB8OwGpUIFwTOibUSrywwpxXxHQ5Libo7Ria25LjFiacEzzy6svTD7YkXnRBaRYqIoebplkQAISHeg/640?wx_fmt=jpeg&tp=wxpic&wxfrom=5&wx_lazy=1#imgIndex=0)

# 等等…谁还在用C语言？

如果你是个新生代程序员，泡在Python生态的API Boy，你确实会纳闷这都2025年了，谁还写C啊？这玩意儿不是老古董吗？

但现实是**写C的人比你以为的多得多**。

而且不只是那些留着大胡子、穿着BSD、GitHub T恤的大佬，从你兜里的手机到墙角的智能插座，C语言在一直都活着，新语言的后浪依旧每拍死50岁的C语言。

因为它不考虑是否更优雅、更短小的实现功能，不考虑工程师的发量多少，它只关系机器是怎么运作的，50年来依旧如此。

  

# C23 核心升级亮点

C23标准发布时有人说这是“王者归来”，要我说王者压根没走过。它只是偶尔翻个身告诉世界自己还醒着。

一门50岁的语言，还能折腾出什么新花样？

看看C23都加了些什么，它引入了auto关键字做类型推导，有了nullptr，甚至支持了constexpr编译期常量，看起来像是在向C++靠拢。

基于 `ckd_add`、`ckd_sub` 等函数的**安全整数运算** ，不错，终于有官方的溢出检查了，多少漏洞是因为整数溢出埋下的，现在总算有个标准做法。

基于 `char8_t` 类型的**原生UTF-8编码支持**，世界不是ASCII码的，C语言终于承认了这一点，**全球化不是可选，是必须**，今后连嵌入式设备都得显示中文了。

基于 `memset_explicit()` 函数的**显式内存清零机制**，这个函数告诉编译器”别自作聪明优化函数"。

```c
#include <stdckdint.h>
#include <stdbool.h>
#include <uchar.h>
int main(void) {    
int x;    
if (ckd_add(2147483640, 100, &x)) {        
// 检测到整数溢出，终于不用自己手写检查了，虽然手写也没多难    
}    
int mask = 0b1010'1100;    // 统计掩码中 1 的位数，这不是python才有的待遇吗    
int ones = stdc_count_ones(mask);}
```

# C语言仍是与硬件交互的语言

新语言标榜自己“更抽象、更安全”，但与操作系统有关系吗？

**Linux、RTOS用C，不是因为历史包袱，而是因为没得选**。你要在启动时初始化页表，要在中断处理里保存寄存器状态……C语言只需要10行以内就可以构建 **“运行时”**（设置堆栈，PC指针），而其他语言的“运行时”如何创建？

所以它的可移植性是天生存在，50年来每个芯片架构诞生时，第一个该适配的软件永远是C编译器。是芯片厂家证明流片成功的Hello Word。

![图片](https://mmbiz.qpic.cn/mmbiz_png/43Iiaw2PwY0wiaE9giaCW4UMdHNBBJvUNmtqgekjsSvONibKApgXXtm8ib7wIosaXOYRPCKYs9oM7ecAx5jiah5wYQOg/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1#imgIndex=1)

C之所以不死是它本来就不活在时髦清单里，它活在机器与人的夹缝中，静静地看着**一代又一代“革命性语言”来了又去**。