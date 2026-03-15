

根据你的报错信息和问题，核心结论是：**阿里云百炼的 Coding Plan 套餐（包括 DeepSeek 模型）是可以在 Dify 平台使用的，但必须使用特定的“套餐专属 API Key”和“套餐专属 Base URL”，不能直接使用百炼通用 API Key。**

你遇到的 `401 Invalid API-key provided`错误，通常是因为使用了错误的 API Key 类型或配置了错误的接口地址。

### 1. 为什么报错 401？

阿里云百炼的 API Key 分为两种，用途完全不同：

- **通用 API Key**：格式通常为 `sk-xxxxx`，用于按量计费（Pay-As-You-Go）模式。
    
- **Coding Plan 专属 API Key**：格式通常为 `sk-sp-xxxxx`，专门用于抵扣套餐额度。
    

**如果你在 Dify 中填入了通用 API Key，系统会认为你没有购买套餐，从而拒绝访问并报错 401。**

### 2. 如何在 Dify 中正确配置 Coding Plan？

请按照以下步骤操作，确保使用套餐额度：

**第一步：获取正确的配置信息**

1. 登录 [阿里云百炼控制台](https://bailian.console.aliyun.com/)。
    
2. 进入 **订阅套餐**​ 页面，找到你购买的 Coding Plan。
    
3. 点击 **获取 API Key**，记录下格式为 `sk-sp-xxxxx`的密钥。
    
4. 记录 **Base URL**（接口地址）：
    
    - **OpenAI 兼容协议**：`https://coding.dashscope.aliyuncs.com/v1`
        
    - **Anthropic 兼容协议**：`https://coding.dashscope.aliyuncs.com/apps/anthropic`
        
    

**第二步：在 Dify 中配置模型**

1. 在 Dify 的 **设置**​ -> **模型供应商**​ 中，找到并安装 **通义千问（百炼）**​ 插件。
    
2. 点击 **添加模型**，选择 **自定义 API**。
    
3. 填写以下参数：
    
    - **模型名称**：自定义，如 `DeepSeek-CodingPlan`。
        
    - **API 端点 (Endpoint)**：填写第一步记录的 Base URL（推荐使用 OpenAI 兼容协议）。
        
    - **API Key**：填写第一步获取的 `sk-sp-xxxxx`。
        
    - **请求头**：添加 `Content-Type: application/json`。
        
    
4. 保存并测试连通性。
    

### 3. 关于 DeepSeek 模型的使用

Coding Plan 套餐支持 DeepSeek 模型（如 `deepseek-v3.2`），但在 Dify 中配置时，**建议使用“通义千问”插件**。因为阿里云百炼的 DeepSeek 服务通常通过兼容模式提供，使用通义千问的接口协议可以无缝接入。

**总结**：请务必使用 `sk-sp-xxxxx`开头的专属密钥和 `coding.dashscope.aliyuncs.com`开头的专属地址，而不是通用的 `dashscope.aliyuncs.com`地址。

---
这个错误表明，**即使你填写了 `sk-sp-xxxxx`开头的 API Key，Dify 的通义千问插件仍在尝试调用错误的 API 端点**（很可能是在调用标准的通义千问 API 地址，而不是 Coding Plan 的专属地址）。

这是新版 Dify 通义千问插件的一个已知问题：插件默认硬编码了标准地址，不支持自动识别和切换到 Coding Plan 的地址。

### 🔧 解决方案：在 Dify 中使用“OpenAI 兼容”方式手动配置

由于插件存在限制，目前最可靠的方法是将 Coding Plan 当作一个 **自定义的 OpenAI 兼容模型**​ 来配置。具体步骤如下：

#### 1. 获取正确的配置信息

登录 [阿里云百炼控制台](https://bailian.console.aliyuncs.com/)-> 找到你的 Coding Plan -> 获取：

- **API Key**: `sk-sp-xxxxxxxxxxxxx`
    
- **Base URL**: `https://coding.dashscope.aliyuncs.com/v1`
    

#### 2. 在 Dify 中配置模型供应商

1. 在 Dify 的 **设置**​ -> **模型供应商**​ 中，找到并安装 **OpenAI**​ 插件。
    
2. 点击“添加模型”，选择 **自定义 API**。
    
3. 填写以下参数：
    
    - **模型名称**：任意自定义，例如 `CodingPlan-DeepSeek`。
        
    - **模型ID**：这里填入你想调用的模型名称，比如：
        
        - `deepseek-v3`
            
        - `deepseek-r1`
            
        - `qwen-max`
            
        
    - **API 端点 (Endpoint)**：填入上面的 Base URL: `https://coding.dashscope.aliyuncs.com/v1`
        
    - **API Key**：填入上面的专属 API Key: `sk-sp-xxxxxxxxxxxxx`
        
    
4. 保存。
    

#### 3. 验证模型可用性

保存成功后，在模型列表中找到刚刚添加的模型，尝试“发送消息”进行测试。

---

### 为什么这个方法有效？

1. **地址正确**：`https://coding.dashscope.aliyuncs.com/v1`是 Coding Plan 的专属地址，插件会正确路由到你的套餐资源。
    
2. **认证匹配**：`sk-sp-`开头的专属 API Key 和专属地址是配对的，百炼服务器能识别这是套餐调用。
    

### 📋 补充：模型 ID 参考

在 Base URL 中指定了 Coding Plan 端点后，可用的模型 ID 包括：

|模型 ID|说明|
|---|---|
|`deepseek-v3`|DeepSeek 最新版本|
|`deepseek-r1`|DeepSeek 推理模型|
|`qwen-max`|通义千问旗舰版|
|`qwen-plus`|通义千问高性能版|

**注意**：请务必在 **同一个模型配置**​ 中同时使用 **专属 API Key**​ 和 **专属 Base URL**，二者缺一不可。

### 🚨 常见错误排查

- 如果仍报 401，请检查 API Key 是否从 Coding Plan 套餐页面获取，而不是从“API-KEY 管理”页面获取的通用 Key。
    
- 确认模型 ID 拼写正确（区分大小写）。
    
- 确保你的 Coding Plan 套餐仍在有效期内且有剩余额度。
    

如果以上步骤仍无法解决，可能需要联系阿里云百炼客服，确认你的 Coding Plan 套餐是否已正确激活。

---

根据你使用的 **阿里云百炼 Coding Plan**​ 套餐，**4096**​ 这个数值通常是指 Dify 平台默认的“最大上下文长度”限制。这个限制需要根据你实际调用的模型能力来调整。

### 1. 核心建议：改到 128K 或 256K

对于 Coding Plan 中的主流模型（如 Qwen3.5-Plus、GLM-5、MiniMax-M2.5 等），建议将 Dify 中的上下文长度设置为 **128000**​ 或 **256000**。

- **128K (128000)**：这是目前大多数模型的标准长上下文能力，适合处理绝大多数文档和代码库。
    
- **256K (256000)**：如果你需要处理超长文档（如整本书、大型代码库），或者使用支持 256K 的模型（如 Qwen3.5-Plus），可以设置为此值。
    

### 2. 为什么不能改到 1M？

虽然 Coding Plan 中部分模型（如 Qwen3.5-Plus）支持 1M 上下文，但 **Dify 平台目前对上下文长度有硬性限制**，通常最大只支持到 **256K**。如果你在 Dify 中设置超过 256K 的值，可能会被系统自动截断或报错。

### 3. 具体操作步骤

在 Dify 中修改模型配置：

1. 进入 **设置**​ -> **模型供应商**。
    
2. 找到你配置的 Coding Plan 模型（如 `CodingPlan-DeepSeek`）。
    
3. 点击 **编辑**，找到 **最大上下文长度 (Max Tokens)**​ 字段。
    
4. 将数值修改为 **128000**​ 或 **256000**。
    
5. 保存配置。
    

### 4. 模型能力参考

以下是 Coding Plan 中主要模型的上下文能力，供你参考：

| 模型名称                  | 上下文长度 (Token) | 建议 Dify 设置            |
| --------------------- | ------------- | --------------------- |
| **Qwen3.5-Plus**​     | 1,000,000     | 256,000 (受限于 Dify 平台) |
| **Qwen3-Coder-Plus**​ | 1,000,000     | 256,000 (受限于 Dify 平台) |
| **GLM-5**​            | 202,752       | 128,000               |
| **MiniMax-M2.5**​     | 1,000,000     | 256,000 (受限于 Dify 平台) |
| **Kimi-K2.5**​        | 262,144       | 128,000               |

---
这个报错 `model 'CodingPlan-DeepSeek' is not supported`非常关键，它揭示了 Dify 在尝试验证模型时的核心逻辑：**Dify 的“OpenAI-API-compatible”插件在点击保存时，会自动调用百炼的 `/models`接口来验证你填写的模型名称是否存在。**

之所以报错，是因为你在 **“API endpoint中的模型名称”**​ 这一栏填写的内容，百炼的模型列表里**查不到**。百炼的模型名称是固定的（如 `qwen3-coder-plus`），不能自定义为 `CodingPlan-DeepSeek`。

请按照以下步骤修正配置，这是目前唯一能打通的路线：

### 1. 修正核心参数（最关键一步）

请在 **“API endpoint中的模型名称”**​ 这一栏填入百炼支持的**具体模型代号**。

- **如果你要调用 通义千问（Qwen）Coding 模型**：
    
    - 填入：`qwen3-coder-plus`
        
    - _备选（如果上面报错）：_`qwen3-coder`
        
    
- **如果你要调用 DeepSeek 模型**：
    
    - 填入：`deepseek-r1`
        
    - 或者：`deepseek-v3`
        
    

> **注意**：这里的名称必须是百炼官方文档或控制台中列出的真实名称，不能是中文或你随意起的名字。

### 2. 确认 Base URL（Endpoint）

请确保 **“API endpoint URL”**​ 填写的是 Coding Plan 的专属地址：

- **正确地址**：`https://coding.dashscope.aliyuncs.com/v1`
    

### 3. 为什么之前会报错？

Dify 的逻辑是：你填了模型名 `CodingPlan-DeepSeek`-> Dify 去问百炼：“请问有叫 `CodingPlan-DeepSeek`的模型吗？” -> 百炼说没有 -> Dify 报错 `is not supported`。

### 4. 最终验证步骤

1. 修改 **“API endpoint中的模型名称”**​ 为 `qwen3-coder-plus`（或其他有效模型名）。
    
2. **删除**​ **“模型显示名称”**（或者随便填个简单的，如 `Qwen`），因为这个字段有时会干扰验证逻辑。
    
3. **点击保存**。
    
4. **点击“添加模型”按钮旁边的“测试连接”**（如果有），或者直接去“应用”里选这个模型试发一条消息。
    

按照这个修正后，Dify 就能在百炼的模型列表里找到对应的模型，验证就能通过了。

关联
 
[[快速原型类 Dify 或FastGPT 初探]] 