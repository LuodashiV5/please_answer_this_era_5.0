#  Claude Code 安装

这份指南将帮助您完成 Claude Code 的安装、配置（含国产模型替换）、插件扩展以及高级功能设置。



## 🛠️ 第一步：基础安装与环境准备

# 软件安装（推荐）

所有软件我都打包好了，放到云盘里了： https://www.alipan.com/s/WRMaGZcfKS4

云盘中软件均只适配windows环境，mac用户需要自己去链接下载！！！

vscode：代码编辑器，方便工程化管理代码

Antigravity Tools：将谷歌账户的claude、gemini模型反代理到claudecode中使用

**Antigravity Tools建议去github下载最新版，地址如下：**

```plain
https://github.com/lbjlaq/Antigravity-Manager/releases/tag/v4.1.11
```

ccswitch：便捷的修改claudecode的模型配置、codex的模型配置

github下载地址：

```plain
https://github.com/farion1231/cc-switch/releases
```

git：windows下需要安装

node：核心，必须有

### 1. 安装核心程序

请在 PowerShell 或终端中执行以下命令：

```bash
npm config set registry https://registry.npmmirror.com
npm install -g @anthropic-ai/claude-code
```

![img](https://cdn.nlark.com/yuque/0/2026/png/23100095/1770627349782-9085334b-e104-4ddd-82d9-c1a3255ab756.png)

如果遇到系统禁止运行此脚本，则用管理员身份运行powershell：

```shell
set-ExecutionPolicy RemoteSigned
```

**输入:  A   ,代表全选**

**验证安装：**输入 `claude --version` 查看版本号。



#### 1.1 初始化

在**powershell**中执行

```bash
claude
```

看到claude出现后退出！！！

### 2. 🌍 解决地区限制（必做）

如果在国内使用，请在 **CMD** 中执行以下命令以跳过地区限制检测：

```bash
powershell -Command "$f='%USERPROFILE%\.claude.json';$j=Get-Content $f|ConvertFrom-Json;$j|Add-Member -NotePropertyName 'hasCompletedOnboarding' -NotePropertyValue $true -Force;$j|ConvertTo-Json|Set-Content $f"
```

或者在**powershell**中执行：

```bash
$f="$env:USERPROFILE\.claude.json"; $j=Get-Content $f|ConvertFrom-Json; $j|Add-Member -NotePropertyName 'hasCompletedOnboarding' -NotePropertyValue $true -Force; $j|ConvertTo-Json|Set-Content $f
```

## ⚙️ 第二步：模型与密钥配置

首次运行 `claude` 需要登录。您可以选择**官方账号**，或配置**国产模型/反代服务**。以下是两种主流配置方案：

### 方案 A：使用国产模型（GLM-4.7）

如果您没有 Claude 账号，可以使用智谱 AI（GLM）替代。

1. **获取 Key**：前往智谱 AI 官网获取 API Key。
2. **配置方法**：您可以使用 `ccswitch` 工具管理，或者直接修改配置文件。
3. **手动配置文件路径**：`C:\Users\您的用户名\.claude\settings.json`
4. **写入以下内容**：

```json
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "您的_GLM_API_KEY",
    "ANTHROPIC_BASE_URL": "https://open.bigmodel.cn/api/anthropic",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "glm-4.7",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "glm-4.7",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "glm-4.7",
    "ANTHROPIC_MODEL": "glm-4.7"
  },
  "includeCoAuthoredBy": false
}
```

### 方案 B：Google 反代（Antigravity Tools）

适合拥有 Google 账号的用户。

1. **工具准备**：下载 `ccswitch` 和 `Antigravity Tools`。
2. **启动服务**：

- 在 Antigravity Tools 中添加 Google 账户并开启反代服务。

#### 2.1 添加谷歌账户：

打开Antigravity Tools ，点击账号管理，点击添加账号，会弹出浏览器进行谷歌账户登录，登录完成后出现如下所示：

![img](https://cdn.nlark.com/yuque/0/2026/png/23100095/1770298737933-5abd88df-8504-4323-91c7-1d7658653447.png)

#### 2.2开启反代服务

点击 API反代，点击启动服务，同时注意保存下方有个api密钥，等会在ccswitch要用：

![img](https://cdn.nlark.com/yuque/0/2026/png/23100095/1770298848105-0321c6ec-d45a-4795-8114-8bea6cc4cb9d.png)

然后再当前页面最下方，点击复制对应的模型名称，后面ccswitch要用

![img](https://cdn.nlark.com/yuque/0/2026/png/23100095/1770298921236-54a1b812-ef7d-4fb1-92a6-681a017c68b7.png)

#### 2.3配置连接

打开ccswicth，点击右上角的加号：

![img](https://cdn.nlark.com/yuque/0/2026/png/23100095/1770299034861-26998567-439e-4e4e-a4e2-42669a10552f.png)

选择自定义配置：

![img](https://cdn.nlark.com/yuque/0/2026/png/23100095/1770299063211-6f6b5ae4-6cd7-41b8-a066-1c5c12865a2f.png)

依次填写 

apikey： xxx，就是Antigravity Tools里面的密钥

**请求地址**：`http://127.0.0.1:8045` （固定写法，不用改）

**主模型名称、推理模型名称等等，可以都填一个值（可以从**Antigravity Tools里面复制**）：**

**claude-opus-4-5-thinking  优先填这个，这个最好，最厉害，额度用完了再换gemini**

**gemini-3-pro-high  上面模型额度用完了，再用这个，五小时刷新一次额度**

**gemini-3-pro-low 同理**

![img](https://cdn.nlark.com/yuque/0/2026/png/23100095/1770299127160-198f8e50-5e73-48ac-9ab2-2665c0b520af.png)



------

## 🧩 第三步：开始使用claude

在**powershell**中执行：

```bash
claude
```

## 安装 Skills (插件)-----可选

Claude Code 支持通过 Skills 扩展能力，例如操作 Excel、PDF 或创建新技能。

### 常用指令表

| **操作**     | **命令**                                | **说明**       |
| ------------ | --------------------------------------- | -------------- |
| **添加市场** | `/plugin marketplace add 组织名/插件名` | 注册插件来源   |
| **安装插件** | `/plugin install 组织名@插件名`         | 正式安装插件   |
| **查看技能** | `/skills`                               | 列出已安装技能 |

### 推荐安装清单

1. **官方基础技能库**（包含文档处理等）：

Bash

```plain
/plugin marketplace add anthropics/skills
/plugin install document-skills@anthropic-agent-skills
```

1. **Superpowers**（需分步执行）：

Bash

```plain
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

1. **Ralph**（强大的辅助工具）：

- GitHub 资源：[snarktank/ralph](https://github.com/snarktank/ralph) 或 [frankbria/ralph-claude-code](https://github.com/frankbria/ralph-claude-code)

------

## 日常使用----可选

### 📂 会话管理

- **继续对话**：`claude --continue`
- **恢复历史**：`claude --resume`（会提供选择列表，让您恢复之前的特定对话）