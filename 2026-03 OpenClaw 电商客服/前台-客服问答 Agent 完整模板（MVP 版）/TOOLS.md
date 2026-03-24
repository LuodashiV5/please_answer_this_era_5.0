# Agent Tools

## 工具清单

### 1. query_chemical_spec
查询化学品技术规格参数

**JSON Schema**:
```json
{
  "name": "query_chemical_spec",
  "description": "查询化工产品的技术参数（纯度、水分、杂质等）",
  "parameters": {
    "type": "object",
    "properties": {
      "product_name": {"type": "string", "description": "产品名称（如：工业级二氯甲烷）"},
      "cas_number": {"type": "string", "description": "CAS号（可选）"},
      "parameters": {
        "type": "array",
        "items": {"enum": ["purity", "moisture", "impurity", "ph", "density"]},
        "description": "需要查询的参数列表"
      },
      "supplier_id": {"type": "string", "description": "供应商ID（1688店铺ID）"}
    },
    "required": ["product_name", "parameters"]
  }
}
对接方式:

MVP：查询本地 CSV 知识库（data/chemical_specs.csv）
Prod：调用1688商品详情 API + 供应商 ERP 接口
返回示例:


{
  "product_name": "工业级二氯甲烷",
  "cas_number": "75-09-2",
  "specs": {
    "purity": {"value": "≥99.5%", "standard": "GB/T 4117-2008"},
    "moisture": {"value": "≤0.01%", "standard": "企标"},
    "impurity": {"chloride": "≤0.005%"}
  },
  "source": "supplier_erp",
  "last_updated": "2026-03-10"
}

### 2. request_coa_document
索取质量分析报告（COA）或安全技术说明书（MSDS）

JSON Schema:


{
  "name": "request_coa_document",
  "description": "向供应商索取 COA/MSDS 等质量文件",
  "parameters": {
    "type": "object",
    "properties": {
      "supplier_id": {"type": "string", "description": "供应商ID"},
      "product_id": {"type": "string", "description": "产品ID"},
      "batch_number": {"type": "string", "description": "批次号"},
      "document_type": {
        "type": "string",
        "enum": ["COA", "MSDS", "SGS", "TDS"],
        "description": "文件类型"
      }
    },
    "required": ["supplier_id", "product_id", "document_type"]
  }
}
对接方式:

MVP：发送站内信至供应商，人工跟进
Prod：调用供应商文档管理系统 API，自动获取
返回示例:


{
  "document_type": "COA",
  "batch_number": "20260301-A",
  "file_url": "https://cdn.1688.com/docs/coa_20260301.pdf",
  "file_size": "2.3MB",
  "test_date": "2026-03-01",
  "test_org": "SGS",
  "has_official_seal": true
}
###  3. validate_supplier_qualification
验证供应商资质（危险化学品经营许可证等）

JSON Schema:


{
  "name": "validate_supplier_qualification",
  "description": "检查供应商是否具备相应资质",
  "parameters": {
    "type": "object",
    "properties": {
      "supplier_id": {"type": "string"},
      "product_category": {
        "type": "string",
        "enum": ["hazardous_chemical", "food_additive", "pharmaceutical_intermediate"]
      }
    },
    "required": ["supplier_id", "product_category"]
  }
}
对接方式:

MVP：查询1688店铺认证信息
Prod：对接国家应急管理部危化品许可证查询系统
返回示例:


{
  "supplier_name": "XX化工有限公司",
  "qualifications": [
    {
      "type": "危险化学品经营许可证",
      "number": "沪应急管危经（2025）001234",
      "valid_until": "2027-12-31",
      "status": "valid"
    }
  ],
  "missing": [],
  "risk_level": "low"
}
###  工具调用逻辑

# 伪代码
def handle_inquiry(query):
    intent = classify_intent(query)
    
    if intent == "technical_spec":
        result = query_chemical_spec(
            product_name=extract_product(query),
            parameters=extract_params(query)
        )
        if result.success:
            return format_spec_response(result)
        else:
            return FALLBACK_SPEC_MESSAGE
    
    elif intent == "coa_request":
        result = request_coa_document(
            supplier_id=get_supplier_id(),
            batch_number=extract_batch(query),
            document_type="COA"
        )
        if result.file_url:
            return format_coa_response(result)
        else:
            return FALLBACK_COA_MESSAGE + escalate_to_human()
    
    elif intent == "supplier_qualification":
        result = validate_supplier_qualification(
            supplier_id=get_supplier_id(),
            product_category="hazardous_chemical"
        )
        return format_qualification_response(result)

