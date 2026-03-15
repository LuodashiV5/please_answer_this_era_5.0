

你让AI帮你分析一份长合同。

AI告诉你：太长了，装不下，你分几次喂吧。

  

你让AI帮你审一整个代码项目。

AI告诉你：只能看一部分，你挑重要的给我。

  

你想让AI同时参考你的需求文档、竞品报告、会议纪要。

AI告诉你：一次放不下这么多，分四次聊吧。

  

这些问题的根源都一样。

AI一次能"看"的信息量有个上限，行话叫 上下文窗口 。

  

以前Claude的上限是200K Token。

Token是AI的计费单位，大约1.5个Token≈1个中文字，200K大约能装15万中文字。

听着不少，但真用起来很快就满了。

  

从今天开始，这个上限翻了5倍。

  

Claude Opus 4.6和Sonnet 4.6正式全面开放 1M上下文窗口 。

不是内测，不是等待名单，直接能用。

最关键的三个字： 不涨价 。

  

![Image|585](https://mmbiz.qpic.cn/mmbiz_png/2AGkuibewjDpZKzzTFYFibLfGnHeXAT6ToiaicIQSrxHiadJXoCuAibdlicoH6v0a8J4cbc5tDqeEFTABzdJCISf4WUONpiayh2AV8kJcgzKdZ1byHc/640?wx_fmt=png&from=appmsg&wxfrom=13&tp=wxpic&watermark=1#imgIndex=0)

  

有人可能会说：1M不是早就有了吗？

  

没错，之前确实有。

但那是 Beta 。

需要在API请求里手动加特殊参数才能开启，普通用户根本不知道怎么用。

  

而且Beta期间有 长上下文溢价 ——超过200K的部分，输入价格翻倍，输出涨50%。

等于你用1M的代价比用200K贵得多。

  

这次的变化是两件事同时发生：

1、Beta转正，所有用户直接可用，不需要任何额外操作

2、溢价全部取消，1M和200K完全同价

  

老金我看到这个消息第一反应是去翻了Anthropic（Claude的母公司）的定价页面。

确认了三遍。

  

![Image](https://mmbiz.qpic.cn/mmbiz_png/2AGkuibewjDqLolmK9g5XE7Vy8r6eU7nVZHHkNyJpALzpXE8eldgGbSAaI5aJVr3kmnMZsGEbGJf5pibjH2KKK9qfy2QdkCgO0ib9czg4pBZOM/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=1)

  

Opus 4.6还是$5/$25每百万Token，Sonnet 4.6还是$3/$15。

不管你用200K还是塞满1M，单价一分不变。

  

之前Beta期间用1M要多花一倍的钱，现在免费升级。

这也太实在了吧。。。

  

## 1M到底能装多少东西

先说结论。

  

1M Token ≈ 75万英文单词 ≈ 大约50万中文字 。

  

什么概念？

  

一本《哈利·波特》全集大概110万英文单词。

1M能装下大半套。

  

换到你日常工作的场景就更好理解了。

  

你是写书的，一整本10万字书稿的素材、提纲、已写章节，全部一次性丢进去让AI帮你找前后矛盾。

以前得切好几段喂，改完再拼接的时候前后语气对不上，那种感觉只有写过长文的人才懂。

  

你是做法务的，一份200页合同加上所有相关条款解读，一次性喂完让AI找风险点。

以前得自己切段落，AI看不到全貌经常断章取义，你还得跟在后面一段段纠正。

  

老金我自己是做产品的，PRD+技术方案+会议纪要+竞品分析，以前分四次对话才能喂完，每次开头都得重复一遍上下文。

现在一次搞定，这效率差距你自己体会。

  

程序员就更爽了。

一个中型代码仓库50万到80万Token，整个扔进去。

以前200K的窗口你得精挑细选喂哪些文件，经常漏了关键依赖AI给你写出一堆莫名其妙的bug。

  

以前200K像是给AI戴了个望远镜，只能看到局部。

1M相当于把望远镜换成了全景摄像头，整个桌面一眼看清。

  

  

## 具体升级了什么

老金我把Anthropic官方文档翻了一遍，核心参数拉出来给你们看。

  

**Claude Opus 4.6（最强旗舰，适合最复杂的任务）**

上下文窗口：200K（默认）→ 1M（GA，无溢价）

最大输出：128K Token（大约8万中文字）

API价格：$5（输入）/ $25（输出）每百万Token

之前状态：Beta期间1M需要手动开启+输入价格翻倍

  

**Claude Sonnet 4.6（性价比之王，日常首选）**

上下文窗口：200K（默认）→ 1M（GA，无溢价）

最大输出：64K Token（大约4万中文字）

API价格：$3（输入）/ $15（输出）每百万Token

之前状态：Beta期间1M需要手动开启+输入价格翻倍

  

**Claude Haiku 4.5（轻量快速，适合简单任务）**

上下文窗口：保持 200K ，没动。

  

这里面有几个细节容易搞混，老金我帮你理一下。

  

Beta转正才是重点。

之前1M是Beta状态，默认200K，要手动加API参数才能用。

现在直接GA，所有人默认就是1M，不需要任何额外操作。

  

溢价取消才是最大福利。

Beta期间用1M，超过200K的部分输入价格翻倍（2x），输出涨50%（1.5x）。

现在全部取消，1M和200K完全同价。

  

上下文扩大≠输出变长。

能"看"的多了，但一次"说"的量没变。

Opus还是最多输出128K，Sonnet还是64K。

这是两个独立参数。

  

Haiku没升。

说明1M对算力要求不低，Haiku定位轻量快速，暂时不需要塞这么大的上下文。

  

而且，这个图上可以看到Opus 4.6 在 MRCR v2 测试中取得了 78.3% 的分数。

是同等上下文长度下前沿模型中的最高分。

  

![Image|776](https://mmbiz.qpic.cn/sz_mmbiz_png/2AGkuibewjDqhicA8AaGuMIpxZicRq759zqu9aLq377IWo1libApz6PQiaAKpfwh272iaN86wxfibjHn53HOT2GibuUfAQXeVAA2fub84HTC6aibNyuA/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=2)

  

## 三家都是1M了，但定价完全不一样

这件事最大的意义不在技术参数。

在定价。

  

百万级上下文不是新鲜事。

Gemini从1.5就有了，GPT-5.4月初也跟上了。

Claude是最后一个到的。

  

但Claude做了一件别人没做的事。

  

**不涨价。不是"基础价不涨"，是真的一分钱不涨，1M和200K完全一个价。**

  

Gemini 3.1 Pro和GPT-5.4都有 长上下文溢价 。

什么意思？

就像手机流量套餐，月底超了之后每兆都更贵。

你用的Token超过一定量，单价翻倍。

  

来看具体数据。

  

**Gemini 3.1 Pro**

输入≤200K Token：$2/百万Token

输入>200K Token：$4/百万Token（直接翻倍）

输出≤200K Token：$12/百万Token

输出>200K Token：$18/百万Token（涨50%）

  

**GPT-5.4**

输入基础价：$2.50/百万Token

超过272K Token后价格翻倍

  

**Claude Opus 4.6 / Sonnet 4.6**

不管你用200K还是1M，单价一分不变。

  

老金我帮你算一笔实际的账。

假设你要处理1M Token的输入（比如喂一整个代码仓库，或者一整本书的内容）。

  

![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/2AGkuibewjDqQichpcErrO2DS7wpNueibLQBYN04sX58jB7vp1UdazuuWvOMsY2mrfq7mPwyXq18uibgSk8pbiccjq9eZ1SGYJP7p1A2OqHYhMeI/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=3)

  

看到没？

  

**Claude Sonnet处理1M输入，反而比Gemini 3.1 Pro便宜17%。**

  

之前很多人选Gemini就是因为"长上下文又好又便宜"。

现在算一笔细账，大量输入的场景下Claude Sonnet更便宜。

  

这个定价策略才是这次升级最炸裂的地方。

  

如果对你有帮助，记得关注一波~  

  

## 怎么用上1M

说了一堆参数，落到怎么用上。

  

其实没什么好教的。

  

确保你用的模型是Opus 4.6或Sonnet 4.6，1M自动就有了。

  

claude.ai网页版的，Pro会员直接能用。

打开对话的时候选对模型就行，上下文自动1M，没额外设置没额外费用。

上传长文档、粘贴大段内容，比以前能装多5倍。

  

用API开发的更简单，不需要改一行代码。

model参数还是填 claude-sonnet-4-6 或 claude-opus-4-6，上下文窗口自动从200K扩到1M，定价不变调用方式不变。

  

Cursor、Windsurf这些AI编程工具，设置里模型切成Claude就完事了。

  

## 搭了半年RAG的人怎么办

标题里提到的RAG，全称Retrieval-Augmented Generation。

不懂没关系，老金我用大白话解释。

  

以前上下文不够用，你想让AI参考一大堆文档怎么办？

得搭一套复杂的系统：把文档切成小块→存到专门的数据库→用户提问时自动检索相关段落→拼接到AI能看的范围内。

光这套系统就够折腾一两个月，还需要懂技术。

  

现在1M上下文，很多简单场景直接喂全文就完事了。

不需要切块、不需要数据库、不需要检索。

  

但RAG不是完全没用了。

  

文档量超过1M的（比如企业知识库有几千份文档）还是需要。

需要实时更新数据的场景（比如每天都有新内容进来）也需要。

  

只是说，以前因为上下文装不下而被迫搭RAG的那些场景，现在可以大幅简化。

之前花一两个月搭的那些"简单场景RAG"，确实白忙了。。。

  

  

## 要注意什么

好的说完了，限制也得说清楚。

  

第一，不是每次都需要1M。

上下文越长，响应越慢，花费越多。

日常问个问题、写个短文案，用不着塞这么多东西进去。

别没事把1M塞满，真的烧钱。。。

  

第二，长上下文有个"中间遗忘"的毛病。

你让AI读一本书，开头和结尾它记得很清楚，但中间部分容易走神。

学术上叫 Lost in the Middle（中间丢失），所有大模型都有这个通病。

  

老金我自己试过，把一份很长的文档丢进去，结果中间一段关键数据它直接给漏了。

所以关键信息尽量放在开头或结尾，别埋在正中间。

  

第三，Haiku用户暂时没份。

Haiku 4.5还是200K，没动。

你主要用Haiku的话，这次升级跟你关系不大。

  

## 老金我的建议

在用Claude的直接试。

把之前因为上下文限制做不了的事拉出来重新跑一遍，长文档分析、代码审查、多文件对比，看看哪些现在能一把搞定。

  

还在纠结选哪家的，别光看上下文了，三家都是1M。

重点看定价逻辑，大量输入的场景Claude Sonnet反而最便宜，上面那个表格你自己算。

  

纯观望的也没事。

短期你可能感知不到变化，但建立在Claude上的工具都会因此变强。

Claude Code、Cursor这些AI编程工具首当其冲。

  

说到底，200K到1M不只是参数翻了5倍。

是从"AI只能看一个文件"到"AI能看整个项目"的质变。

这才是真正有意义的升级。

  

你们怎么看？

1M上下文你最想用在什么场景？评论区聊聊。

  

---

**往期推荐：**


[AI编程教程列表](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0NzU2MDgyNA==&action=getalbum&album_id=3704202865347362819#wechat_redirect)

[提示词工工程（Prompt Engineering）](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0NzU2MDgyNA==&action=getalbum&album_id=4120385726238392327#wechat_redirect)

[LLMOPS(大语言模运维平台)](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0NzU2MDgyNA==&action=getalbum&album_id=3171759118513111043#wechat_redirect)

[AI绘画教程列表](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0NzU2MDgyNA==&action=getalbum&album_id=3192433076551843848#wechat_redirect)

[WX机器人教程列表](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI0NzU2MDgyNA==&action=getalbum&album_id=3502843007181520907#wechat_redirect)
  

**开源知识库地址（实时更新交流群）：**

**https://tffyvtlai4.feishu.cn/wiki/OhQ8wqntFihcI1kWVDlcNdpznFf**

**Claude Code & Openclaw 双顶流全中文从零开始的教程：
[不懂代码照样造网站，老金15万字Claude Code+OpenClaw教程免费开源](https://mp.weixin.qq.com/s?__biz=MzI0NzU2MDgyNA==&mid=2247491415&idx=1&sn=8f5fef928275df4c595a4245bb2f3691&scene=21#wechat_redirect)**


我的小破站（含我开源的项目）：**https://www.aiking.dev/**