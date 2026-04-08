本 Agent 可用工具列表（由后端实现并接入 OpenClaw）：

---

## 1. 解析 Excel 化工品数据：`parse_excel_products`

**用途**：  
将包含化工原料商品信息的 Excel 文件解析成原始商品列表，字段可为原始形式（如 raw_xxx），由 Agent 做进一步映射与清洗。

**调用参数(JSON Schema)：**

```json
{
  "name": "parse_excel_products",
  "description": "Parse an Excel file that contains chemical raw material product data and return a list of raw products.",
  "parameters": {
    "type": "object",
    "properties": {
      "file_id": {
        "type": "string",
        "description": "Unique identifier or path of the Excel file stored in the system."
      },
      "sheet_name": {
        "type": "string",
        "description": "Optional sheet name. If not provided, use the first sheet."
      },
      "header_row_index": {
        "type": "integer",
        "description": "Row index where header starts (1-based).",
        "default": 1
      },
      "max_rows": {
        "type": "integer",
        "description": "Optional max number of rows to parse for preview/testing."
      }
    },
    "required": ["file_id"]
  }
}

期望返回示例：
{
  "products": [
    {
      "raw_chemical_name_cn": "三乙醇胺",
      "raw_chemical_name_en": "Triethanolamine",
      "raw_CAS_number": "102-71-6",
      "raw_purity": "99%",
      "raw_grade": "工业级",
      "raw_application": "表面活性剂, 缓蚀剂",
      "raw_packaging": "230KG/桶",
      "raw_price": "12345",
      "raw_stock": "1000",
      "raw_images": ["https://..."],
      "origin_data": {
        "excel_row_index": 5,
        "source": "excel"
      }
    }
  ]
}
