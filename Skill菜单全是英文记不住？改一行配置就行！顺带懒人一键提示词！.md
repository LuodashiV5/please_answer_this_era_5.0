昨天有个群友群里@老金我：
  
![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/4t4XAdD3vanNtM7RBERKnF4QtovueuFa5DqQPgJgE725q8pazYevhRQ4iaFwUcYkRQBDibdKk0a8S8mMKujBmojw/640?wx_fmt=png&from=appmsg&wxfrom=13&tp=wxpic&watermark=1#imgIndex=0)
  
老金我一看，这问题太常见了。
  
你装了几十个Skill，菜单里全是英文名字。
remotion-best-practices是什么？vercel-react-best-practices是什么？web-design-guidelines又是什么？
每次想用都要翻文档，或者一个个点进去看描述。
  
而我的是这样的，这个只是截图用的，它是按照智能排序（使用频率），没有命名排序！
  
![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/4t4XAdD3vanNtM7RBERKnF4QtovueuFaRWNXWwE0yBRbCaQ1NmC8SAoghN9WyQsBPt0jlUIskPic6hRINfJvgYg/640?wx_fmt=png&from=appmsg&wxfrom=13&tp=wxpic&watermark=1#imgIndex=1)
  
当然，前面的名字也能改中文。
  
![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/4t4XAdD3vanNtM7RBERKnF4QtovueuFafib0wFBH1YgpCLwYQgLJibfiaYyBzPtLe828aXocEQHgfmXjtvIkqlnTA/640?wx_fmt=png&from=appmsg&wxfrom=13&tp=wxpic#imgIndex=2)
  
老金我告诉你，改一行配置就能解决。
至于为什么我不都把名字改中文，下面告诉你。
  
## 先说说大家能共鸣的
你打开Claude Code，输入 /想调用Skill。
菜单弹出来，你看到的全是英文。
  
你想用"视频剪辑工具"，但你记不住它叫remotion-best-practices。
你想看"React开发规范"，但你记不住它叫vercel-react-best-practices。
你想查"Web设计指南"，但你记不住它叫web-design-guidelines。
  
每次都要猜，或者翻文档。
  
老金我装了30多个Skill，刚开始也是这样。
  
  
## 解决方案
打开你的Skill配置文件。
  
路径是 ~/.claude/skills/xxx/SKILL.md，xxx是你的Skill名字。
  
比如remotion-best-practices这个Skill，路径就是 ==~/.claude/skills/remotion-best-practices/SKILL.md。
  
打开文件，你会看到开头有个YAML格式的配置区，类似这样：
```
---
name: remotion-best-practices
description: Best practices for Remotion video development...
---
```
  
关键来了。
  
把 name字段改成"英文名 中文说明"的格式。
  
description字段也可以改成中文，这样看详细信息时更清楚：
```
---
name: remotion-best-practices 视频剪辑工具
description: Remotion视频开发和动画工作流的最佳实践指南
---
```
  
两个字段的区别：
- **name：**显示在菜单列表中，建议"英文名 中文说明"格式（保留英文方便搜索，甚至我用的数00这样的数字）
- **description：**显示在详细信息中，可以完全用中文写详细说明
- 
保存文件。
再打开Claude Code，输入 /。
  
菜单里显示的就是：
```
/remotion-best-practices 视频剪辑工具
```
  
一眼就知道这个Skill是干什么的。
  
比如最近火的这些，你都可以调整下：
```
remotion-best-practices 视频剪辑工具
vercel-react-best-practices React开发规范
web-design-guidelines Web设计指南
frontend-design 前端设计
seo-audit SEO审计
```
  
每个Skill是干什么的，一目了然。
  
老金我现在想用视频剪辑工具，直接找"视频剪辑工具"。
想看React开发规范，直接找"React开发规范"。
想查Web设计指南，直接找"Web设计指南"。
  
不用记英文名，不用翻文档，不用猜。
  
## 老金的建议

老金作为个铁I人，习惯性的有一些整理癖好。
这个技巧几用了几个月了，有几条经验。
  
**第一，中文说明要简短。**
4-6个字最好，太长菜单会挤。
好的例子：视频剪辑工具、React开发规范、Web设计指南、前端设计。
不好的例子：用于Remotion视频开发和动画工作流的最佳实践指南。
  
**第二，中文说明要准确。**
写的是这个Skill的核心功能，不是描述。
remotion-best-practices的核心功能是"视频剪辑工具"，不是"视频开发指南"。
vercel-react-best-practices的核心功能是"React开发规范"，不是"Vercel团队建议"。
  
**第三，description可以写详细点。**
name要简短（4-6字），但description可以写详细一点。
description是给你看详细信息用的，写清楚一点更好。
  
**第四，定期整理。**
你装了新Skill，记得改name和description。
你删了旧Skill，记得清理配置。
  
老金我每个月整理一次，保持菜单清爽。
  
如果对你有帮助，记得关注一波~  
  
## 进阶技巧
如果你Skill装得特别多，可以用分类前缀。
  
比如：
```
写作-文档生成
写作-SEO审计
开发-视频剪辑工具
开发-React开发规范
设计-Web设计指南
设计-前端设计
```
  
这样菜单里同类Skill会排在一起，更好找。
  
### 懒人福音：一键批量翻译

老金我刚才教你手动改，但你要是装了几十个Skill，一个个改也挺累的。
有个更快的方法：让Claude Code帮你批量翻译。
  
打开Claude Code，复制下面这段提示词：
```
请帮我批量为所有Claude Code Skills添加中文翻译。
任务：
1. 扫描 ~/.claude/skills/ 目录下的所有Skill
2. 读取每个Skill的SKILL.md文件
3. 检查name和description字段是否已有中文翻译
4. 如果没有，根据Skill的英文名和description，添加中文翻译
5. 格式：   
- name: 英文名 中文说明（4-6字）   
- description: 完整的中文描述

要求：
- name的中文说明要简短（4-6字）
- description要完整翻译成中文，写详细一点
- 保留原有英文名，只在后面添加中文
- 不要修改其他字段
- 处理完后告诉我修改了哪些Skill
  
开始执行。
```
  
粘贴到Claude Code，回车。
Claude翻译的中文说明，比老金我自己想的还准确。
  
#注意：更新会被覆盖，需要重新翻译
  
  
## 老金想说
这个技巧很简单，改一行配置就行。
但老金我见过太多人，装了一堆Skill，每次用都要翻文档。
  
不是他们不会用，是他们不知道可以这么改。
老金我告诉你：工具是为你服务的，不是你为工具服务。
  
菜单全是英文看不懂？改成中文。
配置太复杂记不住？简化配置。
操作太繁琐效率低？写个脚本。
  
这才是用工具的正确姿势。
  
老金我写这篇文章，就是想告诉你：
  
很多问题，解决方案比你想象的简单。
  
你觉得"菜单全是英文"是个大问题，其实改一行配置就行。
你觉得"Skill太多记不住"是个大问题，其实加个中文说明就行。
  
不要被问题吓住，先想想有没有简单的解决方案。
  
老金我用Claude Code这么久，最大的感受就是：
  
最好的配置，是你自己改出来的。
  
官方给你的是通用配置，你要根据自己的习惯调整。
你可能有别的习惯，比如用emoji分类、用数字排序。
  
都行，只要你用着顺手。
就这么简单。