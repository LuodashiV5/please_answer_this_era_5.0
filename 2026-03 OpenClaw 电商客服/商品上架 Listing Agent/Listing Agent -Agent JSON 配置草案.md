好，直接上干货 💪  
下面给你一个**可直接当“模板”用的 Listing Agent JSON 配置草案**，你只需要按你们实际的 OpenClaw 配置结构调整字段名即可。

***

## 一、结论摘要

1.  我给你的是一个 **单 Agent JSON 配置草案**：`ListingAgent`，专做“商品上架”。
2.  包含：`meta`、`systemPrompt`、`tools` 绑定、`behavior`（是否自动提交）、`safety` 等字段。
3.  工具列表已经绑定了你之前设计的 3 个核心工具：`parse_listing_source` / `validate_listing` / `submit_product_catalog`。
4.  你可以：
    *   在 OpenClaw 后台的“自定义 Agent”里新建 → 选择“JSON 编辑模式”（如果支持），直接粘；
    *   或者按你们平台需要，把里面的 prompt 和 tools 拆开填入。
5.  JSON 已经内嵌了完整的 System Prompt（上一轮为你写的那个，略做了 JSON 转义）。

***

## 二、完整 JSON 配置草案（MVP）

> ⚠️ 注意：
>
> *   `id` / `tools[].id` / `tools[].endpoint` / `auth` 等字段需要你改成自己的实际值；
> *   如果你们 OpenClaw 的 Agent 配置结构不同，只要把里面的 `systemPrompt` 和 `tools` 部分搬过去即可。

```json
{
  "id": "listing-agent-mvp",
  "name": "商品上架 Listing Agent",
  "description": "将 Excel/后台数据转换为可上架的商品结构，自动生成标题/卖点/小红书文案，并在校验通过和人工确认后提交商品库。",
  "type": "assistant",
  "language": "zh-CN",
  "owner": "ops-team-or-dev", 
  "visibility": "private",
  "tags": ["ecommerce", "listing", "catalog", "xhs", "internal"],

  "systemPrompt": "你是一个电商商品上架助手（Listing Agent），服务对象是内部运营/商家人员。你不直接面对消费者，你的目标是：\n1）把 Excel 或后台导出的原始数据转换为结构化商品数据；\n2）自动生成适合电商平台的商品标题和卖点；\n3）自动生成符合小红书风格的种草型介绍文案；\n4）在数据完整、校验通过且用户确认的前提下，将商品提交到商品库。\n\n[输入数据类型]\n运营可能会给你以下几种形式的输入：\n- Excel 的一行（复制出来的文本，可能是用逗号/制表符分隔的字段）\n- 后台导出的 JSON 或类似结构\n- 同时包含类目信息、品牌、价格、库存、图片链接等\n\n你要先判断输入类型，再调用工具 parse_listing_source 进行标准化解析。\n\n[工作步骤（必须严格按照顺序执行）]\n1）理解任务：\n  - 搞清楚用户是要「新建商品」还是「补全/修改」已有商品（如果用户没说，默认是新建）。\n  - 搞清楚目标渠道（如用户未指明，默认以“通用电商平台”为目标）。\n\n2）解析原始数据：\n  - 使用工具 parse_listing_source，把 Excel/后台数据转换为统一的 ProductDraft 结构。\n  - 若解析失败，明确告诉用户失败原因，并请用户重新提供更清晰的数据格式（比如列出你需要的字段名）。\n\n3）补全和优化商品信息（在你内部思考中完成，无需暴露中间过程）：\n  - 在尊重原始数据的前提下，对标题、卖点、描述做“文案级加工”，但不得捏造不存在的参数、证书、功效等。\n  - 对于缺失但又必需的信息（如类目、品牌、价格等），先向用户发问确认，禁止自行编造。\n\n4）生成以下结构化结果：\n  - 标准化商品结构（JSON 格式），包含至少：\n    - 标题（title）\n    - 卖点数组（selling_points，3-5 条，每条不超过40字）\n    - 价格（price）\n    - 库存（stock）\n    - 类目（category_name）\n    - 品牌（brand）\n    - 关键属性（attributes）\n    - 图片链接（images）\n    - SEO 关键词（seo_keywords，5-10 个）\n    - 小红书风格长文案（xhs_description）\n  - 小红书文案风格要求：\n    - 口吻：第一人称/朋友安利口吻，真实分享，不要像广告文案。\n    - 内容结构建议：\n      1）开头 1-2 句抓注意力，说明使用场景或痛点；\n      2）中间分点描述产品亮点、使用感受、适用人群（可适当使用 emoji）；\n      3）结尾给出种草/下单理由，避免夸大宣传。\n    - 禁止使用绝对化用语（如“永久”“最强”“百分百”），避免涉及功效承诺的敏感描述。\n\n5）调用 validate_listing 工具：\n  - 把你生成后的完整商品数据传给 validate_listing。\n  - 若校验不通过：\n    - 明确告诉用户：哪些字段缺失/不合法；\n    - 给出修改建议；\n    - 请求用户补充信息后再重新校验。\n\n6）提交商品库：\n  - 当且仅当：\n    - 商品数据结构完整，\n    - validate_listing 返回通过，\n    - 用户明确表达“确认提交/可以上架/帮我入库”这类语句，\n    - 才调用 submit_product_catalog。\n  - 调用成功后，将返回的商品ID或链接清晰地反馈给用户。\n  - 若提交失败，向用户说明失败原因，并建议联系技术/人工处理。\n\n[风格与格式要求]\n- 优先使用 JSON 代码块返回结构化商品数据，便于开发调试。\n- 跟用户对话时，语言要简洁、工程化：\n  - 先给关键信息摘要（标题+价格+主图链接总数）；\n  - 再给完整 JSON；\n  - 最后给小红书文案。\n- 如果有不确定的地方，一定要提问澄清，不要自作主张。\n\n[安全与合规]\n- 不得虚构不存在的检测报告、资质、疗效；\n- 不得输出违法或平台明令禁止的内容；\n- 对于涉及年龄、功效等敏感商品（如保健品、医美），请保守描述，必要时提醒运营补充专业文案或走合规审核。\n\n[工具使用原则]\n- 任何时候你只通过工具与商品库交互，不直接假设“已经成功保存”。\n- 工具超时/失败时，向用户说明：“商品库接口暂时不可用，请稍后重试或联系技术”。",

  "behavior": {
    "allowToolCalls": true,
    "requireUserConfirmationBeforeSubmit": true,
    "autoSummarizeBeforeAskConfirm": true,
    "maxListingPerConversation": 50,
    "defaultChannel": "generic",
    "supportedChannels": ["taobao", "tmall", "douyin", "xhs", "self"]
  },

  "tools": [
    {
      "id": "parse-listing-source",
      "name": "解析上架源数据",
      "type": "http",
      "description": "将 Excel 行文本或后台 JSON 转为标准化 ProductDraft 结构。",
      "endpoint": {
        "method": "POST",
        "url": "https://your-api-domain.com/api/listing/parse",
        "headers": {
          "Content-Type": "application/json"
        },
        "auth": {
          "type": "bearer",
          "tokenEnvKey": "LISTING_API_TOKEN"
        }
      },
      "inputSchema": {
        "type": "object",
        "properties": {
          "source_type": {
            "type": "string",
            "enum": ["excel_row", "backend_json"]
          },
          "raw_content": {
            "type": "string",
            "description": "Original content, e.g. a CSV line or JSON string"
          },
          "channel": {
            "type": "string",
            "description": "sales channel, e.g. taobao, douyin, xhs, self"
          }
        },
        "required": ["raw_content"]
      },
      "outputSchema": {
        "$ref": "#/components/schemas/ProductDraft"
      },
      "timeoutMs": 8000,
      "retries": 1
    },
    {
      "id": "validate-listing",
      "name": "校验商品上架数据",
      "type": "http",
      "description": "根据类目和平台规则校验商品是否满足上架要求。",
      "endpoint": {
        "method": "POST",
        "url": "https://your-api-domain.com/api/listing/validate",
        "headers": {
          "Content-Type": "application/json"
        },
        "auth": {
          "type": "bearer",
          "tokenEnvKey": "LISTING_API_TOKEN"
        }
      },
      "inputSchema": {
        "$ref": "#/components/schemas/FullProduct"
      },
      "outputSchema": {
        "type": "object",
        "properties": {
          "valid": {
            "type": "boolean"
          },
          "errors": {
            "type": "array",
            "items": {
              "type": "string"
            }
          },
          "warnings": {
            "type": "array",
            "items": {
              "type": "string"
            }
          }
        },
        "required": ["valid"]
      },
      "timeoutMs": 8000,
      "retries": 1
    },
    {
      "id": "submit-product-catalog",
      "name": "提交商品到商品库",
      "type": "http",
      "description": "将完整的商品信息写入商品库，返回商品ID和访问地址。",
      "endpoint": {
        "method": "POST",
        "url": "https://your-api-domain.com/api/catalog/products",
        "headers": {
          "Content-Type": "application/json"
        },
        "auth": {
          "type": "bearer",
          "tokenEnvKey": "CATALOG_API_TOKEN"
        }
      },
      "inputSchema": {
        "$ref": "#/components/schemas/FullProduct"
      },
      "outputSchema": {
        "type": "object",
        "properties": {
          "success": {
            "type": "boolean"
          },
          "product_id": {
            "type": "string"
          },
          "product_url": {
            "type": "string"
          },
          "message": {
            "type": "string"
          }
        },
        "required": ["success"]
      },
      "timeoutMs": 8000,
      "retries": 0
    }
  ],

  "components": {
    "schemas": {
      "ProductDraft": {
        "type": "object",
        "properties": {
          "source_type": {
            "type": "string"
          },
          "channel": {
            "type": "string"
          },
          "category_name": {
            "type": "string"
          },
          "brand": {
            "type": "string"
          },
          "sku_code": {
            "type": "string"
          },
          "spu_code": {
            "type": "string"
          },
          "raw_title": {
            "type": "string"
          },
          "raw_description": {
            "type": "string"
          },
          "attributes": {
            "type": "object",
            "additionalProperties": true
          },
          "price": {
            "type": "number"
          },
          "stock": {
            "type": "integer"
          },
          "images": {
            "type": "array",
            "items": {
              "type": "string"
            }
          },
          "extra": {
            "type": "object",
            "additionalProperties": true
          }
        },
        "required": ["raw_title"]
      },
      "FullProduct": {
        "type": "object",
        "properties": {
          "title": {
            "type": "string"
          },
          "selling_points": {
            "type": "array",
            "items": {
              "type": "string"
            }
          },
          "price": {
            "type": "number"
          },
          "stock": {
            "type": "integer"
          },
          "category_name": {
            "type": "string"
          },
          "brand": {
            "type": "string"
          },
          "attributes": {
            "type": "object",
            "additionalProperties": true
          },
          "images": {
            "type": "array",
            "items": {
              "type": "string"
            }
          },
          "seo_keywords": {
            "type": "array",
            "items": {
              "type": "string"
            }
          },
          "xhs_description": {
            "type": "string"
          },
          "channel": {
            "type": "string"
          },
          "sku_code": {
            "type": "string"
          },
          "spu_code": {
            "type": "string"
          },
          "extra": {
            "type": "object",
            "additionalProperties": true
          }
        },
        "required": ["title", "price", "category_name", "images"]
      }
    }
  },

  "safety": {
    "allowSensitiveEfficacyClaims": false,
    "blockAbsoluteClaims": true,
    "logToolErrors": true
  }
}
```

***

## 三、如何在你那边落地（简单步骤）

1.  **在 OpenClaw 管理后台 → 新建 Agent**
2.  如果平台有“JSON 配置模式”，可以：
    *   新建后，把上面 JSON 粘进去
    *   修改：
        *   `endpoint.url`
        *   `auth` 方式
        *   `id` / `owner`
3.  如果平台只支持 UI 配置：
    *   把 `systemPrompt` 字段内容复制到“系统 Prompt”
    *   工具：按 `tools` 中的 3 个，分别添加 HTTP 工具
    *   对应的 `inputSchema` / `outputSchema` 填到 JSON Schema 里
4.  用一条真实的 Excel 行测试：
    *   看是否调用 `parse-listing-source`
    *   检查生成的 JSON 结构是否符合你的后端需求

***

## 四、简单测试用例（你可以拿来直接喂 Agent）

### 用例 1：普通女装商品行

```text
帮我上架这个商品，这是 Excel 复制的一行：
夏季雪纺连衣裙,女装,示例品牌,199,100,黑色;M;L;XL,https://img.xxx/1.jpg
```

期望：

*   自动调用 `parse-listing-source`
*   生成标题、3–5 条卖点、小红书文案
*   校验通过后，等你说“确认提交”再调 `submit-product-catalog`

***

### 下一步可以优化的两个点

1.  **按类目加风格规则**  
    比如主做“女装”，可以在 Prompt 里再加一小段：标题要突出版型+场景，小红书文案多讲“上身效果、遮肉、搭配”等。
2.  **按渠道分支**  
    在 Prompt 里再补充：“渠道=小红书时，标题更生活化；渠道=淘宝时，标题可以多一些属性词和场景词”。

***

最后问你一个**关键小问题（方便我给你再精简一版 JSON）**：  
👉 你现在商品库的 HTTP API 域名是统一的吗？比如 `https://api.xxx.com` 这一类？如果你告诉我一个**假域名结构**，我可以帮你把多个 endpoint 合并成更贴你现状的风格。
