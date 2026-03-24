太对了，新店前期最累人的就是“上架”，这个完全可以丢给 Agent 干 ⭐️
 我帮你先把一个 **可在 OpenClaw 中落地的「商品上架 Listing Agent」MVP 方案**梳理出来，你可以直接拿去改 ID / 接口地址就能用。

------

## 一、结论摘要

1. 建议你做一个独立的 **Listing Agent**，专门服务内部运营同学（不是对用户），负责一整条链路：
     `Excel/后台数据 → 结构化商品草稿 → 标题&卖点 → 小红书文案 → 提交商品库`。
2. Agent 主要依赖 3 个工具（Skills）：
    - `parse_listing_source`：把 Excel/后台的一行数据解析成统一的 `ProductDraft` 结构
    - `validate_listing`：根据类目规则检查必填字段 & 数据合法性
    - `submit_product_catalog`：把最终商品数据写入你的商品库（内部 HTTP API）
3. 生成内容的逻辑（标题/卖点/XHS 文案）全部写在 Agent 的 Prompt 里，用规则 + 示例约束输出质量。
4. 给你一份 **可直接复制到 OpenClaw 的 Agent Prompt 草案 + 工具 JSON Schema/OpenAPI 草案 + 流程图**，MVP 先“半自动+人工确认”，后面可以改成全自动。
5. 兜底策略：工具挂了 / 数据缺失时，Agent 不乱编，明确告诉运营“缺什么字段”，只在数据完整、校验通过时才调用提交接口。

最后我会给你：

- ✅ Listing Agent 的 System Prompt
- ✅ 3 个工具定义（JSON Schema 风格）
- ✅ 编排流程（Mermaid）
- ✅ 失败兜底思路
- ✅ 5 条测试用例

------

## 二、整体架构 & 流程

### 1. 角色定位

- **Listing Agent（商品上架 Agent）**
    - 使用对象：内部运营 / 你自己
    - 职责：
        1. 解析 Excel/后台原始数据 → 标准 `ProductDraft`
        2. 根据类目生成：标题、卖点（3–5 条）、搜索关键词
        3. 生成小红书风格长文案
        4. 校验必填项 & 规则
        5. 提交到商品库 + 返回商品 ID / 上架状态

### 2. 高层流程（文字版）

1. 运营在 OpenClaw 里对话：

    > “帮我上架这批商品，这是 Excel 里的一行：……”
    >  或
    >  “这是后台导出的 JSON：……”

2. Listing Agent：

    1. 自动判断这是 Excel 行文本/CSV/JSON
    2. 调用 `parse_listing_source` → 标准化 `ProductDraft`
    3. 基于 `ProductDraft` 生成：`title`、`selling_points`、`xhs_description`、`seo_keywords`
    4. 调用 `validate_listing` 检查：类目、价格、图片、规格等
    5. 把最终版本展示给运营确认（MVP 阶段建议加确认）
    6. 调用 `submit_product_catalog` 写入商品库
    7. 返回：商品库 ID / 状态 / 如果失败则失败原因

### 3. Mermaid 流程图（可直接贴到文档）



flowchart TD

  A[运营在 OpenClaw 里发送nExcel行/后台数据] --> B[Listing Agent 接收请求]

  B --> C{识别数据格式}

  C -->|Excel/CSV 文本| D[调用 parse_listing_source]

  C -->|JSON/后台数据| D[调用 parse_listing_source]

  D --> E[获得标准化 ProductDraft]

  E --> F[Agent 内部生成n标题/卖点/小红书文案/关键词]

  F --> G[调用 validate_listing 校验]

  G -->|不通过| H[向运营说明缺失或错误字段n请求补充/修改]

  H --> E

  G -->|通过| I[将生成结果摘要展示给运营n(标题+价格+主图+文案)]

  I --> J{运营是否确认提交?}

  J -->|否| H

  J -->|是| K[调用 submit_product_catalog]

  K --> L{提交结果}

  L -->|成功| M[返回商品ID/链接n提示上架成功]

  L -->|失败| N[返回错误信息n建议人工处理]



------

## 三、可落地清单（ToDo / Checklist）

你可以按“最小可行(MVP)”一步步搞：

### MVP 阶段（推荐先做）

1. **定义统一数据结构 `ProductDraft`**
    - 字段建议： { "source_type": "excel_row | backend_json", "channel": "taobao | tmall | douyin | xhs | self", "category_name": "女装/连衣裙", "brand": "示例品牌", "sku_code": "SKU123", "spu_code": "SPU001", "raw_title": "原始标题", "raw_description": "原始描述", "attributes": {  "颜色": "黑色",  "尺码": "M",  "材质": "棉" }, "price": 199.0, "stock": 100, "images": [  "https://xxx/1.jpg",  "https://xxx/2.jpg" ], "extra": {}}
2. **实现 3 个 HTTP 工具接口（哪怕先用 mock）：**
    - `POST /api/listing/parse` → `parse_listing_source`
    - `POST /api/listing/validate` → `validate_listing`
    - `POST /api/catalog/products` → `submit_product_catalog`
3. **在 OpenClaw 后台创建 Listing Agent**
    - 填入 System Prompt（下面给）
    - 绑定上述 3 个工具（OpenAPI/JSON Schema）
    - 设置对话对象为“内部运营”
4. **用 1–3 个真实商品行做测试**
    - 重点看：字段映射是否准确？标题/卖点风格是否符合预期？XHS 文案是否太长/太夸张？
5. **加一个简单的“确认开关”**
    - MVP 时默认要求运营输入“确认提交”或点击按钮才触发 `submit_product_catalog`。

### Pro / 进阶阶段

1. 按类目定制规则：标题长度、是否带品牌、含关键词等。
2. 按渠道（淘宝/抖音/小红书）定制不同的标题/卖点风格。
3. 增加 `get_category_template` 工具，从类目服务拉取：必填属性、可选值、规则说明。
4. 把历史爆款商品作为 RAG 知识库，让 Agent “模仿本店爆款风格”。
5. 支持批量模式：一次处理一整批 Excel（N 行），逐条生成草稿。

------

## 四、Listing Agent 的 Prompt 草案（可直接使用）

> 你可以根据实际平台替换“淘宝/抖音/小红书”等词。



[角色]
你是一个电商商品上架助手（Listing Agent），服务对象是内部运营/商家人员。
你不直接面对消费者，你的目标是：
1）把 Excel 或后台导出的原始数据转换为结构化商品数据；
2）自动生成适合电商平台的商品标题和卖点；
3）自动生成符合小红书风格的种草型介绍文案；
4）在数据完整、校验通过且用户确认的前提下，将商品提交到商品库。

[输入数据类型]
运营可能会给你以下几种形式的输入：
- Excel 的一行（复制出来的文本，可能是用逗号/制表符分隔的字段）
- 后台导出的 JSON 或类似结构
- 同时包含类目信息、品牌、价格、库存、图片链接等
你要先判断输入类型，再调用工具 parse_listing_source 进行标准化解析。

[工作步骤（必须严格按照顺序执行）]
1）理解任务：
 - 搞清楚用户是要「新建商品」还是「补全/修改」已有商品（如果用户没说，默认是新建）。
 - 搞清楚目标渠道（如用户未指明，默认以“通用电商平台”为目标）。
2）解析原始数据：
 - 使用工具 parse_listing_source，把 Excel/后台数据转换为统一的 ProductDraft 结构。
 - 若解析失败，明确告诉用户失败原因，并请用户重新提供更清晰的数据格式（比如列出你需要的字段名）。
3）补全和优化商品信息（在你内部思考中完成，无需暴露中间过程）：
 - 在尊重原始数据的前提下，对标题、卖点、描述做“文案级加工”，但不得捏造不存在的参数、证书、功效等。
 - 对于缺失但又必需的信息（如类目、品牌、价格等），先向用户发问确认，禁止自行编造。
4）生成以下结构化结果：
 - 标准化商品结构（JSON 格式），包含至少：
  - 标题（title）
  - 卖点数组（selling_points，3-5 条，每条不超过40字）
  - 价格（price）
  - 库存（stock）
  - 类目（category_name）
  - 品牌（brand）
  - 关键属性（attributes）
  - 图片链接（images）
  - SEO 关键词（seo_keywords，5-10 个）
  - 小红书风格长文案（xhs_description）
 - 小红书文案风格要求：
  - 口吻：第一人称/朋友安利口吻，真实分享，不要像广告文案。
  - 内容结构建议：
   1）开头 1-2 句抓注意力，说明使用场景或痛点；
   2）中间分点描述产品亮点、使用感受、适用人群（可适当使用 emoji）；
   3）结尾给出种草/下单理由，避免夸大宣传。
  - 禁止使用绝对化用语（如“永久”“最强”“百分百”），避免涉及功效承诺的敏感描述。
5）调用 validate_listing 工具：
 - 把你生成后的完整商品数据传给 validate_listing。
 - 若校验不通过：
  - 明确告诉用户：哪些字段缺失/不合法；
  - 给出修改建议；
  - 请求用户补充信息后再重新校验。
6）提交商品库：
 - 当且仅当：
  - 商品数据结构完整，
  - validate_listing 返回通过，
  - 用户明确表达“确认提交/可以上架/帮我入库”这类语句，
  - 才调用 submit_product_catalog。
 - 调用成功后，将返回的商品ID或链接清晰地反馈给用户。
 - 若提交失败，向用户说明失败原因，并建议联系技术/人工处理。

[风格与格式要求]
- 优先使用 JSON 代码块返回结构化商品数据，便于开发调试。
- 跟用户对话时，语言要简洁、工程化：
 - 先给关键信息摘要（标题+价格+主图链接总数）；
 - 再给完整 JSON；
 - 最后给小红书文案。
- 如果有不确定的地方，一定要提问澄清，不要自作主张。

[安全与合规]
- 不得虚构不存在的检测报告、资质、疗效；
- 不得输出违法或平台明令禁止的内容；
- 对于涉及年龄、功效等敏感商品（如保健品、医美），请保守描述，必要时提醒运营补充专业文案或走合规审核。

[工具使用原则]
- 任何时候你只通过工具与商品库交互，不直接假设“已经成功保存”。
- 工具超时/失败时，向用户说明：“商品库接口暂时不可用，请稍后重试或联系技术”。

``



------

## 五、工具（Skills）定义草案

> 下面是偏 OpenAPI/JSON Schema 的草案，你可以按你们后端实际接口调整。

### 1. 解析数据工具：`parse_listing_source`



```yaml
# OpenAPI snippet
paths:
  /api/listing/parse:
    post:
      operationId: parse_listing_source
      summary: Parse raw listing data (Excel row or backend JSON) into a ProductDraft
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                source_type:
                  type: string
                  enum: [excel_row, backend_json]
                raw_content:
                  type: string
                  description: "Original content, e.g. a CSV line or JSON string"
                channel:
                  type: string
                  description: "sales channel, e.g. taobao, douyin, xhs, self"
              required: [raw_content]
      responses:
        "200":
          description: Parsed ProductDraft
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/ProductDraft"

components:
  schemas:
    ProductDraft:
      type: object
      properties:
        source_type:
          type: string
        channel:
          type: string
        category_name:
          type: string
        brand:
          type: string
        sku_code:
          type: string
        spu_code:
          type: string
        raw_title:
          type: string
        raw_description:
          type: string
        attributes:
          type: object
          additionalProperties: true
        price:
          type: number
        stock:
          type: integer
        images:
          type: array
          items:
            type: string
        extra:
          type: object
          additionalProperties: true
      required: [raw_title]
```





### 2. 校验工具：`validate_listing`

```yaml
paths:
  /api/listing/validate:
    post:
      operationId: validate_listing
      summary: Validate a full product listing against category rules
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/FullProduct"
      responses:
        "200":
          description: Validation result
          content:
            application/json:
              schema:
                type: object
                properties:
                  valid:
                    type: boolean
                  errors:
                    type: array
                    items:
                      type: string
                  warnings:
                    type: array
                    items:
                      type: string

components:
  schemas:
    FullProduct:
      type: object
      properties:
        title:
          type: string
        selling_points:
          type: array
          items:
            type: string
        price:
          type: number
        stock:
          type: integer
        category_name:
          type: string
        brand:
          type: string
        attributes:
          type: object
          additionalProperties: true
        images:
          type: array
          items:
            type: string
        seo_keywords:
          type: array
          items:
            type: string
        xhs_description:
          type: string
      required: [title, price, category_name, images]
```



 



### 3. 提交商品库工具：`submit_product_catalog`



```yaml
paths:
  /api/catalog/products:
    post:
      operationId: submit_product_catalog
      summary: Create a new product in catalog
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/FullProduct"
      responses:
        "200":
          description: Product created
          content:
            application/json:
              schema:
                type: object
                properties:
                  success:
                    type: boolean
                  product_id:
                    type: string
                  product_url:
                    type: string
                  message:
                    type: string
        "400":
          description: Bad request
        "500":
          description: Server error
```



------

## 六、失败兜底策略（建议写进配置 / 运维说明）

1. **parse 失败**
    - Agent 明确说：哪一行解析失败 / 哪个字段缺失；
    - 提示运营：按你约定的格式重新复制（甚至可以给出一个示例行格式）。
2. **validate 不通过**
    - 不调用提交接口；
    - 用列表列出所有 `errors`，让运营一次性改全；
    - 对 `warnings` 只做提醒，不阻止提交（例如“建议上传更多图片”）。
3. **提交商品库失败**
    - 不重复调用；
    - 把后端返回的 `message` 原样展示出来；
    - 建议运营“改为人工在后台创建”或“联系技术看日志”。
4. **用户表达不清**
    - 如果用户说“帮我看看这个 SKU”而没说“上架”，就只做解析+生成草稿，不调用提交接口。
5. **多轮对话上下文**
    - Agent 要记得：这次对话的任务是“围绕同一个商品”，直到用户说“下一个商品”或“换一行数据”。

------

## 七、对话 & 测试用例示例

### 1. 测试用例列表（5 条）

1. **女装类目 – Excel 行文本**
    - 输入：一行“夏季连衣裙,女装,199,100,黑色;M;雪纺,[https://img1.jpg”](https://img1.xn--jpg-9o0a/)
    - 期望：解析成功，生成清晰标题、3–5 条卖点、小红书文案，价格=199，类目=女装/连衣裙，校验通过后可提交。
2. **3C 数码 – JSON 后台数据**
    - 输入：含“手机壳”类目、多个颜色/型号属性的 JSON。
    - 期望：能正确识别多属性，标题避免堆砌关键词，小红书文案偏“使用体验+防摔场景”。
3. **缺少类目/价格的行**
    - 输入：只有标题和描述，没有价格/类目。
    - 期望：Agent 主动提问要类目和价格，不得随便填；在补全前不调用提交接口。
4. **商品库接口失败**
    - 模拟 submit_product_catalog 返回 500。
    - 期望：Agent 提示“商品库接口异常，请稍后重试或联系技术”，不要宣称上架成功。
5. **运营只想要文案，不想入库**
    - 输入：“帮我帮这个商品写标题和小红书文案，不需要上架。” 并附数据。
    - 期望：只做生成标题+卖点+文案，不调用 validate/submit，最后说明“如需入库可以再告诉我”。

### 2. 简单对话示例（缩写版）

**用户：**

> 帮我上架这个商品，这是 Excel 里复制的一行：
>  `夏季雪纺连衣裙,女装,示例品牌,199,100,黑色;M;L;XL,https://img.xxx/1.jpg`

**Agent：**

> 我先帮你解析这行数据并生成上架草稿，然后给你看标题和文案，确认后再入库。

（内部调用 `parse_listing_source` → 得到 ProductDraft）

**Agent：**

> 我已经解析出以下关键字段：
>
> - 类目：女装/连衣裙（来自“女装”和标题）
> - 品牌：示例品牌
> - 价格：199 元
> - 库存：100
> - 图片：1 张
>      接下来我会基于这些信息生成标题、卖点和小红书文案。

（Agent 内部生成 FullProduct + 调用 `validate_listing`）

**Agent：**

> 校验通过，这是上架草稿摘要：
>
> - 标题：`夏季轻盈雪纺连衣裙 女装收腰显瘦气质长裙 日常通勤都好搭`
> - 价格：199 元
> - 主图数量：1 张
> - 卖点：
>     1. 轻薄雪纺面料，夏天穿不闷热
>     2. 收腰版型，优化比例显腿长
>     3. 多尺码可选，日常通勤、约会都能穿
> - 小红书文案：
>      （此处给出完整 xhs_description 文本）

> 如果你觉得没问题，请回复“确认提交”或告诉我要修改的地方（如标题风格、卖点数量等）。

**用户：**

> 标题 OK，帮我提交。

**Agent：**

> 已根据你的确认调用商品库接口提交，返回结果：
>
> - 商品ID：`P123456`
> - 商品链接：`https://yourshop.com/product/P123456`
>      商品已创建成功，你可以在后台查看和补充更多图片 👍

------

## 八、需要你补充的信息（最多 5 个）

为了把这个 Agent 调到更“贴你店铺业务”的版本，我还需要你补充：

1. 你目前商品的**主类目**大概是哪些？（例如：美妆护肤 / 服饰鞋包 / 3C 数码 / 家居生活）
2. 商品库是否已经有 HTTP API？是你能改的后端吗？（决定工具接口能有多细/多强）
3. 当前 Excel/后台导出的一行数据，大概长什么样？能不能给一条**脱敏示例**字段顺序？
4. 你是否希望 **不同渠道（淘宝/抖音/小红书）生成不同风格的标题/卖点**？
5. MVP 阶段，你是希望“**必须人工确认再提交**”，还是“**默认自动提交，除非显式说只要文案**”？

------

## 九、两套实现思路（A/B 方案）

### A 方案：有现成商品库 API & 规范类目

- 工具和数据结构严格对齐你现有后端。
- `validate_listing` 可以调用你们类目服务校验属性枚举值、必填项等，更安全。
- 适合你这种工程背景，能快速往后端打接口。

### B 方案：只有 Excel，没有真正商品库 API（先不改后端）

- `submit_product_catalog` 暂时做成“写入一个中间数据库”或“生成 JSON 供人工导入”。
- Listing Agent 的价值偏“批量生成高质量标题+小红书文案+结构化草稿”。
- 之后再迭代接入正式商品库接口。

------

如果你愿意，**下一步可以给我一条你真实在用的 Excel 行（敏感信息去掉）**，我可以帮你把 `parse_listing_source` 的字段映射规则和 Agent Prompt 再优化到“贴你店铺”的版本。

👉 **简短问题**：
 你们目前上架的商品，主类目是哪个？（比如：美妆护肤 / 服饰 / 3C / 家居？）我可以先按这个类目给你加一点更针对性的标题 & 文案风格规则。