前面几篇文章分别记录了：

- 如何在本地电脑上安装opencode
    
- 如何在VSCode中安装opencode插件
    
- 如何将opencode集成到Obsidian
    
- 如何使用快捷指令将手机上阅读的文章或文章链接存档到Obsidian，并同步到电脑端
    

如果感兴趣，可以翻阅前面的文章。

这两天又尝试了下通过opencode以及skills将收藏的文章提炼出文章总结笔记。下面分步记录下。

# 文章总结模板

在“99-模板”目录下创建“文章总结”模板，供skills调用：  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/kJgMlSXdCicfT4WAaeVD00BzPPe347SMualaQFic2LQcI1uDR98EdHx0ib09RylGN0cDb9PbweP02eG8nziavPLho6gbhBdzVPUI6loiaNjgPfTQ/640?wx_fmt=png&from=appmsg&wxfrom=13&tp=wxpic#imgIndex=0)

以下是模板内容：

```txt 
# 一、基础信息 

- **标题**：`{{替换为文章标题}}`   

- **来源**：`{{替换为文章来源/作者}}` 

- **阅读日期**：`{{替换为日期}}` 

- **原文链接**: [{{替换为原来https链接}}]({{替换为原来https链接}})  

# 二、核心内容 

## 核心观点 

- 文章的主要论点或中心思想  

## 关键论据 

- 支撑核心观点的关键证据或数据 

- 重要案例或引用  

# 三、结构分析 

## 文章脉络 

- 开篇引入方式 

- 主体论述结构 

- 结尾总结方式  

## 逻辑框架 

- 论证逻辑链条 

- 各部分衔接关系  

# 四、要点提炼 

## 核心结论 

- 文章得出的主要结论  

## 重要观点 

- 值得记录的次级观点 

- 新颖或有启发的见解  

# 五、评价与思考 

## 价值评估 

- 文章的独特价值 

- 信息可靠性分析  

## 个人启示 

- 引发的思考或启发 

- 可应用的实际场景  

# 六、关键词 

`#关键词1` `#关键词2` `#关键词3`  

---  

*总结生成时间：{{时间}}* 
```


# 创建技能

在知识库根目录下创建 **.opencode**->**skills** 目录，以及存放文章总结技能的**article-summarizer** 目录，并在此目录下创建**SKILL.md** 文件：  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/kJgMlSXdCicfdeqT1Tib1e08AuczFJ2Y7JcJhk7GMA1M1xRd319DGtmibnMrXCFFEBuX9dk79PKIXzVjdyBwoiaRPfv9h73ORp1HtWk2fd5FBnA/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1#imgIndex=1)

以下是**SKILL.md** 文件内容：

```markdown
--- 
name: article-summarizer 
description: 当用户提供文章（内容、链接或指定文件）并要求总结、解析、评论或做笔记时，使用此技能。我将严格按照指定的结构化模板，生成一份包含基础信息、核心内容、结构分析、要点提炼、评价与思考及关键词的完整文章总结。 
license: CC0 
compatibility: opencode metadata:   
  author: OpenAI User
  template-version: "1.0" 
---  
## What I do 
- 根据用户提供的文章内容（文本、链接或文件路径），生成一份结构化的深度总结。 
- 严格遵循包含“基础信息、核心内容、结构分析、要点提炼、评价与思考、关键词”的标准模板。 
- 自动填充阅读日期、总结生成时间等信息。 
- 旨在提炼文章的论点、论据、结构与价值，便于用户存档、回顾或进一步思考。 
- 如果文章中涉及或引用了书，请列出书单，并说明作者引用的原因。  
## When to use me 
- 当用户说“总结一下这篇文章”、“分析这篇内容”、“为这篇文章做个笔记”或使用类似表述时。 
- 当用户提供清晰的文本、URL链接或指向本地文件的路径时。 
- 当用户需要一个超越简单摘要的、结构化的深度分析时。  
## Instructions 
请严格按照以下步骤执行：  
1. **读取模板文件**：首先读取 `99-模板/文章总结.md`，了解输出格式结构 
2. **获取文章内容**：如果笔记文档中有https链接，则读取本笔记文档中的https链接的内容；否则，直接读取笔记文档中的内容 
3. **生成总结**：基于文章内容，严格按照模板格式填充内容，**直接写入当前打开的笔记文件中**，替换原有内容 
4. **【关键步骤】修改文件名**：用文档标题替换被写入文档文件名中最后一个中划线之后的内容    
- 原文件名格式：`2026-03-07-XXXXXX.md`（时间戳部分）    
- 新文件名格式：`2026-03-07-文章标题.md`    
- 例如：将"2026-03-07-171713"替换成"2026-03-07-用本地打造免费的OpenClaw助理"    
- **此步骤必须执行，不可遗漏**  
注意： 
1. 模板里的所有占位符都要替换 
2. 生成的总结文档中的**章节编号**必须与模板一致 
```

# 使用文章总结技能

好了，到此为止，所有的文章总结技能的准备工作就做好了，下面开始使用这个技能。

在Obsidian打开带总结的笔记文档，此笔记文档是之前在手机端收藏同步到电脑端的，其中包含了一个https链接。

在chat窗口，输入：

```
请调用文章总结技能生成总结笔记 
```

可以看到opencode调用了文章总结技能：**article-summarizer**：  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/kJgMlSXdCiceD2R4uFxTWgYSJiarMClqmknFFPDa7teXsibCVY3kicJuuODWtVcc92abUef4dsXCoTibPwsTJ6DIo2SKiazefHC8dAwVFVwSrjxXY/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1#imgIndex=2)

紧接着，技能就开始运行了。

稍等片刻，这篇通过读取**https**链接并进行总结的笔记就按照前面的模板生成了。  
![图片](https://mmbiz.qpic.cn/mmbiz_png/kJgMlSXdCicc8U9WpicSgGaWyEgDfvhGBPhBCv0woShnKvbDFm2X2Hib9uTjlwVicV0uEKxEpKDibYaZYazMBukxZQfJKTcnxPnEyj8hsbHoxnUs/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1#imgIndex=3)
 