# Agent Initialization

## 启动时执行

### 1. 加载知识库
```python
# 加载常见化工原料参数表
chemical_db = load_csv("data/chemical_specs.csv")
# 加载危险化学品目录
hazardous_list = load_json("data/hazardous_chemicals.json")
# 加载标准话术模板
templates = load_yaml("config/response_templates.yaml")

### 2. 验证工具连接

# 测试1688 API 连接
test_1688_api_connection()
# 测试供应商 ERP 接口（Prod）
if ENV == "production":
    test_supplier_erp_connection()
### 3. 初始化日志

# 创建询盘日志表
init_inquiry_log_table()
# 设置监控指标
setup_metrics(["response_time", "success_rate", "fallback_rate"])

### 4. 预热模型

# 预加载意图分类模型
load_intent_classifier()
# 预加载实体抽取模型（产品名、参数名）
load_ner_model()
健康检查
每 5 分钟执行一次：

检查知识库更新时间（>24h 则告警）
检查工具可用性（失败率 >10% 则告警）
检查人工兜底队列长度（>50 则告警）


