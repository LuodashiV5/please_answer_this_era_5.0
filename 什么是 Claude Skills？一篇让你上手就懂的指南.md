你是否想过，让 AI 不仅能聊天，还能帮你处理具体的工作任务？

比如：

- 丢给它一个 PDF，它能自动提取内容
    
- 扔给它一个 Excel，它能帮你分析数据
    
- 说一句"做张幻灯片"，它能直接生成 PPT
    

这一切，通过 **Claude Skills**就能实现。

---

## 什么是 Skills？

**Skills（技能）**是 Claude 的"超能力包"。

你可以把它理解为：Claude 有了"技能插件"之后，从一个聊天AI变成了一个**能干活的生产力工具**。

`你提出需求 → Claude 识别需要用的技能 → 调用对应工具 → 完成工作   `

每个 Skill 专注某一类任务，就像专业工匠的工具箱一样。

![图片|644](https://mmbiz.qpic.cn/sz_mmbiz_png/cpKDSd81FXib1fZZUOdpUZtrIicfdOlW1UGSIULCdoStBLRYSib8pWudJ9lQzFT1LEvPDKVQYkYsSPHUL1ic6F7HNpUM4ibE5GYN1FFibRqpUsicYM/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=0)

  

---

## Skills 能做什么？

根据你安装的技能不同，Claude 可以：

|你说|Claude 帮你做|
|---|---|
|"读一下这个 PDF"|提取文字、分析内容|
|"帮我做个表格"|处理 Excel、数据分析|
|"做一张幻灯片"|生成 PPT|
|"画个流程图"|制作 Mermaid 图表|

这只是冰山一角。目前社区已有大量 Skills，涵盖：

- Web 开发（React、Next.js、TypeScript）
    
- 测试（Playwright、Jest）
    
- 设计（UI、UX、品牌规范）
    
- 文档（API 文档、Changelog）
    
- 效率工具（自动化、工作流）
    

**技能市场**：https://skills.sh/

---

## 快速上手：安装一个技能

最推荐的方式是用 `npx skills`命令。

### 1. 搜索技能

 `npx skills find react`

### 2. 安装技能

 `# 全局安装（所有项目都能用）   npx skills add <技能名> -g      
 # 示例：安装 Vercel 的 React 最佳实践   npx skills add vercel-labs/agent-skills@vercel-react-best-practices -g -y`

### 3. 查看已安装的技能

 `npx skills list`

---

## 技能的存放位置

安装的技能放在哪里？这里有三种选择：

| 作用域             | 谁能用   | 哪些项目能用 | Git 提交？ |
| --------------- | ----- | ------ | ------- |
| **user**(全局)    | 只有你   | 所有项目   | -       |
| **project**(项目) | 你+协作者 | 所有项目   | ✅ 会提交   |
| **local**(本地)   | 只有你   | 仅当前项目  | ❌ 不提交   |

**怎么选？**
	- 个人使用 → user scope
	- 团队协作 → project scope
	- 特定项目 → local scope

### 我的真实用法

我自己有两套存放策略：

**`.claude/skills/`**
    - 管理项目用的技能
    - 一般不和项目分享，设置成 local scope
    - 比如：项目特定的构建脚本、测试工具

**`50-Skills/`**
	- 想和项目一起分享的技能
	- 单独建一个文件夹，纳入 Git 管理
	- 比如：团队统一的代码规范、文档模板

这样分区的好处是：个人用的不干扰团队，团队用的大家一起维护。

![图片|554](https://mmbiz.qpic.cn/sz_mmbiz_png/cpKDSd81FX9c6hVYJFehbicPun3B16tJ6ZdMP3GMXOblEBgYLiaHffvrhtRjsaBca9sMK3ludVlUInHwNHmQoqdPFZC1pDDibeJicc75TuWttrk/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=1)

---

## Skills 和 MCP 是什么关系？

很多人容易混淆这两个概念：

| |Skills|MCP|
|---|---|---|
|**定位**|任务级别的功能|协议级别的连接|
|**比喻**|"能做什么菜"|"如何去菜市场买菜"|
|**关系**|技能可以使用 MCP 服务|MCP 是底层协议|

简单理解：

- **MCP**
    = 帮你连接外部服务（数据库、API）
    
- **Skills**    
    = 有了这些连接后，怎么帮你干活
    

![图片|556](https://mmbiz.qpic.cn/mmbiz_png/cpKDSd81FXiczsy5RHYJMNdRd9CvofQPI0wjwgdicHNXAYYZFTIrfchq2V9Sosg2zGAJJQRobmDlicBNAojWxibAAyVy9MjENvCdhibFTBlAjQeQ/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=2)

---

## 常用命令速查

	```markdown
	npx skills find <关键词>  
	  
	# 安装  
	npx skills add <技能> -g        # 全局  
	npx skills add <技能> --project # 项目  
	npx skills add <技能> --local   # 本地  
	  
	# 查看  
	npx skills list  
	npx skills check  
	  
	# 更新  
	npx skills update  
	  
	# 删除  
	npx skills remove <技能>
	````

---
## 为什么要用 Skills？

1. **更专业**
    - 每个技能专注一个领域，处理更精准

2. **更高效**
    - 相同任务有标准化流程，不用重复说明

3. **可扩展**
    - 随时可以安装新技能，能力无限

4. **可复用**
    - 创建的技能可以重复使用

## 写在最后
文使用 Claude + Minimax2.5 协助创作