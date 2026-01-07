# 衍生品风险数据质量监控系统 - 架构设计

> 基于 Apache Airflow 的 FRTB 合规数据质量监控平台

---

## 🎯 项目定位

### 业务背景

本项目模拟**国际银行衍生品交易风险管理系统**的数据质量监控场景，重点关注：

1. **FRTB 监管合规**: 满足巴塞尔 FRTB 框架的数据质量要求
2. **实时风险监控**: 支持日内风险计算和回测
3. **多币种处理**: Dollarization 和跨币种风险聚合
4. **Greeks 计算**: Delta、Gamma、Vega 等风险指标的准确性
5. **CVA 监控**: 交易对手信用风险的数据质量

### 技术目标

通过实际业务场景，全面展示 **Apache Airflow** 的核心功能：

- ✅ 自定义组件开发 (Hook、Operator、Sensor)
- ✅ 复杂任务编排 (DAG、TaskGroup、依赖管理)
- ✅ 数据质量监控 (完整性、准确性、时效性)
- ✅ 动态 DAG 生成 (工厂模式、配置驱动)
- ✅ 条件分支执行 (BranchOperator、Trigger Rules)
- ✅ 告警与通知 (EmailOperator、Callbacks)
- ✅ 性能优化 (自定义 XCom Backend、并行处理)

---

## 📊 系统架构

### 整体架构图

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         Apache Airflow Scheduler                             │
│                    (调度和监控所有 DAG 的执行)                                 │
└────────────────────────────────┬────────────────────────────────────────────┘
                                 │
        ┌────────────────────────┼────────────────────────┐
        │                        │                        │
┌───────▼────────┐   ┌──────────▼──────────┐   ┌────────▼─────────┐
│ 市场数据采集    │   │ 风险数据质量监控    │   │ 监管报送准备      │
│ DAG            │   │ DAG                 │   │ DAG              │
└───────┬────────┘   └──────────┬──────────┘   └────────┬─────────┘
        │                       │                        │
        └───────────────────────┼────────────────────────┘
                                │
        ┌───────────────────────▼────────────────────────┐
        │            自定义组件层 (Plugins)                │
        │  ┌──────────┐  ┌──────────┐  ┌──────────┐     │
        │  │  Hooks   │  │Operators │  │ Sensors  │     │
        │  └──────────┘  └──────────┘  └──────────┘     │
        └───────────────────────┬────────────────────────┘
                                │
        ┌───────────────────────▼────────────────────────┐
        │              数据存储层                          │
        │  ┌──────────────┐    ┌──────────────┐         │
        │  │ PostgreSQL   │    │    MySQL     │         │
        │  │ (元数据)     │    │  (业务数据)   │         │
        │  └──────────────┘    └──────────────┘         │
        └─────────────────────────────────────────────────┘
```

### 核心 DAG 设计

#### 1. 市场数据采集 DAG (`market_data_pipeline`)

**目标**: 采集多资产类别的市场数据，模拟银行交易系统的数据源

```
market_data_pipeline
│
├── TaskGroup: equity_data_ingestion (股票数据)
│   ├── check_market_open (Sensor)
│   ├── download_stock_prices (Operator)
│   ├── validate_stock_data (Operator)
│   └── load_to_database (Operator)
│
├── TaskGroup: fx_data_ingestion (外汇数据)
│   ├── download_fx_rates (Operator)
│   ├── calculate_cross_rates (Operator)
│   ├── dollarization (Operator)
│   └── load_fx_data (Operator)
│
├── TaskGroup: rates_data_ingestion (利率数据)
│   ├── download_yield_curves (Operator)
│   ├── interpolate_curves (Operator)
│   └── load_rates_data (Operator)
│
└── TaskGroup: credit_data_ingestion (信用数据)
    ├── download_cds_spreads (Operator)
    ├── update_credit_ratings (Operator)
    └── load_credit_data (Operator)
```

**Airflow 特性展示**:
- ✅ TaskGroup 组织复杂任务
- ✅ 自定义 Sensor 检查市场状态
- ✅ 自定义 Operator 处理特定数据源
- ✅ XCom 在 TaskGroup 间传递数据

---

#### 2. 风险数据质量监控 DAG (`risk_data_quality_check`)

**目标**: 全面监控风险计算所需数据的质量，确保 FRTB 合规

```
risk_data_quality_check
│
├── TaskGroup: data_completeness_check (完整性检查)
│   ├── check_missing_prices (Operator)
│   ├── check_missing_fx_rates (Operator)
│   ├── check_missing_volatilities (Operator)
│   └── generate_completeness_report (Operator)
│
├── TaskGroup: data_accuracy_check (准确性检查)
│   ├── validate_price_ranges (Operator)
│   ├── validate_fx_cross_rates (Operator)
│   ├── validate_yield_curve_shape (Operator)
│   └── detect_outliers (Operator)
│
├── TaskGroup: data_timeliness_check (时效性检查)
│   ├── check_data_freshness (Sensor)
│   ├── check_eod_cutoff (Operator)
│   └── alert_stale_data (Operator)
│
├── TaskGroup: greeks_validation (Greeks 验证)
│   ├── calculate_portfolio_greeks (Operator)
│   ├── validate_delta_sum (Operator)
│   ├── validate_gamma_limits (Operator)
│   └── validate_vega_exposure (Operator)
│
├── quality_decision_branch (BranchOperator)
│   ├─ quality_passed → proceed_to_risk_calc
│   └─ quality_failed → send_alert_and_remediate
│
├── proceed_to_risk_calc (Operator)
│
└── send_alert_and_remediate (Operator)
    ├── send_email_alert (EmailOperator)
    ├── create_jira_ticket (Operator)
    └── trigger_data_refresh (TriggerDagRunOperator)
```

**Airflow 特性展示**:
- ✅ BranchOperator 条件分支
- ✅ Trigger Rules 灵活控制
- ✅ EmailOperator 告警通知
- ✅ TriggerDagRunOperator 触发其他 DAG
- ✅ 自定义 Sensor 检查数据时效性

---

#### 3. FRTB 回测验证 DAG (`frtb_backtesting`)

**目标**: 模拟 FRTB 要求的每日回测和 PLA 测试

```
frtb_backtesting
│
├── TaskGroup: prepare_backtest_data
│   ├── load_historical_positions (Operator)
│   ├── load_market_data (Operator)
│   └── calculate_hypothetical_pnl (Operator)
│
├── TaskGroup: var_backtesting
│   ├── calculate_var (Operator)
│   ├── compare_with_actual_pnl (Operator)
│   ├── count_exceptions (Operator)
│   └── update_traffic_light (Operator)
│
├── TaskGroup: pla_testing
│   ├── calculate_theoretical_pnl (Operator)
│   ├── calculate_actual_pnl (Operator)
│   ├── compute_correlation (Operator)
│   └── validate_pla_threshold (Operator)
│
├── backtest_result_branch (BranchOperator)
│   ├─ green_zone → generate_report
│   ├─ yellow_zone → escalate_warning
│   └─ red_zone → trigger_model_review
│
└── generate_regulatory_report (Operator)
```

**Airflow 特性展示**:
- ✅ 复杂的数据依赖管理
- ✅ 多层条件分支
- ✅ 动态任务生成（基于交易台数量）

---

#### 4. CVA 数据质量监控 DAG (`cva_data_quality`)

**目标**: 监控 CVA 计算所需的交易对手信用数据质量

```
cva_data_quality
│
├── TaskGroup: counterparty_data_check
│   ├── validate_credit_ratings (Operator)
│   ├── check_cds_spreads_availability (Operator)
│   ├── validate_pd_curves (Operator)
│   └── check_lgd_parameters (Operator)
│
├── TaskGroup: exposure_data_check
│   ├── validate_netting_sets (Operator)
│   ├── check_collateral_data (Operator)
│   ├── validate_mtm_values (Operator)
│   └── check_exposure_profiles (Operator)
│
├── TaskGroup: cva_calculation_validation
│   ├── calculate_expected_exposure (Operator)
│   ├── calculate_cva (Operator)
│   ├── validate_cva_sensitivities (Operator)
│   └── compare_with_previous_day (Operator)
│
└── generate_cva_quality_report (Operator)
```

---

#### 5. 动态交易台监控 DAG (`dynamic_desk_monitoring`)

**目标**: 根据配置动态生成每个交易台的监控任务

```python
# 动态生成 DAG 的工厂模式
def create_desk_monitoring_dag(desk_name, desk_config):
    """
    为每个交易台创建独立的监控 DAG
    
    Args:
        desk_name: 交易台名称 (e.g., 'equity_desk_asia')
        desk_config: 交易台配置 (产品类型、风险限额等)
    """
    dag = DAG(
        dag_id=f'desk_monitoring_{desk_name}',
        schedule_interval='@hourly',
        ...
    )
    
    with dag:
        # 动态创建任务
        check_position_limits = CheckPositionLimitsOperator(...)
        calculate_desk_greeks = CalculateGreeksOperator(...)
        validate_risk_limits = ValidateRiskLimitsOperator(...)
        
        check_position_limits >> calculate_desk_greeks >> validate_risk_limits
    
    return dag

# 从配置文件读取所有交易台
desks_config = Variable.get("trading_desks", deserialize_json=True)

# 动态生成 DAG
for desk_name, desk_config in desks_config.items():
    globals()[f'desk_monitoring_{desk_name}'] = create_desk_monitoring_dag(
        desk_name, desk_config
    )
```

**Airflow 特性展示**:
- ✅ 动态 DAG 生成
- ✅ DAG 工厂模式
- ✅ 配置驱动的任务创建

---

## 🔧 自定义组件设计

### 1. 自定义 Hooks

#### `RiskDataHook` (风险数据库 Hook)

```python
class RiskDataHook(MySqlHook):
    """
    管理风险数据库连接，提供风险数据专用操作
    """
    
    def get_position_data(self, desk_id, as_of_date):
        """获取交易台头寸数据"""
        
    def get_market_data(self, risk_factors, as_of_date):
        """获取市场数据"""
        
    def calculate_portfolio_greeks(self, portfolio_id):
        """计算组合 Greeks"""
        
    def dollarize_positions(self, positions, fx_rates):
        """美元化头寸"""
        
    def bulk_insert_risk_metrics(self, metrics):
        """批量插入风险指标"""
```

#### `MarketDataHook` (市场数据 Hook)

```python
class MarketDataHook(BaseHook):
    """
    连接外部市场数据源 (Bloomberg, Reuters等)
    """
    
    def get_stock_prices(self, symbols, date):
        """获取股票价格"""
        
    def get_fx_rates(self, currency_pairs, date):
        """获取外汇汇率"""
        
    def get_yield_curves(self, currencies, date):
        """获取收益率曲线"""
        
    def get_volatility_surface(self, underlying, date):
        """获取波动率曲面"""
```

---

### 2. 自定义 Operators

#### `GreeksCalculationOperator`

```python
class GreeksCalculationOperator(BaseOperator):
    """
    计算衍生品组合的 Greeks
    
    支持：Delta, Gamma, Vega, Theta, Rho
    """
    
    def execute(self, context):
        # 1. 获取头寸数据
        # 2. 获取市场数据
        # 3. 计算 Greeks
        # 4. 聚合到不同层级 (Position -> Desk -> Portfolio)
        # 5. 存储结果
```

#### `DollarizationOperator`

```python
class DollarizationOperator(BaseOperator):
    """
    将多币种头寸统一换算为美元等价
    
    处理：
    - 直接汇率换算
    - 交叉汇率计算
    - 历史汇率回溯
    """
    
    def execute(self, context):
        # 1. 获取所有币种头寸
        # 2. 获取最新汇率
        # 3. 执行 Dollarization
        # 4. 验证换算结果
        # 5. 存储美元等价头寸
```

#### `DataQualityCheckOperator`

```python
class DataQualityCheckOperator(BaseOperator):
    """
    执行数据质量检查
    
    检查维度：
    - 完整性 (Completeness)
    - 准确性 (Accuracy)
    - 一致性 (Consistency)
    - 时效性 (Timeliness)
    """
    
    template_fields = ('check_rules', 'threshold')
    
    def execute(self, context):
        # 1. 加载检查规则
        # 2. 执行各项检查
        # 3. 计算质量分数
        # 4. 生成质量报告
        # 5. 触发告警（如果需要）
```

#### `CVACalculationOperator`

```python
class CVACalculationOperator(BaseOperator):
    """
    计算交易对手信用估值调整 (CVA)
    
    计算流程：
    1. 计算预期暴露 (Expected Exposure)
    2. 获取违约概率 (PD)
    3. 应用违约损失率 (LGD)
    4. 计算 CVA
    """
    
    def execute(self, context):
        # 实现 CVA 计算逻辑
```

---

### 3. 自定义 Sensors

#### `MarketDataReadySensor`

```python
class MarketDataReadySensor(BaseSensorOperator):
    """
    检查市场数据是否已就绪
    
    检查项：
    - 所有必需的价格数据已到达
    - 数据时间戳符合要求
    - 数据质量通过初步验证
    """
    
    mode = 'reschedule'  # 节省资源
    
    def poke(self, context):
        # 检查数据就绪状态
```

#### `RiskLimitSensor`

```python
class RiskLimitSensor(BaseSensorOperator):
    """
    监控风险限额使用情况
    
    当风险接近限额时触发告警
    """
    
    def poke(self, context):
        # 检查风险限额
        # 返回 True 触发下游任务
```

---

### 4. 自定义 XCom Backend

#### `CompressedXComBackend`

```python
class CompressedXComBackend(BaseXCom):
    """
    优化大数据量传输
    
    特性：
    - 自动压缩 (gzip)
    - DataFrame 优化序列化
    - 性能监控
    """
    
    @staticmethod
    def serialize_value(value, **kwargs):
        # 智能序列化和压缩
        
    @staticmethod
    def deserialize_value(result):
        # 解压缩和反序列化
```

---

## 📋 数据模型设计

### 核心数据表

#### 1. 市场数据表

```sql
-- 股票价格
CREATE TABLE stock_prices (
    symbol VARCHAR(10),
    date DATE,
    open DECIMAL(18,4),
    high DECIMAL(18,4),
    low DECIMAL(18,4),
    close DECIMAL(18,4),
    volume BIGINT,
    data_source VARCHAR(50),
    quality_score DECIMAL(5,2),
    created_at TIMESTAMP,
    PRIMARY KEY (symbol, date)
);

-- 外汇汇率
CREATE TABLE fx_rates (
    currency_pair VARCHAR(10),
    date DATE,
    rate DECIMAL(18,6),
    bid DECIMAL(18,6),
    ask DECIMAL(18,6),
    data_source VARCHAR(50),
    is_cross_rate BOOLEAN,
    created_at TIMESTAMP,
    PRIMARY KEY (currency_pair, date)
);

-- 收益率曲线
CREATE TABLE yield_curves (
    currency VARCHAR(3),
    date DATE,
    tenor VARCHAR(10),
    rate DECIMAL(10,6),
    curve_type VARCHAR(20),
    created_at TIMESTAMP,
    PRIMARY KEY (currency, date, tenor)
);
```

#### 2. 风险数据表

```sql
-- 头寸数据
CREATE TABLE positions (
    position_id VARCHAR(50),
    desk_id VARCHAR(50),
    security_id VARCHAR(50),
    quantity DECIMAL(18,4),
    currency VARCHAR(3),
    usd_equivalent DECIMAL(18,2),
    as_of_date DATE,
    created_at TIMESTAMP,
    PRIMARY KEY (position_id, as_of_date)
);

-- Greeks 数据
CREATE TABLE greeks (
    position_id VARCHAR(50),
    as_of_date DATE,
    delta DECIMAL(18,6),
    gamma DECIMAL(18,6),
    vega DECIMAL(18,6),
    theta DECIMAL(18,6),
    rho DECIMAL(18,6),
    calculation_timestamp TIMESTAMP,
    PRIMARY KEY (position_id, as_of_date)
);

-- CVA 数据
CREATE TABLE cva_metrics (
    counterparty_id VARCHAR(50),
    as_of_date DATE,
    expected_exposure DECIMAL(18,2),
    cva_amount DECIMAL(18,2),
    dva_amount DECIMAL(18,2),
    credit_rating VARCHAR(10),
    pd DECIMAL(10,6),
    lgd DECIMAL(5,2),
    created_at TIMESTAMP,
    PRIMARY KEY (counterparty_id, as_of_date)
);
```

#### 3. 数据质量表

```sql
-- 数据质量检查结果
CREATE TABLE data_quality_checks (
    check_id VARCHAR(50),
    check_type VARCHAR(50),
    data_source VARCHAR(50),
    as_of_date DATE,
    check_result VARCHAR(20),
    quality_score DECIMAL(5,2),
    issues_found INT,
    details TEXT,
    checked_at TIMESTAMP,
    PRIMARY KEY (check_id, as_of_date)
);

-- 数据质量指标
CREATE TABLE quality_metrics (
    metric_name VARCHAR(50),
    metric_value DECIMAL(10,4),
    as_of_date DATE,
    data_source VARCHAR(50),
    threshold_min DECIMAL(10,4),
    threshold_max DECIMAL(10,4),
    is_within_threshold BOOLEAN,
    created_at TIMESTAMP,
    PRIMARY KEY (metric_name, as_of_date, data_source)
);
```

---

## 🔄 核心业务流程

### 流程 1: 日终风险数据处理

```
1. 市场收盘 (16:00 EST)
   ↓
2. 触发 market_data_pipeline DAG
   ↓
3. 并行采集各类市场数据
   - 股票价格
   - 外汇汇率
   - 利率曲线
   - 信用利差
   ↓
4. 数据质量初步验证
   ↓
5. 加载到数据库
   ↓
6. 触发 risk_data_quality_check DAG
   ↓
7. 全面数据质量检查
   ↓
8. 质量通过？
   ├─ Yes → 触发风险计算
   └─ No → 发送告警，启动补救流程
```

### 流程 2: FRTB 回测流程

```
1. 每日早上 (09:00)
   ↓
2. 触发 frtb_backtesting DAG
   ↓
3. 加载昨日头寸和市场数据
   ↓
4. 计算 VaR / ES
   ↓
5. 对比实际 P&L
   ↓
6. 统计例外次数
   ↓
7. 更新交通灯状态
   ├─ Green Zone → 正常运行
   ├─ Yellow Zone → 发出警告
   └─ Red Zone → 触发模型审查
   ↓
8. 生成监管报告
```

---

## 🎯 Airflow 核心特性映射

### 特性矩阵

| Airflow 特性 | 实现组件 | 业务场景 | 技术亮点 |
|-------------|---------|---------|---------|
| **自定义 Hook** | `RiskDataHook`<br>`MarketDataHook` | 风险数据库连接<br>市场数据源连接 | 连接池管理<br>批量操作优化<br>事务控制 |
| **自定义 Operator** | `GreeksCalculationOperator`<br>`DollarizationOperator`<br>`DataQualityCheckOperator`<br>`CVACalculationOperator` | Greeks 计算<br>多币种换算<br>数据质量检查<br>CVA 计算 | 模板化参数<br>幂等性设计<br>错误处理<br>性能优化 |
| **自定义 Sensor** | `MarketDataReadySensor`<br>`RiskLimitSensor` | 数据就绪检测<br>风险限额监控 | Reschedule 模式<br>超时控制<br>Poke 间隔优化 |
| **TaskGroup** | `equity_data_ingestion`<br>`quality_check` | 任务逻辑分组<br>提高可读性 | 嵌套 TaskGroup<br>依赖管理 |
| **BranchOperator** | `quality_decision_branch`<br>`backtest_result_branch` | 基于质量的条件分支<br>基于回测结果分支 | 动态路由<br>Trigger Rules |
| **XCom** | 所有 DAG 间数据传递 | 任务间数据共享 | 自定义序列化<br>大数据优化 |
| **自定义 XCom Backend** | `CompressedXComBackend` | 大数据量传输优化 | Gzip 压缩<br>性能监控 |
| **动态 DAG** | `dynamic_desk_monitoring` | 交易台级别监控 | DAG 工厂模式<br>配置驱动 |
| **Variables** | 全局配置管理 | 交易台配置<br>风险限额配置 | JSON 序列化<br>动态更新 |
| **Connections** | 数据库连接<br>API 连接 | MySQL<br>PostgreSQL<br>外部数据源 | 安全管理<br>连接池 |
| **EmailOperator** | 告警通知 | 数据质量告警<br>风险限额告警 | HTML 邮件<br>附件支持 |
| **TriggerDagRunOperator** | DAG 间触发 | 质量检查触发风险计算 | 参数传递<br>条件触发 |
| **SubDagOperator** | 复杂子流程 | 回测子流程 | 独立调度<br>错误隔离 |
| **Callbacks** | `on_failure_callback`<br>`on_success_callback` | 任务失败处理<br>成功通知 | 自定义逻辑<br>告警集成 |

---

## 📈 性能优化策略

### 1. 并行处理

```python
# 并行采集多个数据源
with TaskGroup('parallel_data_ingestion'):
    equity_task = download_equity_data()
    fx_task = download_fx_data()
    rates_task = download_rates_data()
    credit_task = download_credit_data()
    
    # 所有任务并行执行
```

### 2. 增量处理

```python
# 只处理增量数据
def download_incremental_data(**context):
    last_run = context['prev_execution_date']
    current_run = context['execution_date']
    
    # 只下载 last_run 到 current_run 之间的数据
```

### 3. 分区处理

```python
# 按交易台分区处理
for desk_id in desk_ids:
    process_desk_data.override(task_id=f'process_{desk_id}')(desk_id)
```

### 4. 缓存机制

```python
# 使用 XCom 缓存中间结果
ti.xcom_push(key='market_data_cache', value=market_data)
```

---

## 🔐 安全与合规

### 1. 数据访问控制

- 使用 Airflow RBAC 控制 DAG 访问权限
- 敏感数据加密存储
- 审计日志记录所有操作

### 2. 监管合规

- **FRTB 合规**: 满足每日回测和 PLA 测试要求
- **数据保留**: 至少保留 3 年历史数据
- **审计追踪**: 完整的数据血缘和变更记录

### 3. 灾难恢复

- 定期备份 Airflow 元数据库
- 业务数据多副本存储
- DAG 代码版本控制

---

## 📊 监控与告警

### 1. DAG 监控

- DAG 执行时间监控
- 任务失败率统计
- SLA 违规告警

### 2. 数据质量监控

- 实时质量分数仪表板
- 质量趋势分析
- 异常自动告警

### 3. 系统性能监控

- Airflow Scheduler 性能
- 数据库连接池状态
- 任务队列长度

---

## 🚀 部署架构

### Docker Compose 部署

```yaml
services:
  airflow-webserver:
    image: apache/airflow:2.10.5
    environment:
      - AIRFLOW__CORE__EXECUTOR=LocalExecutor
      - AIRFLOW__CORE__LOAD_EXAMPLES=false
    volumes:
      - ./dags:/opt/airflow/dags
      - ./plugins:/opt/airflow/plugins
    ports:
      - "8080:8080"
  
  airflow-scheduler:
    image: apache/airflow:2.10.5
    command: scheduler
  
  postgres:
    image: postgres:13
    environment:
      - POSTGRES_DB=airflow
  
  mysql:
    image: mysql:8.0
    environment:
      - MYSQL_DATABASE=risk_data
```

---

## 📚 技术栈总结

| 类别 | 技术 | 用途 |
|------|------|------|
| **工作流引擎** | Apache Airflow 2.10.5 | 任务编排和调度 |
| **编程语言** | Python 3.11.5 | DAG 开发和数据处理 |
| **元数据库** | PostgreSQL 13 | Airflow 元数据存储 |
| **业务数据库** | MySQL 8.0 | 风险数据和市场数据 |
| **容器化** | Docker & Docker Compose | 环境隔离和部署 |
| **数据处理** | Pandas, NumPy | 数据分析和计算 |
| **测试框架** | Pytest | 单元测试和集成测试 |
| **监控** | Airflow UI, Prometheus (可选) | 系统监控和告警 |

---

## 🎓 学习价值

### 对于 Airflow 学习者

1. **全面的特性覆盖**: 涵盖 Airflow 90% 以上的核心功能
2. **真实业务场景**: 基于实际金融行业需求
3. **最佳实践**: 展示工程化的 DAG 设计模式
4. **性能优化**: 学习大规模数据处理的优化技巧

### 对于金融从业者

1. **FRTB 理解**: 深入理解监管要求
2. **风险系统**: 了解风险管理系统架构
3. **数据质量**: 掌握数据质量监控方法
4. **自动化**: 学习风险计算自动化实践

---

## 📖 相关文档

- [实施计划](IMPLEMENTATION_PLAN.md)
- [快速开始指南](GETTING_STARTED.md)
- [项目结构说明](PROJECT_STRUCTURE.md)
- [开发指南](DEVELOPMENT.md)
- [文档索引](INDEX.md)

---

**文档版本**: v2.0  
**最后更新**: 2025-10-31  
**维护者**: DataQuality-Airflow Team
