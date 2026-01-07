# ROADMAP

目标: 2026-01 将 `RiskDataQuality-Airflow` 收敛为可演示, 可复现, 可维护的最小版本.

优先级说明:

- 该项目在 2026-01 的优先级低于 `RiskAgent-MultiAgent` 与 `RiskKnowGraph-GraphRAG`.
- 当且仅当 P0 达标后, 再推进本项目.

依据文档:

- `docs/IMPLEMENTATION_PLAN.md`(原计划 6 周).

## 2026-01 收敛范围

只做到里程碑 M2:

- M1: Day 7, 基础组件完成
- M2: Day 14, 核心 DAG 完成

也就是:

- 自定义组件最小集合(Hook, Operator, Sensor)可用
- `market_data_pipeline` 与 `basic_quality_check` 两条 DAG 可运行并可演示

## 硬性验收口径

- Docker Compose 一键启动可用
- Airflow Web UI 可访问, DAG 可触发, 任务可成功
- 至少 1 条 e2e 路径可复现
- 无明文 secrets

## 2026-01 1 个月计划(按周)

### Week 1: 环境与最小 DAG 骨架

- 交付
  - `./scripts/start.sh` 一键启动可用
  - DAG 可被 Airflow 正常加载
  - 数据库连接配置走环境变量
- 验收
  - Airflow Web UI 可访问
  - 至少 1 个 DAG 可触发运行并成功

### Week 2: 自定义组件最小集合(Hook, Operator, Sensor)

- 交付
  - 至少 1 个 Hook
  - 至少 1 个 Operator
  - 至少 1 个 Sensor
  - 对应单元测试
- 验收
  - 组件可在 DAG 中被引用并运行
  - 测试可在本地运行

### Week 3: 两条核心 DAG 跑通

- 交付
  - `market_data_pipeline` 可跑通
  - `basic_quality_check` 可跑通
  - 基础质量检查输出可落盘或可在 UI 中查看
- 验收
  - UI 可触发 DAG, 任务成功
  - 至少 1 份质量检查结果可复现

### Week 4: 文档与最小告警

- 交付
  - docs 更新: 快速开始, 配置说明, 常见问题
  - 最小告警: failure callback 或 EmailOperator(二选一)
  - demo 脚本: 端到端跑一遍
- 验收
  - 出错时能触发告警
  - 新人按 README 10-15 分钟可跑通 demo
