# Periodic Tasks

## 每日任务（00:00 执行）

### 1. 同步供应商资质有效期
```python
def daily_sync_qualifications():
    suppliers = get_active_suppliers()
    for supplier in suppliers:
        qualifications = validate_supplier_qualification(supplier.id)
        if qualifications.has_expiring_soon(days=30):
            send_alert(f"供应商 {supplier.name} 资质即将过期")

2. 更新化工原料价格指数

def daily_update_price_index():
    # 从公开数据源抓取价格趋势
    price_data = fetch_chemical_price_index()
    update_knowledge_base(price_data)
3. 生成询盘分析报告

def daily_generate_report():
    metrics = {
        "total_inquiries": count_inquiries(yesterday),
        "top_products": get_top_products(yesterday),
        "fallback_rate": calculate_fallback_rate(yesterday),
        "avg_response_time": calculate_avg_response_time(yesterday)
    }
    send_report_to_admin(metrics)
每周任务（周一 08:00 执行）
1. 清理过期 COA 缓存

def weekly_cleanup_cache():
    delete_expired_documents(days=90)
2. 重新训练意图分类模型

def weekly_retrain_model():
    new_data = get_labeled_inquiries(last_week)
    if len(new_data) > 100:
        retrain_intent_classifier(new_data)
实时监控（每分钟）
1. 检测异常询盘

def monitor_anomalies():
    if get_inquiry_rate(last_minute) > 100:
        send_alert("询盘量异常激增，可能遭受攻击")


---

## 工具定义（JSON Schema 完整版）

已在 TOOLS.md 中给出，这里补充对接说明：

| 工具 | MVP 实现 | Prod 实现 | 预计响应时间 |
|------|---------|----------|------------|
| query_chemical_spec | 本地 CSV 查询 | 1688 API + 供应商 ERP | <500ms |
| request_coa_document | 站内信 + 人工 | 供应商文档系统 API | <2s |
| validate_supplier_qualification | 1688店铺认证 | 国家应急管理部 API | <1s |

---

## 路由策略

### 关键词路由（MVP）
```python
INTENT_KEYWORDS = {
    "technical_spec": ["纯度", "含量", "指标", "参数", "规格", "水分", "杂质"],
    "coa_request": ["COA", "MSDS", "检测报告", "质检", "SGS", "TDS"],
    "supplier_qualification": ["资质", "证书", "许可证", "认证"]
}
LLM 路由（Prod）

# 使用 Claude 进行意图分类
prompt = f"""
用户询盘：{query}
请分类为以下意图之一：
1. technical_spec - 技术参数确认
2. coa_request - 文件索取
3. supplier_qualification - 资质验证
4. other - 其他

只返回意图名称。
"""
intent = claude_classify(prompt)
失败兜底话术
技术参数查询失败

抱歉，暂时无法查询到该产品的详细参数。
建议您：
1. 查看供应商店铺的产品详情页
2. 直接联系供应商技术支持（电话：138****1234）
3. 申请样品并索取该批次的 COA 报告

💡 正规供应商应提供符合国标/行标的技术参数表。
COA 索取失败

抱歉，该批次的 COA 报告暂未上传到系统。
建议您：
1. 在1688订单页面点击"申请质量文件"
2. 联系供应商客服索取（响应时效：3个工作日）
3. 如急需，可转接人工客服加急处理

⚠️ 提示：采购前务必确认 COA 加盖供应商公章。
资质验证失败

⚠️ 风险提示：该供应商的资质信息不完整。
缺失项：
- 危险化学品经营许可证

建议：
1. 要求供应商补充资质证明
2. 选择具备完整资质的其他供应商
3. 咨询平台合规顾问（转接入口）

💡 采购危险化学品时，供应商必须具备相应许可证。
