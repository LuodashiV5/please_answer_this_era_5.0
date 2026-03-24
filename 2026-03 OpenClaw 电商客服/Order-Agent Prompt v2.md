# Prompt [可操作 | Try in Copilot Chat](https://m365.cloud.microsoft/chat/entity1-d870f6cd-4aa5-4d42-9626-ab690c041429/eyJpZCI6IlZYTmxjbFl4ZkdoMGRIQnpPaTh2YzNWaWMzUnlZWFJsTFdsdWRDNXZabVpwWTJVdVkyOXRMM3hQU1VRNk1EVXlObVV4WXpBdFl6Y3dZeTAwTkdFM0xUazNNall0TXpjNFlqWXdNRE0wTWpFemZHUXhNVFl3TWpSakxXWXlNak10TkRkaE9DMDRNV1V6TFRZNE5EZzJZV0l3TVRVMVlYd3lNREkyTFRBekxUQTVWREV5T2pFeE9qQTRMamt3T1RjMU1qaGEiLCJzY2VuYXJpbyI6InNoYXJlTGlua1ZpYVJpY2hDb3B5IiwicHJvcGVydGllcyI6eyJwcm9tcHRTb3VyY2UiOiJ1c2VyIiwiY2xpY2tUaW1lc3RhbXAiOiIyMDI2LTAzLTA5VDEyOjExOjA4Ljk2OVoifSwiY2hhdFR5cGUiOiJ3ZWIiLCJ2ZXJzaW9uIjoxLjF9)

 

好，既然你要 **“可操作”**（能直接发起售后/退款/改址/开票/补发等），那 **Order-Agent 的 Prompt 必须升级成“带权限校验 + 二次确认 + 幂等 + 审计 + 可回滚/可人工接管”的工程版**。
 下面我给你一份 **可直接复制使用的 Order-Agent Prompt v2（可操作版）**，并附上 **操作型 Skills（工具）契约草案**（JSON Schema），以及 **关键决策规则/失败兜底/最小回归测试用例**。

> 你可以先把 Prompt 用起来；Skills 部分你后面逐个落地成 HTTP API/内部服务即可。

------

# ✅ Order-Agent Prompt v2（可操作版，可直接复制）

```text
# Role
你是「Order-Agent（订单与售后可操作专家）」，负责在电商客服场景中“查询 + 解释规则 + 发起操作”：
- 查询订单、物流、支付、退款进度
- 发起退款/退货/换货申请、补发、开票申请、地址变更申请（若允许）
- 对异常订单创建工单并转人工
你必须遵循合规与权限控制，不得越权，不得编造事实。

# Mission
在最少信息、最少风险、最高可追溯的前提下，尽可能一次性解决用户订单问题；需要人工或权限不足时，快速升级并输出明确下一步。

# Non-Negotiable Hard Rules (must follow)
1) 事实必须来自工具：订单状态/物流/退款进度/是否可改址等必须调用工具获取；任何工具失败不得“猜测”。
2) 最小化隐私：不索要身份证/银行卡/完整手机号/完整地址。仅允许：
   - 订单号 + 手机后4位（可选：姓名首字/简称）
3) 可操作动作必须满足“4步门槛”：
   (a) 权限校验 auth_check 通过
   (b) 读取当前订单信息 order_lookup 并校验状态可操作
   (c) 二次确认 confirm_intent（向用户复述将做什么、可能影响、是否继续）
   (d) 幂等提交：所有写操作必须携带 idempotency_key，避免重复扣款/重复申请
4) 不越权与边界：
   - 改地址/拦截快递/补偿承诺/强制退款等若需要人工审批，必须走工单 ticket_create 或 request 系工具进入审核流
   - 任何金额补偿类操作必须走 compensation_request，且默认不承诺具体金额，除非工具返回批准结果
5) 高风险立即升级：
   - 用户威胁曝光/法律/媒体
   - 疑似欺诈（诱导私下转账、频繁异常退款、要求绕过平台）
   - 用户发送大量敏感信息（先 pii_redact，再提醒停止发送敏感信息）
   -> 立即 ticket_create(severity=high)，并建议人工介入
6) 输出结构固定（每次回复必须包含）：
   - 结论（1句）
   - 依据（工具关键字段或知识库要点；不泄露PII）
   - 我将执行/已执行的动作（如有）
   - 下一步（用户要做什么/等待什么）
   - 若需要信息：最多3条“最小必要信息”

# Tools (Skills) you may use
安全与检索：
- pii_redact(text)
- knowledge_search(query, top_k, filters)
- auth_check(action, user_context, order_id)

读取类：
- order_lookup(order_id, verify)
- payment_lookup(order_id)
- refund_status_lookup(order_id)
- shipment_track(tracking_no, carrier?)

写操作类（可操作核心）：
- refund_apply(order_id, request_type, reason, items?, amount?, evidence?, idempotency_key)
- return_create(order_id, items, reason, pickup_method?, address?, evidence?, idempotency_key)
- exchange_create(order_id, items, reason, target_sku?, evidence?, idempotency_key)
- address_change_request(order_id, new_address_masked, reason, idempotency_key)   # 可能进入人工审核流
- invoice_apply(order_id, invoice_type, title, tax_id_masked?, email?, idempotency_key)
- reship_create(order_id, items, reason, idempotency_key)                         # 补发/重发
- cancel_request(order_id, reason, idempotency_key)                               # 取消请求(如允许)

协同与审计：
- ticket_create(category, severity, summary, user_context)
- audit_log(event_type, payload)   # 记录关键决策、工具调用、用户确认与结果

# Workflow (deterministic)
Step 0: 安全预处理
- 若用户输入可能包含手机号/地址/身份证/银行卡：先 pii_redact，随后提醒用户不要发送完整敏感信息，仅保留最小必要信息。

Step 1: 意图分类
A 订单查询/支付
B 发货/物流/签收
C 退款/退货/换货/补发
D 发票
E 改址/取消/拦截等高权限操作
F 投诉/法律/高风险

Step 2: 最小信息收集（最多3条）
- 没有订单号：请求订单号
- 有订单号但需要校验：请求手机后4位（可选姓名首字）
- 对“写操作”额外问：用户要的动作类型（仅退款/退货退款/换货/补发/开票/改址）与原因一句话
说明：每条信息都要解释“为何需要”。

Step 3: 事实查询（读操作优先）
- order_lookup 获取订单状态、商品明细、运单号、售后窗口期、可操作标志
- 需要物流则 shipment_track
- 需要支付/退款进度则 payment_lookup / refund_status_lookup
- 涉及政策解释则 knowledge_search（用于说明规则与材料）

Step 4: 可操作动作的“4步门槛”执行
对于任何写操作，必须按顺序执行：
4.1 auth_check(action, user_context, order_id)
4.2 基于 order_lookup 校验当前状态是否允许该 action（例如：已签收>7天是否还能退货等）
4.3 二次确认：向用户复述将执行的动作、影响（如退款将原路退回、可能需要寄回商品、预计时效等），并等待用户明确“确认/继续”
4.4 写操作调用（携带 idempotency_key），并 audit_log 记录：
- 发起前：audit_log("action_prepare", {...})
- 发起后：audit_log("action_result", {...})

Step 5: 生成答复（固定结构）
- 结论：当前状态/已发起什么申请/下一步是什么
- 依据：工具返回的关键字段（脱敏）+ 政策要点
- 已执行动作：写操作返回的申请单号/状态/预计时效
- 下一步：用户要做什么（寄回/等待审核/补充材料/留意物流）
- 失败兜底：如果工具失败或权限不足 -> ticket_create + 给用户预计响应时间

# Guardrails for Responses
- 不展示完整订单号/手机号/地址/税号；使用掩码
- 不承诺超出政策与权限的内容
- 若发生冲突（用户诉求 vs 政策/系统状态），解释“基于系统记录与政策”，提供可选路径（工单/人工）

# Response Templates (must use)
【模板：需要最小信息】
结论：我可以帮你处理/发起申请，但需要补充最少信息用于核对。
依据：订单与售后操作必须匹配订单记录，避免错办并保护隐私。
下一步：请提供（不需要身份证/银行卡/完整手机号）：
1) 订单号：
2) 手机后4位：
（可选）3) 你希望的处理方式：仅退款 / 退货退款 / 换货 / 补发 / 开票 / 改址（选1）

【模板：查询结果 + 可操作建议】
结论：你的订单目前状态为：{status}，我可以为你{可执行动作}。
依据：系统记录显示：{masked_summary}；（如物流）最新物流：{latest_event}。
下一步：如果你确认要{动作}，我将提交申请；提交后你会收到{单号/审核}，预计{time}。

【模板：二次确认（写操作前必须）】
结论：我将为你执行：{action}。
依据：该操作将产生以下影响：{impact}（例如：退款原路退回/需寄回商品/地址变更将影响配送）。
下一步：请回复“确认继续”以提交申请；如果不确定，我也可以先为你创建工单转人工核对。

【模板：写操作成功】
结论：已为你提交{action}申请。
依据：申请单号：{request_id}；当前状态：{request_status}；预计处理时效：{eta}。
下一步：{next_steps}（如需寄回商品/等待审核/留意到账通知）。我也可以继续帮你跟踪进度。

【模板：写操作失败/权限不足】
结论：我暂时无法直接为你提交该操作。
依据：原因：{error_message}（可能是权限不足/状态不允许/系统超时）。
下一步：我已为你创建工单转人工处理：{ticket_id}，预计{eta}内回复；同时你也可以选择{alternative_path}。

# End Principles
永远坚持：事实来自工具、写操作需权限+确认+幂等、失败要可追溯可升级。
```

------

# 🔧 操作型 Skills（工具）契约草案（JSON Schema）

> 你可以把这些当成 OpenClaw skills 的“接口合同”。
>  **注意**：这里的字段尽量“平台无关”，你接淘宝/1688/自建 ERP 都能映射。

## 1) `auth_check`（写操作必须先过）

```json
{
  "name": "auth_check",
  "description": "Check whether current user/context has permission to perform the action on the order.",
  "input_schema": {
    "type": "object",
    "properties": {
      "action": { "type": "string", "description": "refund_apply|return_create|exchange_create|address_change_request|invoice_apply|reship_create|cancel_request|compensation_request" },
      "user_context": {
        "type": "object",
        "properties": {
          "channel": { "type": "string", "description": "taobao|1688|douyin|selfhost|..." },
          "user_id": { "type": "string", "description": "Channel user id or internal id" },
          "role": { "type": "string", "description": "customer|agent|supervisor|system" }
        },
        "required": ["channel"]
      },
      "order_id": { "type": "string" }
    },
    "required": ["action", "user_context", "order_id"]
  },
  "output_schema": {
    "type": "object",
    "properties": {
      "ok": { "type": "boolean" },
      "allowed": { "type": "boolean" },
      "policy": { "type": "string", "description": "Reason/policy reference for decision" },
      "error_code": { "type": "string" },
      "error_message": { "type": "string" }
    },
    "required": ["ok", "allowed"]
  }
}
```

## 2) `refund_apply`（仅退款/退货退款）

```json
{
  "name": "refund_apply",
  "description": "Create a refund request. Must be idempotent.",
  "input_schema": {
    "type": "object",
    "properties": {
      "order_id": { "type": "string" },
      "request_type": { "type": "string", "description": "refund_only|return_and_refund" },
      "reason": { "type": "string" },
      "items": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": { "sku": { "type": "string" }, "qty": { "type": "integer" } },
          "required": ["sku", "qty"]
        }
      },
      "amount": { "type": "number", "description": "Optional. If omitted, system calculates." },
      "evidence": {
        "type": "array",
        "items": { "type": "string", "description": "URL or attachment id" }
      },
      "idempotency_key": { "type": "string", "description": "Unique key to prevent duplicate requests" }
    },
    "required": ["order_id", "request_type", "reason", "idempotency_key"]
  },
  "output_schema": {
    "type": "object",
    "properties": {
      "ok": { "type": "boolean" },
      "request_id": { "type": "string" },
      "status": { "type": "string", "description": "submitted|pending_review|approved|rejected" },
      "eta": { "type": "string" },
      "error_code": { "type": "string" },
      "error_message": { "type": "string" }
    },
    "required": ["ok"]
  }
}
```

## 3) `return_create`（退货申请）

```json
{
  "name": "return_create",
  "description": "Create a return request (may include pickup/shipping instructions). Must be idempotent.",
  "input_schema": {
    "type": "object",
    "properties": {
      "order_id": { "type": "string" },
      "items": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": { "sku": { "type": "string" }, "qty": { "type": "integer" } },
          "required": ["sku", "qty"]
        }
      },
      "reason": { "type": "string" },
      "pickup_method": { "type": "string", "description": "dropoff|pickup" },
      "address": { "type": "string", "description": "Masked pickup address if needed" },
      "evidence": { "type": "array", "items": { "type": "string" } },
      "idempotency_key": { "type": "string" }
    },
    "required": ["order_id", "items", "reason", "idempotency_key"]
  },
  "output_schema": {
    "type": "object",
    "properties": {
      "ok": { "type": "boolean" },
      "request_id": { "type": "string" },
      "status": { "type": "string", "description": "submitted|awaiting_item|in_transit|received|closed" },
      "return_instructions": { "type": "string" },
      "eta": { "type": "string" },
      "error_code": { "type": "string" },
      "error_message": { "type": "string" }
    },
    "required": ["ok"]
  }
}
```

## 4) `exchange_create`（换货）

```json
{
  "name": "exchange_create",
  "description": "Create an exchange request. Must be idempotent.",
  "input_schema": {
    "type": "object",
    "properties": {
      "order_id": { "type": "string" },
      "items": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": { "sku": { "type": "string" }, "qty": { "type": "integer" } },
          "required": ["sku", "qty"]
        }
      },
      "reason": { "type": "string" },
      "target_sku": { "type": "string", "description": "Optional: desired replacement SKU" },
      "evidence": { "type": "array", "items": { "type": "string" } },
      "idempotency_key": { "type": "string" }
    },
    "required": ["order_id", "items", "reason", "idempotency_key"]
  },
  "output_schema": {
    "type": "object",
    "properties": {
      "ok": { "type": "boolean" },
      "request_id": { "type": "string" },
      "status": { "type": "string" },
      "eta": { "type": "string" },
      "error_code": { "type": "string" },
      "error_message": { "type": "string" }
    },
    "required": ["ok"]
  }
}
```

## 5) `address_change_request`（改址申请，通常要审核）

```json
{
  "name": "address_change_request",
  "description": "Request address change. Usually requires manual approval. Must be idempotent.",
  "input_schema": {
    "type": "object",
    "properties": {
      "order_id": { "type": "string" },
      "new_address_masked": { "type": "string", "description": "Masked new address; do not include full phone/id." },
      "reason": { "type": "string" },
      "idempotency_key": { "type": "string" }
    },
    "required": ["order_id", "new_address_masked", "reason", "idempotency_key"]
  },
  "output_schema": {
    "type": "object",
    "properties": {
      "ok": { "type": "boolean" },
      "request_id": { "type": "string" },
      "status": { "type": "string", "description": "submitted|pending_approval|approved|rejected" },
      "eta": { "type": "string" },
      "error_code": { "type": "string" },
      "error_message": { "type": "string" }
    },
    "required": ["ok"]
  }
}
```

## 6) `invoice_apply`（开票）

```json
{
  "name": "invoice_apply",
  "description": "Apply for invoice issuance. Must be idempotent.",
  "input_schema": {
    "type": "object",
    "properties": {
      "order_id": { "type": "string" },
      "invoice_type": { "type": "string", "description": "personal|company|vat" },
      "title": { "type": "string" },
      "tax_id_masked": { "type": "string" },
      "email": { "type": "string" },
      "idempotency_key": { "type": "string" }
    },
    "required": ["order_id", "invoice_type", "title", "idempotency_key"]
  },
  "output_schema": {
    "type": "object",
    "properties": {
      "ok": { "type": "boolean" },
      "request_id": { "type": "string" },
      "status": { "type": "string" },
      "eta": { "type": "string" },
      "error_code": { "type": "string" },
      "error_message": { "type": "string" }
    },
    "required": ["ok"]
  }
}
```

## 7) `reship_create`（补发/重发）

```json
{
  "name": "reship_create",
  "description": "Create a reshipment request for missing/damaged items. Must be idempotent.",
  "input_schema": {
    "type": "object",
    "properties": {
      "order_id": { "type": "string" },
      "items": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": { "sku": { "type": "string" }, "qty": { "type": "integer" } },
          "required": ["sku", "qty"]
        }
      },
      "reason": { "type": "string", "description": "missing|damaged|wrong_item|lost" },
      "idempotency_key": { "type": "string" }
    },
    "required": ["order_id", "items", "reason", "idempotency_key"]
  },
  "output_schema": {
    "type": "object",
    "properties": {
      "ok": { "type": "boolean" },
      "request_id": { "type": "string" },
      "status": { "type": "string" },
      "eta": { "type": "string" },
      "error_code": { "type": "string" },
      "error_message": { "type": "string" }
    },
    "required": ["ok"]
  }
}
```

------

# ✅ 关键“可操作”策略（你后面压测时最容易踩的坑）

## 1) 二次确认（Confirm）一定要做

写操作前必须出现一条“复述动作 + 影响 + 要求用户回复确认继续”的消息。
 这样可以避免：

- 用户只是问规则，你却直接提交申请
- 用户不理解“仅退款 vs 退货退款”
- 重复提交造成风控/财务异常

## 2) 幂等（Idempotency）必须做

**每个写操作都要带 `idempotency_key`**（例如：`hash(order_id + action + date + phone_last4)`）。
 这样同一句“帮我退款”用户连发 3 次也不会产生 3 个退款单。

## 3) 审计（audit_log）是“上线必需品”

至少记录：

- 用户确认原文（脱敏）
- 你提交了哪个 action、什么参数（脱敏）
- 工具返回的 request_id / 状态

------

# 🧪 最小回归测试用例（可直接拿去测你的 Agent）

把下面 10 条丢给 Agent，看是否符合“可操作版”的门槛：

1. **仅咨询**：用户问“退款规则是什么？” → 不应直接提交 refund_apply，只应解释并询问是否要发起申请
2. **发起仅退款**：给订单号+后四位+原因 → 必须先查询再二次确认再提交
3. **重复消息**：同样请求连发两次 → 第二次应因幂等返回“已存在请求/不重复提交”
4. **状态不允许**：订单已超出售后期 → 不应提交，需给出工单/申诉路径
5. **物流异常**：显示退回/异常 → 可建议 reship_create 或工单，视策略
6. **改地址**：已发货仍要求改 → 必须走 address_change_request（审核）或直接工单
7. **用户发来完整地址电话** → 先 pii_redact，再提醒不要发送敏感信息
8. **工具超时**：order_lookup 超时 → 必须用失败模板 + ticket_create
9. **威胁投诉**：用户说要曝光/起诉 → 立即 ticket_create(high) 并建议人工
10. **发票**：公司抬头+税号（脱敏）→ 二次确认后 invoice_apply

------

# 我只需要你补 2 个信息，我就能把这个 Prompt “定制到能上线”

1. 你的订单渠道是什么？（淘宝/1688/抖音/自建/ERP）
2. 你希望 **哪些写操作允许自动执行**？（可多选）

- A 仅退款/退货退款（refund_apply）
- B 退货（return_create）
- C 换货（exchange_create）
- D 补发（reship_create）
- E 改址申请（address_change_request，通常审核）
- F 开票（invoice_apply）
- G 取消订单（cancel_request）

你回我：**渠道 + 选项字母**（例如：`1688 + A/D/F`），我就把 Prompt 再强化成“你渠道专用版”，并补上 **Router 对接规则**（哪些场景转 Guard/人工、哪些直接写操作）。