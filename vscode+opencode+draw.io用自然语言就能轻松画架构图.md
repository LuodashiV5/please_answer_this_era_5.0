# 写在前面

这两天遇到个头疼的问题：要画一个比较复杂的组网架构图。用ppt折腾了很久，始终不满意，最后想：“能不能借助AI来画图呢？”

嗯，准备用vscode+opencode+draw.io的组合尝试下，又经过一阵猛如虎的操作，竟然尝试成功了，特定写一篇文章记录下。

# 准备工作

1、**安装vscode**：这个就不多说了，应该是个写代码的基本上都知道怎么安装。  
2、**安装opencode插件**：这个我在之前写的《折腾了半天，总算把Open Code安装好了！》文章中有详细讲怎么安装，如果不会可以翻出来看看。有同学问我为啥什么都用opencode，其实主要是穷，不想花钱买token。opencode自带的免费模型完全满足我这些简单应用。

3、**安装draw.io**：这个稍微详细讲下，毕竟这货是今天的主角。  
在vscode的【扩展】里搜索【draw.io】，选择【Draw.io Integration】，并点击右边的【安装】，就安装好啦！说明下，因为我之前已经安装了，所以没有【安装】按钮。![图片](https://mmbiz.qpic.cn/mmbiz_png/kJgMlSXdCiceNviaJUVjuReA1lB7ibarRllDs7QicpZgX24ht9WEHM4VDvJbOYiaFkBFdSKI9ZUdMF9cTVniawuzb1Aun45UBl0YtQiatia8IIicF370/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=0)

# 开始画图

1、**创建画图目录**：在本地建一个存放画图文件的目录，比如D:\draw.io。  
2、**vscode打开画图目录**：打开vscode，打开D:\draw.io目录，打开opencode插件，创建画图文件，比如微服务架构的画图文件：architecture.drawio。![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/kJgMlSXdCicfqnMxiawIVWvHHddFUk02Vl5W9HSafHtNekQKBic85PCibibsiaahicoqHVheibfh867FxHGfYJicEj2vA8iaxGkrDgPicIpw0iaIyhtHMOk/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=1)

3、**输入画图需求**：开始在opencode的对话窗口写入画图需求，并指明写入D:\draw.io\architecture.drawio中。  
需求示例如下：

```txt
请作为一个系统架构师，为我生成一个微服务架构图的 draw.io XML 代码，放在D:\draw.io\architecture.drawio文件中。 
需求如下：   
- 包含用户端、负载均衡器 (Nginx)、API 网关。   
- 后端有三个服务：用户服务、订单服务、支付服务。   
- 数据库包括 MySQL 和 Redis 集群。   
- 使用 AWS 图标风格。   
- 请直接输出完整的、可被 draw.io 导入的 XML 代码块，不要包含 markdown 解释文字，以便我直接复制。 
```

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/kJgMlSXdCiceo01iaNhXibNX4zoEJYKx53iaB6DrNYWpfUA5Q0lszUh30woXE61SgSMSI9KbIzo1hWddzkusnahqj8ZXje1fGhUS1Vx86Rp4W3o/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=2)

这样就开始画图啦，这个要稍等一会儿。  
下面这个是已经画好的：![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/kJgMlSXdCicdxQ8pBWA4EvgGiaibWqyEG3IWdmrfE4r0hoHn5CvlQHNVfublI1WxZmO5WQCA4nLib0tjNunAegjEUKWk8fK3lmKYa3SBGIyfgFI/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=3)

这里有个问题，为啥画图板上是空白的？

别急，如果遇到这种问题，可以尝试关掉architecture.drawio文件重新打开试试。如果还是空的，则可以把问题抛给opencode，让它帮你解决。

如下是画出来的微服务组网架构图：![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/kJgMlSXdCicdg7FpTT9wf2tXaJCHiblxVwVvnB6ibbaT9RlSB8HL0nWsyOp0piczI1rBPaM2wdb6jwHLdfm0O2de7DkYd9XgiblOribZzcnicyWHhI/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=4)

如果对这个图的细节不满意，可以直接请opencode给你微调，比如：

```txt
1、请将最外层的框从圆角改成直角。 
 2、除了专有名词，其它的英文请改成中文。 
```

下面是微调后的结果：<u>最外层的框从圆角改成直角；部分英文请改成了中文</u>。 

下面是微调后的结果：![图片](https://mmbiz.qpic.cn/mmbiz_png/kJgMlSXdCicdrSblk1iaicyWyGwZwHhLoT8ABpAlQibiaMzvh5Psibic0DNJQQrCV6xAj0hCickOqYadZAiaiaicg6upulZ7ZZkXTpzKM95V68c6hhjvec/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=5)
  

# 写在最后

这篇文章记录了通过**vscode+opencode+draw.io**的组合画图的完整过程。我准备再尝试下写个skill，将一些基本的要求直接在skill中明确，比如：字体、中英文、配色、箭头样式等等。