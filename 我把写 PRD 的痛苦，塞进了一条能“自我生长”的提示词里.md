很多人问我：“老金，写 PRD 为什么总是又慢又痛？”

  

因为大脑要同时做三件事：拆需求、编结构、保一致性。任何一环掉链子，整份文档就会“从一开始就写歪”。

  

我这次带来一条我反复打磨的提示词，它不是把你变成作家，而是让模型变成你的“文档流水线”。你只要喂进去项目描述，剩下的结构化输出、用词洁净、可测试的用户故事，全给你端上来。

  

它长什么样？先看核心：这条 PRD Prompt 会强制模型用固定大纲来写文档、给每条用户故事编 ID、覆盖主路径/备选/边界场景，并自查是否可测试、是否覆盖鉴权与角色权限。换句话说——它把“合格 PRD 的最低标准”写进了系统。

  

## 这条 Prompt 到底能帮你什么

- 节省 70% 的骨架时间：不用从 0 开始搭结构。
- 输出“可测试”的用户故事：天然带验收标准，QA直接拿。
- 角色与权限不再漏：产品、运营、开发不再对齐半天。
- 文档一致性：多人协作时，版本与编号可追踪。  
    

##   

## 老金三步上手法

1. 1. 把你的项目描述塞进 {PROJECT_DESCRIPTION}：越具体越好（目标用户、竞品对比、关键指标、边界限制）。
    
2. 2. 直接运行提示词，生成首版 PRD：先别改，先通读结构。
    
3. 3. 二轮迭代：在“用户故事”和“验收标准”里加数据口径、埋点约定、失败态文案。
    

##   

## 一键复制：英文原 Prompt

```
# (EN) PRD Authoring Prompt  
  
You are senior product manager, your goal istocreate a comprehensive Product Requirements Document (PRD) based on the following instructions:  
  
<prd_instructions>  
{PROJECT_DESCRIPTION}  <-- Include the full PROJECT_DESCRIPTION here -->  
</prd_instructions>  
  
Follow these steps tocreate your PRD  
  
1.Beginwith a brief introduction stating the purpose of the document.  
  
2. Organize your PRD into the following sections:  
  
<prd_outline>  
# Title  
## 1. Title and Overview  
### 1.1 Document Title & Version  
### 1.2 Product Summary  
## 2.User Personas  
### 2.1 Key User Types  
### 2.2 Basic Persona Details  
## 3. Role-based Access  
### 3.1 Briefly describeeachuser role (e.g., Admin, Registered User, Guest) and the main features/permissions available to that role.  
## 4.User Stories  
</prd_outline>  
  
3.Foreach section, provide detailed and relevant information based on the PRD instructions. Ensure that you:  
- Use clean and concise language  
- Provide specific details and metrics where required  
- Maintain consistency throughout the document  
- Address all points mentioned ineach section  
  
4.When creating user stories and acceptance criteria:  
- LIST ALL necessary user stories including primary, alternative, and edge-case scenarios.  
- Assign a unique requirement ID (e.g., US-001) toeachuser story for direct traceability  
- Include at least oneuser story specifically for secure access or authentication if the application requires user identification or access restrictions  
- Ensure no potential user interaction is omitted  
- Make sure eachuser story is testable  
  
<user_story>  
- ID  
- Title  
- Description  
- Acceptance Criteria  
</user_story>  
  
5. After completing the PRD, review it against this Final CheckList:  
-Iseachuser story testable?  
-Are acceptance criteria clear andspecific?  
- Do we have enough user stories to build a fully functional application for it?  
- Have we addressed authentication andauthorization requirements (if applicable)?  
  
6. Format your PRD:  
- Maintain consistent formatting and numbering.  
- Don't format text in markdown bold "**", we don't need this.  
- List ALLUser Stories in the output!  
- Format the PRD in valid Markdown, withno extraneous disclaimers.
```

  

## 一个 60 秒小示例

项目：基于内容生成 HTML 卡片并可下载（多语言：中/日/韩/英；输出语言可选）。

用这条 Prompt 喂描述后，你会拿到：

- 规范化的章节：Title、Overview、Personas、Role-based Access、User Stories。
- 用户故事样例（含 ID 与验收标准），比如：

- US-007：访客在不登录的前提下也能预览渲染结果（边界态：超长文本自动截断并提示）。
- US-011：管理员配置输出字体与行距模板（验收标准含渲染一致性与回退策略）。

- 安全/鉴权专门故事：如 US-015（下载链接带时效签名，10 分钟失效，重复请求记录到审计日志）。

  

你也能把它立刻用在别的项目上，因为大纲和检查清单是通用的。

  

老金用来做个HTML渲染的PRD，效果非常可以。

原本的提示词要求英文输出，若看不懂，请记得加入中文输出。

  

输入如下：

```
做根据内容生成HTML卡片可供下载的PRD  
需要中日韩英系统语言，以及输出语言  
请用中英文两种格式输出，放入代码块中  
「接上面的提示词」
```

  

产出灰常可用：

```
# Title  
HTML 卡片生成器 PRD  
  
## 1. Title and Overview  
### 1.1 Document Title & Version  
HTML 卡片生成器 PRD，版本 1.0  
  
### 1.2 Product Summary  
项目描述：构建一个将用户输入内容转为高品质 HTML 卡片并可靠下载的 Web 应用。系统支持四种系统 UI 语言（中文、日文、韩文、英文），并允许为卡片输出选择独立语言，提供机器翻译与人工覆写。生成器具备实时预览、模板选择、品牌样式、无障碍检查与基于角色的安全访问。  
  
## 2. User Personas  
### 2.1 Key User Types  
- 内容创作者：需要快速、零代码地生成并导出卡片。  
- 设计师：维护模板、品牌样式与视觉质量。  
- 本地化负责人：保障多语言翻译质量与版式稳定。  
- 市场人员：批量产出活动卡片，确保一致性。  
- 管理员：配置组织、权限、审计与单点登录。  
  
### 2.2 Basic Persona Details  
- 内容创作者：非技术背景，关注文本、图片与效率。  
- 设计师：擅长排版与字体，维护模板库。  
- 本地化负责人：关注语义准确与各语种排版特性。  
- 市场人员：以场景为导向，需要常用尺寸预设。  
- 管理员：重视安全合规与可审计性。  
  
## 3. Role-based Access  
### 3.1 Roles and Permissions  
- 管理员：组织、用户、角色、SSO、审计日志、全局设置、模板发布、限流。  
- 设计师：创建/编辑模板，管理品牌 Token，发布/下线模板，视觉质检。  
- 创作者：基于模板创建/编辑卡片，上传素材，预览与导出，提交审核。  
- 审核者：评论、审批/驳回、锁定发布版本。  
- 访客：在授权下查看共享预览；无编辑/导出权限。  
- API 客户端：按授权范围调用生成与导出接口。  
  
## 4. User Stories  
US-101 身份认证  
Title: 安全登录  
Description: 作为用户，我通过邮箱/密码或 SSO 登录工作区。  
Acceptance Criteria:  
- 支持邮箱/密码与 SSO（OAuth2 或 SAML）。  
- 强密码与会话超时策略。  
- 错误信息不泄露敏感信息。  
- 记录所有认证事件。  
  
US-102 访问控制  
Title: 基于角色的权限  
Description: 作为管理员，我为不同角色分配权限。  
Acceptance Criteria:  
- 仅管理员可进行角色的增删改。  
- 所有接口强制校验权限矩阵。  
- 无权限返回 403，且不暴露细节。  
  
US-201 系统语言切换  
Title: 切换 UI 语言  
Description: 作为用户，我可在中/日/韩/英之间切换系统 UI 语言。  
Acceptance Criteria:  
- 切换在会话间持久化。  
- 包含校验与错误提示在内的全量文案本地化。  
- 默认跟随浏览器语言，可手动覆盖。  
  
US-202 输出语言选择  
Title: 选择卡片输出语言  
Description: 作为创作者，我可为卡片选择独立于 UI 的输出语言。  
Acceptance Criteria:  
- 输出语言包含中/日/韩/英。  
- 提供机器翻译，并支持人工覆写。  
- 预览即时反映所选输出语言。  
  
US-203 翻译覆写  
Title: 人工校对翻译  
Description: 作为创作者/本地化负责人，我可对机器翻译逐字段覆写。  
Acceptance Criteria:  
- 各文本字段支持按语种覆写。  
- 显示机器与人工版本 Diff。  
- 导出优先使用人工覆写。  
  
US-204 语言回退  
Title: 缺失翻译的回退  
Description: 作为创作者，缺失翻译时系统回退到源语言并提示。  
Acceptance Criteria:  
- 导出不出现占位符或空白。  
- 回退提示仅在编辑界面显示，不进入导出文件。  
  
US-301 从模板新建  
Title: 模板起步  
Description: 作为创作者，我从模板和必填字段开始创建卡片。  
Acceptance Criteria:  
- 模板库带缩略图与元数据。  
- 必填项未完成前禁止导出。  
- 记录所用模板版本。  
  
US-302 可视化编辑器  
Title: 编辑内容与布局  
Description: 作为创作者，我在可视化界面编辑文本、图片与布局并实时预览。  
Acceptance Criteria:  
- 所见即所得，支持网格/吸附。  
- 撤销/重做 50 步。  
- 自动保存：每 5 秒或变更即保存。  
  
US-303 品牌样式  
Title: 品牌 Token  
Description: 作为设计师，我以 Token 管理字体、色彩、间距与圆角。  
Acceptance Criteria:  
- Token 可应用于任意模板。  
- 对比度低于 WCAG AA 给出警告。  
- Token 版本可追踪与审计。  
  
US-304 素材上传  
Title: 上传图片与 Logo  
Description: 作为创作者，我上传图片用于卡片。  
Acceptance Criteria:  
- 支持 png、jpg、webp、svg。  
- 大小限制可配；上传进度与错误明确。  
- 反恶意处理与清洗。  
  
US-305 尺寸预设  
Title: 常用尺寸与安全区  
Description: 作为创作者，我选择预设尺寸并查看安全区。  
Acceptance Criteria:  
- 提供常见渠道预设。  
- 显示安全区/出血参考线。  
- 预览可切换不同预设。  
  
US-401 模板管理  
Title: 创建与发布模板  
Description: 作为设计师，我维护模板并发布。  
Acceptance Criteria:  
- 草稿/已发布状态与版本管理。  
- 破坏性变更需新版本。  
- 兼容性在说明中明确。  
  
US-402 模板导入/导出  
Title: 跨空间迁移模板  
Description: 作为设计师，我导入/导出模板。  
Acceptance Criteria:  
- 以 JSON + 资源 ZIP 导出。  
- 导入校验 Schema 并给出冲突报告。  
  
US-501 导出单文件 HTML  
Title: 单文件导出  
Description: 作为创作者，我导出包含内联 CSS/图片的 .html。  
Acceptance Criteria:  
- 所有资源内联（base64）。  
- 离线打开视觉一致。  
- 文件名安全并带语种后缀。  
  
US-502 导出 ZIP 包  
Title: 结构化导出  
Description: 作为创作者，我导出 HTML + 资源的 ZIP。  
Acceptance Criteria:  
- HTML 使用相对路径。  
- 目录结构固定且文档化。  
- 入口文件为 index.html。  
  
US-503 可选 PNG/PDF  
Title: 图片/PDF 导出  
Description: 作为创作者，我可导出 PNG 或 PDF。  
Acceptance Criteria:  
- 可选 DPI；文本栅格化说明清晰。  
- 与 HTML 预览像素误差 ≤2 px。  
- 大文件流式导出不阻塞 UI。  
  
US-504 元数据注入  
Title: SEO 与社交分享  
Description: 作为创作者，我设置标题、描述与 OG 标签。  
Acceptance Criteria:  
- 元信息写入导出 HTML head。  
- 提供分享预览模拟。  
  
US-505 文件名与编码  
Title: 多平台安全文件名  
Description: 作为创作者，导出文件名跨系统可用。  
Acceptance Criteria:  
- 清除非法字符；必要时转写。  
- 保留语种后缀，如 _ja、_ko。  
  
US-601 无障碍检查  
Title: A11y 校验  
Description: 作为设计师/审核者，我运行无障碍检查。  
Acceptance Criteria:  
- 文本对比度达到 WCAG AA。  
- 图片必须有替代文本或警告。  
- 编辑器支持键盘导航。  
  
US-602 性能预算  
Title: 快速生成  
Description: 作为用户，我期望生成快速。  
Acceptance Criteria:  
- 常规卡片（文本 <50KB、图片 <1MB）TTI < 2 秒。  
- 单文件导出 < 5 秒（参考硬件）。  
  
US-603 安全加固  
Title: 防 XSS/注入  
Description: 作为管理员，我需要安全的内容处理。  
Acceptance Criteria:  
- 输入清洗；导出文件不执行脚本。  
- 推荐导出 CSP（禁止远程脚本）。  
- CI 进行静态扫描。  
  
US-604 审计与日志  
Title: 行为可追踪  
Description: 作为管理员，我查看并导出审计日志。  
Acceptance Criteria:  
- 记录创建/编辑/导出/发布事件与用户、时间、对象。  
- 支持筛选与 CSV 导出。  
  
US-701 审核流  
Title: 审批流程  
Description: 作为审核者，我审批或驳回卡片。  
Acceptance Criteria:  
- 字段级评论；状态流转可追踪。  
- 仅已批准版本可标记为发布。  
  
US-702 共享预览  
Title: 受控分享  
Description: 作为创作者，我共享只读预览链接。  
Acceptance Criteria:  
- 时间限制与随机令牌。  
- 可选要求登录访问。  
  
US-703 版本历史  
Title: 回滚到历史版本  
Description: 作为创作者，我可回滚到旧版本。  
Acceptance Criteria:  
- 时间线与差异视图。  
- 回滚会生成新版本并指向来源。  
  
US-801 接口生成  
Title: API 生成导出  
Description: 作为 API 客户端，我通过接口生成并获取导出。  
Acceptance Criteria:  
- REST 端点具备生成/导出权限范围。  
- 限流与 429 处理。  
- Webhook 通知完成。  
  
US-802 批量生成  
Title: CSV/JSON 批量  
Description: 作为市场人员，我批量生成多语卡片。  
Acceptance Criteria:  
- 字段映射 UI。  
- 进度与部分失败重试。  
  
US-803 字体离线可用  
Title: CJK 字体保障  
Description: 作为设计师，我确保中日韩字体离线可用。  
Acceptance Criteria:  
- 默认使用内置或系统字体栈（如 Noto 系）。  
- 不强依赖外部 CDN。  
  
US-804 本地化格式  
Title: 数字与日期本地化  
Description: 作为创作者，我按目标语种格式化数字与日期。  
Acceptance Criteria:  
- 按语种规则格式化。  
- 预览与导出一致。  
  
US-805 错误与恢复  
Title: 清晰错误与恢复  
Description: 作为用户，我获得可操作的错误提示并能重试。  
Acceptance Criteria:  
- 网络错误可重试并带退避。  
- 未完成导出可续传或取消。  
  
Final Checklist  
- 所有用户故事均具备可测试的验收标准。  
- 身份认证与授权由 US-101 与 US-102 覆盖。  
- 覆盖创建、编辑、本地化、导出、安全、性能与协作。  
- 对 MVP 足够完整，并可平滑扩展。
```

  

## 老金有话说

老金我太懂那种“文档还没搭好，需求已经在路上”的无力感了。

所以把这条 Prompt 留给你：它不替你思考，但替你把台子搭平。

当结构有人管，你就能把心思用在真正值钱的地方——用户与选择。

  

给自己一个轻松的起点：复制、粘贴、生成、微调。

把焦虑从“怎么写”换成“写到哪儿更值”。

愿这条提示词，成为你做产品路上的小小稳定器。