
# Claude Code 画图 skill：excalidraw-diagram-skill

**excalidraw-diagram-skill** 是一个专为 Claude Code 设计的 Skill，让 AI 能从自然语言直接生成 Excalidraw 手绘风格图表。你只需要描述想画什么，剩下的——概念映射、布局、JSON 生成、视觉验证——全部由 AI 完成。

项目地址：`github.com/coleam00/excalidraw-diagram-skill`

---

## 一、Excalidraw 是什么

Excalidraw 是一款开源的手绘风格绘图工具，生成的图表看起来像白板上手画的，清晰自然，适合架构图、流程图、思维导图。传统方式下，你需要手动拖拽操作；有了这个 Skill，Claude Code 直接帮你生成对应的 `.excalidraw` 文件。

与一般的「让 AI 输出 JSON 代码」不同，这个 Skill 还内置了**视觉验证循环**：AI 生成图表后，会自动渲染成 PNG、查看效果、发现布局问题、修复，直到满意为止。这是它区别于其他方案的核心竞争力。

![安装步骤](https://mmbiz.qpic.cn/mmbiz_png/th2mbhGqgT787v4qrc8ZDn2vmduwzzJlrhpydwgFib7ibCFqia4co9YePvu8jqD0RzQSCBJLITAOaDaXzGRNFKoz1iaibVDNC0Vr1NOWDV4IDMqw/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1#imgIndex=0)

安装步骤

---

## 二、安装方式

安装分两步，5 分钟搞定。

### 第一步：下载技能

```
git clone https://github.com/coleam00/excalidraw-diagram-skill.git
```

### 第二步：安装技能

直接在 Claude Code 里说：

> "安装这个技能 excalidraw-diagram-skill"

AI 会自动运行所需命令，安装 Playwright 依赖（用于把 `.excalidraw` 渲染为 PNG）。

安装完成后，技能目录结构如下：

```
excalidraw-diagram/
├── SKILL.md                    # 核心指令文档（设计方法论）
├── references/
│   ├── color-palette.md        # 颜色配置（可自定义品牌色）
│   ├── element-templates.md    # 各类图形的 JSON 模板
│   ├── json-schema.md          # Excalidraw JSON 格式说明
│   ├── render_excalidraw.py    # 渲染脚本
│   └── render_template.html    # 浏览器渲染模板
└── pyproject.toml              # Python 依赖（playwright）
```

---

## 三、怎么用

安装好之后，使用方式非常直接——用自然语言描述你想要的图表就行，AI 会自动判断调用这个 Skill。

**示例指令：**

```
帮我画一个微服务架构图，包含 API Gateway、用户服务、订单服务和数据库
```

```
Create a diagram showing how data flows from frontend to backend API to database
```

```
画一张 CI/CD 流水线流程图，从代码提交到部署上线
```

最终会在当前目录生成一个 `.excalidraw` 文件，可以直接在 Excalidraw 网页版（excalidraw.com）、VS Code 插件或 Obsidian 中打开和编辑。你不需要手动输入特定命令——只要描述需求，AI 会自己找到对应的 Skill。

---

## 四、核心工作流：生成 → 渲染 → 看图 → 修复

这个 Skill 的核心是一个完整的**视觉验证闭环**，不只是输出 JSON 就完事了。

![核心工作流](data:image/svg+xml,%3C%3Fxml%20version='1.0'%20encoding='UTF-8'%3F%3E%3Csvg%20width='1px'%20height='1px'%20viewBox='0%200%201%201'%20version='1.1'%20xmlns='http://www.w3.org/2000/svg'%20xmlns:xlink='http://www.w3.org/1999/xlink'%3E%3Ctitle%3E%3C/title%3E%3Cg%20stroke='none'%20stroke-width='1'%20fill='none'%20fill-rule='evenodd'%20fill-opacity='0'%3E%3Cg%20transform='translate\(-249.000000,%20-126.000000\)'%20fill='%23FFFFFF'%3E%3Crect%20x='249'%20y='126'%20width='1'%20height='1'%3E%3C/rect%3E%3C/g%3E%3C/g%3E%3C/svg%3E)

核心工作流

![[d3b24af1b8c8b52153e3239464ae7f6d.png|740]]

生成图表后，AI 会运行渲染命令，把 `.excalidraw` 文件转成 PNG：

```
cd .claude/skills/excalidraw-diagram/references
uv run python render_excalidraw.py <path/to/file.excalidraw>
```

渲染完成后，AI 会**读取这张图**，检查布局是否合理：

- 有没有文字重叠
    
- 箭头有没有正确对齐
    
- 整体布局是否均衡
    

发现问题就修改 JSON，重新渲染，再看图，直到满意为止。这个循环是工作流的核心部分，而不是最后才做的检查。

对于复杂图表，还有一个约束：**必须逐节构建**，不能一次生成整个文件。Claude Code 每次响应约有 32,000 token 的输出限制，一张综合架构图轻易就会超出，分段生成才能保证质量。
 