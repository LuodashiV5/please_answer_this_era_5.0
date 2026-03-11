
来看看libevhtp这个高性能HTTP服务器库中，用到的宏高级技巧。

## 1. 分支预测优化

现代CPU都有分支预测器，一旦预测错误，流水线得全部冲掉，性能瞬间暴跌。libevhtp用`__builtin_expect`给编译器提个醒。

### 1.1 likely/unlikely宏

![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/K62vJYPSiaghmBJRLniauW9HiaVlKtyNFhbBoad5ibkcajsfmV6xwveZaOzMK6LpfpQiciaROwMLiauuusicfib1iclqyUkm5CNt4ia8wCVDNxC5iaMkqNc/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

关键在这个`!!(x)`的双重否定：

> int x = 5;
> !x      // 0 (false)   
> !!x     // 1 (true)  - 把任意值规范化为0或1

`__builtin_expect(!!x, 1)`告诉编译器："这个条件大概率是true"。

### 1.2 错误处理中的应用

错误处理代码99%的时间都不会执行，用`unlikely`能显著提升性能：

![Image](https://mmbiz.qpic.cn/mmbiz_png/K62vJYPSiagia42Fiah36E6gD6ycaFZGpLMG74oWXQ5beKNqFVQXx8OqI0mbVdlUdxyQXIsR4h3uqhgHrTpZjOfs33hvibhm1ibuLiaSFBYdWjzPU/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)

编译器会把"unlikely"的分支移到函数末尾，让主路径保持紧凑，提高指令缓存命中率。

### 1.3 跨平台兼容性

注意看`#else`分支，不支持`__builtin_expect`的编译器上，宏会退化成普通判断。功能不受影响，只是少了优化。一份代码，多种实现，这就是宏的魅力。

## 2. Token拼接（##）

`##`操作符能把两个token粘在一起，让我们在编译期就生成代码，效果堪比C++模板。

### 2.1 命名规范的统一

libevhtp有个设计很讲究，hook回调函数和它的参数总是成对出现：

![Image](https://mmbiz.qpic.cn/mmbiz_png/K62vJYPSiagjMZs13j8c0wiaDw5pibqiasc9jGbpIY1icIK1U9l4eaye9UDetWic01pXia37ibzMeyTH14TN2NcJSFkFIpMicJq8NWRtuzibLGf01HCjk/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=2)

每次都手写这两个字段，代码得累死。用`##`自动拼：

```c
#define HOOK_ARGS(var, hook_name) \    var->hooks->hook_name##_arg
```

看看展开效果：
```c
HOOK_ARGS(request, on_headers)  →  request->hooks->on_headers_arg
HOOK_ARGS(request, on_path)     →  request->hooks->on_path_arg
HOOK_ARGS(request, on_read)     →  request->hooks->on_read_arg
```

一个宏，所有hook都适用。这种命名规范统一起来，维护代码轻松多了。

### 2.2 简化深层访问

libevhtp还用这招简化深层结构体的访问：
```c
/* rc == request->conn. 简化深层访问 */
#define rc_scratch  conn->scratch_buf
#define rc_parser   conn->parser

/* ch_ == conn->hooks->on_... */
#define ch_fini_arg hooks->on_connection_fini_arg
#define ch_fini     hooks->on_connection_fini

/* cr_ == conn->request */
#define cr_status   request->status
#define cr_flags    request->flags
#define cr_proto    request->proto
```

代码里就能这样写：
```c
// 原本要写：  
if (request->conn->request->status == 200) { ... }      
// 简化后：   
if (cr_status == 200) { ... }
```
不仅简洁，更关键的是将来结构体改了，只需要改宏定义，业务代码一行不用动。

### 2.3 函数名自动生成

在数据结构库中，`##`还能生成完整的函数名。libevhtp的tree.h里就有这样的用法：

```c
#define RB_INSERT(name, x, y)   name##_RB_INSERT(x, y)   
#define RB_REMOVE(name, x, y)   name##_RB_REMOVE(x, y)   
#define RB_FIND(name, x, y)     name##_RB_FIND(x, y)   
#define RB_MIN(name, x)         name##_RB_MINMAX(x, RB_NEGINF)   
#define RB_MAX(name, x)         name##_RB_MINMAX(x, RB_INF) 
```

使用时：

```c
RB_HEAD(test, node) head;      
// 自动生成：test_RB_INSERT, test_RB_FIND 等函数   
RB_INSERT(test, &head, new_node);   
node_t *found = RB_FIND(test, &head, key);   `
```
这就是编译期代码生成，每个树类型都有独立的函数集，类型安全，零运行时开销。

## 3. 可变参数宏：##__ VA_ARGS__

C99引入了可变参数宏，但真正好用的是GNU的`##__VA_ARGS__`扩展。它能自动处理空参数，这在日志系统中简直是救命稻草。

### 3.1 ##的吞逗号魔法

先看libevhtp的日志是怎么实现的：

```c
#if !defined(EVHTP_DEBUG)  
#define log_debug(M, ...)  
#else  
#define log_debug(M, ...) \  
    fprintf(stderr, __log_debug_color("DEBUG") " " \  
            "%s/%s:%-9d" M "\n", \  
            __FILENAME__, __FUNCTION__, __LINE__, ##__VA_ARGS__)  
#endif
```
关键在这个`##__VA_ARGS__`。为什么要用`##`？看两个调用：
```c
log_debug("Connection established");          // 没有参数
log_debug("Received %d bytes", bytes_read);   // 有参数
```
如果没有`##`，第一行会展开成：

```c
fprintf(stderr, "DEBUG %s/%s:%-9d" "Connection established" "\n",          
	__FILENAME__, __FUNCTION__, __LINE__, );  // 注意最后多了个逗号！   `
```
这会直接编译错误。`##__VA_ARGS__`的妙处就在这：当`__VA_ARGS__`为空时，它会自动把前面的逗号吃掉：
```c
// 有参数时：   
fprintf(stderr, "..." "\n", file, func, line, bytes_read);
// 无参数时：  
fprintf(stderr, "..." "\n", file, func, line);  // 逗号消失了!
```

### 3.2 嵌套可变参数宏

更骚的操作是嵌套使用，把可变参数一层层传下去：

![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/K62vJYPSiagh9FvlXHiayhNKuvOtGJDmvy740BxqnZfv7VmZcGLQ3JpJQCBBIhNDqAcNsXKpKib4gDkbHsBahoIcCeO4yo2gaeOlAQItEiaOfqM/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=3)

这个宏接受可变参数，然后原样转发给函数调用。这招让宏能适配任意数量的参数，是C语言泛型编程的基石。
看看效果：

```c
// 0个额外参数
HOOK_REQUEST_RUN(req, on_headers_start);

// 1个额外参数
HOOK_REQUEST_RUN(req, on_header, header);

// 2个额外参数
HOOK_REQUEST_RUN(req, on_read, buffer, length);
```
一个宏，所有场景通吃。

## 4. 字符串化（#）

`#`操作符能把宏参数转成字符串，这是C语言"编译期反射"的关键。

### 4.1 断言信息自动生成

标准的`assert`只告诉你挂了，但不告诉你为啥挂。libevhtp用`#`把条件表达式也打出来：
```c
#define evhtp_assert(x) \
    do { \
        if (evhtp_unlikely(!(x))) { \
            fprintf(stderr, "Assertion failed: %s (%s:%s:%d)\n", \
                #x, __func__, __FILE__, __LINE__); \
            fflush(stderr); \
            abort(); \
        } \
    } while (0)
```

看展开效果：
```c
evhtp_assert(conn != NULL);
// 展开后：
if (!(conn != NULL)) {
    fprintf(stderr, "Assertion failed: %s (%s:%s:%d)\n",
            "conn != NULL",  // #x 自动转成字符串
            __func__, __FILE__, __LINE__);
    abort();
}
```
错误信息直接包含源代码，调试时一眼就知道哪出问题了。

### 4.2 带格式化的断言
更进一步，把`#`和可变参数组合起来：
```c
#define evhtp_assert_fmt(x, fmt, ...) \
    do { \
        if (evhtp_unlikely(!(x))) { \
            fprintf(stderr, "Assertion failed: %s (%s:%s:%d) " fmt "\n", \
                #x, __func__, __FILE__, __LINE__, __VA_ARGS__); \
            fflush(stderr); \
            abort(); \
        } \
    } while (0)
```

使用：
```c
evhtp_assert_fmt(len < MAX_BUF_SIZE,                     
	"Buffer overflow: len=%zu, max=%zu", len, MAX_BUF_SIZE);   `
```

输出：
```c
Assertion failed: len < MAX_BUF_SIZE (process_data:evhtp.c:1234)        Buffer overflow: len=8192, max=4096   `
```
既有条件表达式，又有具体数值，定位问题快多了。

### 4.3 编译期文件名优化

==__FILE__== 会包含完整路径，在嵌入式系统里太浪费ROM了。libevhtp有个巧招：

```c
#define __FILENAME__ \    
	(strrchr(__FILE__, '/') ? strrchr(__FILE__, '/') + 1 : __FILE__)
```

这是一个常量表达式，编译器会在编译期计算出结果：
```c
// __FILE__ = "/home/LinuxZn/project/src/evhtp.c"
// __FILENAME__ = "evhtp.c"
```
因为是常量表达式，编译器会在编译期算好，最终二进制里只有文件名，完整路径被优化掉了。嵌入式系统每个字节都金贵，这招能省不少空间。

## 5. 总结

掌握这些宏技巧，能一定程度帮助我们写出高性能又好维护的嵌入式代码。记住一点：能用内联函数就用内联函数，只有宏能解决的场景才上宏。宏虽强大，但别滥用。