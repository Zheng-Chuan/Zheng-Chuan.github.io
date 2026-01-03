# ROADMAP

目标: 2026-01 用 1 个月集中完成 AI Agent 能力展示, 并形成可复现可演示的作品集.

## 优先级

- P0: 必须优先完成
  - `RiskAgent-MultiAgent`
  - `RiskKnowGraph-GraphRAG`
- P1: 在 P0 达标后继续推进
  - `RiskMonitor-MCP`
  - `RiskDataQuality-Airflow`

## 总体验收口径

- 可运行: 新人按 README 10-15 分钟可跑通关键 demo.
- 可复现: 有固定命令与固定输入, 输出可落盘.
- 可解释: 输出有证据链或可追溯来源, 能复述数据流.
- 工程化底线: 无明文 secrets, 至少 1 条 e2e smoke test, 关键日志可定位.

## 2026-01 计划(按周)

时间分配建议(用于你做时间预算):

- P0 主线(MultiAgent + GraphRAG): 70%-80%
- P1 次线(MCP + Airflow): 20%-30%

降级规则:

- 若 P0 在任一周末未达成该周验收, 下一周先保证 P0 达标, P1 只保留 10% 时间做保活(修 bug, 补文档, 不开新 scope).
- 若 P0 连续 2 周滞后, 暂停 P1, 直到 P0 回到计划节奏.

### Week 1 (01-03 to 01-09): P0 baseline 跑通

- `RiskAgent-MultiAgent`
  - 目标: 跑通最小 RAG + 多智能体编排闭环, 形成可演示 demo.
  - 依据: `RiskAgent-MultiAgent/docs/ROADMAP.md`.
  - 验收: UI 或 CLI 能 ingest -> retrieve -> answer, 并返回 citations.

- `RiskKnowGraph-GraphRAG`
  - 目标: 跑通开源 GraphRAG baseline, 锁定环境与版本.
  - 依据: `RiskKnowGraph-GraphRAG/docs/ROADMAP.md`.
  - 验收: index 与 query 跑通, 产物可落盘.

- P1 时间窗口(建议 20%-30%)
  - `RiskMonitor-MCP`
    - 目标: Phase 0 收尾启动, 明确 1 条最小 demo 路径.
    - 交付: 连接池与超时策略设计草案, 模块拆分计划.
    - 验收: 写清楚 demo 脚本大纲与数据口径.
  - `RiskDataQuality-Airflow`
    - 目标: 环境可启动, DAG 可加载.
    - 交付: Docker Compose 一键启动通过, 1 条最小 DAG 可触发.
    - 验收: Airflow UI 可访问, DAG 可成功运行一次.

### Week 2 (01-10 to 01-16): P0 用你的语料跑通, 开始固化评测

- `RiskAgent-MultiAgent`
  - 目标: 固化 ingest 与 chunk 策略, 提升引用可追溯.
  - 验收: 20 个种子问题中, 80% 以上回答包含有效 citations.

- `RiskKnowGraph-GraphRAG`
  - 目标: 用你的最小语料跑通 end to end, 固化 5 个可复现问题.
  - 验收: 5 个问题能稳定复现输出, 且能定位 context 来源.

- P1 时间窗口(建议 20%-30%)
  - `RiskMonitor-MCP`
    - 目标: Phase 0 收尾继续推进.
    - 交付: main.py 模块化拆分最小落地, 基础测试补齐.
    - 验收: Phase 0 验收标准至少完成 1-2 项未完成项.
  - `RiskDataQuality-Airflow`
    - 目标: 自定义组件最小集合启动.
    - 交付: 至少 1 个 Hook 或 1 个 Operator 的最小实现.
    - 验收: 组件可在 DAG 中被引用, 并有 1 条单元测试.

### Week 3 (01-17 to 01-23): P0 质量与可控

- `RiskAgent-MultiAgent`
  - 目标: 加入最小评测脚本与 guardrails, 让系统可测可控.
  - 验收: 评测脚本可一键运行并输出报告.

- `RiskKnowGraph-GraphRAG`
  - 目标: 深入理解数据流与关键参数, 完成小幅调参并可解释收益.
  - 验收: 能口头复述完整数据流, 参数变化的质量与成本变化可解释.

- P1 时间窗口(建议 20%-30%)
  - `RiskMonitor-MCP`
    - 目标: Phase 1 稳定性与 DX.
    - 交付: 端到端 demo 脚本落地, README 启动方式固化.
    - 验收: MCP 客户端可调用至少 2 个工具并复现 demo.
  - `RiskDataQuality-Airflow`
    - 目标: 核心 DAG 推进.
    - 交付: `market_data_pipeline` 或 `basic_quality_check` 至少 1 条 DAG 可跑通.
    - 验收: UI 可触发 DAG, 任务成功.

### Week 4 (01-24 to 01-31): P0 文档与对外展示

- `RiskAgent-MultiAgent`
  - 目标: 收尾文档, demo 脚本, 让他人可快速复现.
  - 验收: 新人按 README 可复现 demo, 并能理解模块职责.

- `RiskKnowGraph-GraphRAG`
  - 目标: 文档固化与排障手册, 并输出 10 分钟讲解稿.
  - 验收: 新人按 README 可复现, 你能用 10 分钟讲清 GraphRAG 核心流程.

- P1 时间窗口(建议 20%-30%, 若 P0 滞后则按降级规则压缩)
  - `RiskMonitor-MCP`
    - 目标: Phase 2 最小业务演示的准备工作.
    - 交付: 选定 1 条主线用例并固化口径, 增加最小集成测试.
    - 验收: 至少 1 条计算链路可运行并有测试.
  - `RiskDataQuality-Airflow`
    - 目标: 收敛到 M2.
    - 交付: 两条核心 DAG 可运行, 文档与最小告警.
    - 验收: 新人按 README 10-15 分钟可跑通 demo.
