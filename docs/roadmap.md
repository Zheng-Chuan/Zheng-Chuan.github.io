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

- [x] `RiskAgent-MultiAgent`
  - [x] 目标: 跑通最小 RAG + 多智能体编排闭环, 形成可演示 demo.
  - [x] 验收: UI 或 CLI 能 ingest -> retrieve -> answer, 并返回 citations.
  - 依据: `RiskAgent-MultiAgent/docs/ROADMAP.md`.

- [x] `RiskKnowGraph-GraphRAG`
  - [x] 目标: 从 0 自研一个 mini GraphRAG MVP, 先用公开金融衍生品风险语料跑通 index + query.
  - [x] 交付:
    - [x] 公开语料落盘到 corpus/, 并记录来源
    - [x] 1 条命令跑通 index, artifacts 落盘
      - [x] graph.json, chunks.jsonl, tfidf_index.joblib(或等价文件)
    - [x] 1 条命令跑通 query
      - [x] seed retrieval -> seed entities -> k=1 graph expansion -> context -> answer
      - [x] 输出 explain + citations, 并落盘
  - [x] 验收:
    - [x] 新人按 README 10-15 分钟可跑通 index + query
    - [x] answer 中的 citations 能回指到具体 chunk
    - [x] explain 能说明 seed entities 与扩展到的节点/边
  - 依据: `RiskKnowGraph-GraphRAG/docs/ROADMAP.md`.

- P1 时间窗口(建议 20%-30%)
  - [x] `RiskMonitor-MCP`
    - [x] 目标: Phase 0 Week 1 监控链路 MVP 跑通, 并完成验收.
    - [x] 验收: tests 通过, scenario 与 smoke 可复现.
  - [ ] `RiskDataQuality-Airflow`
    - [ ] 目标: 环境可启动, DAG 可加载.
    - [ ] 验收: Airflow UI 可访问, DAG 可成功运行一次.

### Week 2 (01-10 to 01-16): P0 用你的语料跑通, 开始固化评测

- [ ] `RiskAgent-MultiAgent`
  - [ ] 目标: 固化 ingest 与 chunk 策略, 提升引用可追溯.
  - [ ] 验收: 20 个种子问题中, 80% 以上回答包含有效 citations.

- [x] `RiskKnowGraph-GraphRAG`
  - [x] 目标: 固化 GraphRAG 的 artifacts contract 与回归口径, 做出可复现问题集.
  - [x] 交付:
    - [x] 固化 artifacts schema
      - [x] chunk_id, source, entity list, edge list, explain 结构
    - [x] 5 个可复现问题 + 1 条 smoke 命令
    - [x] 调整 k 与过滤策略的默认值(先固定 k=1)
  - [x] 验收:
    - [x] 5 个问题能稳定复现输出
    - [x] 每个问题都能定位到对应的 context 与扩展路径
  - 依据: `RiskKnowGraph-GraphRAG/docs/ROADMAP.md`.

- P1 时间窗口(建议 20%-30%)
  - [ ] `RiskMonitor-MCP`
    - [ ] 目标: Phase 0 Week 2 工程化强化与性能.
    - [ ] 交付: main.py 模块化拆分最小落地, 基础测试补齐.
    - [ ] 验收: Phase 0 验收标准至少完成 1-2 项未完成项.
  - [ ] `RiskDataQuality-Airflow`
    - [ ] 目标: 自定义组件最小集合启动.
    - [ ] 交付: 至少 1 个 Hook 或 1 个 Operator 的最小实现.
    - [ ] 验收: 组件可在 DAG 中被引用, 并有 1 条单元测试.

### Week 3 (01-17 to 01-23): P0 质量与可控

- `RiskAgent-MultiAgent`
  - 目标: 加入最小评测脚本与 guardrails, 让系统可测可控.
  - 验收: 评测脚本可一键运行并输出报告.

- `RiskKnowGraph-GraphRAG`
  - 目标: 强化 GraphRAG 的可解释性与可控性, 让 graph 部分成为明确增益来源.
  - 交付:
    - explain 输出增强
      - 展示 seed chunks, seed entities, expanded entities, expanded edges
      - 展示最终 context 由哪些 chunks 组成
    - 做 2-3 个关键参数对比
      - k(1 vs 2), seed top_k, entity 过滤规则
    - 输出对比报告(表格或 markdown)
  - 验收:
    - 你能口头复述 index 与 query 的完整数据流
    - 你能解释 graph expansion 带来的质量变化与噪声代价

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
  - 目标: 文档固化与对外展示, 让别人也能跑通并理解 GraphRAG 的技术亮点.
  - 交付:
    - README: 一键运行 index/query/smoke, 目录与 artifacts 说明
    - NOTES: GraphRAG vs RAG 的差异, graph expansion 的 explain 示例
    - TROUBLESHOOTING: 至少 5 个常见问题与解决办法
    - 10 分钟讲解稿: 用 1 个问题演示 seed -> expand -> citations
  - 验收:
    - 新人按 README 可复现
    - 你能用 10 分钟讲清 GraphRAG 的核心流程与亮点

- P1 时间窗口(建议 20%-30%, 若 P0 滞后则按降级规则压缩)
  - `RiskMonitor-MCP`
    - 目标: Phase 2 最小业务演示的准备工作.
    - 交付: 选定 1 条主线用例并固化口径, 增加最小集成测试.
    - 验收: 至少 1 条计算链路可运行并有测试.
  - `RiskDataQuality-Airflow`
    - 目标: 收敛到 M2.
    - 交付: 两条核心 DAG 可运行, 文档与最小告警.
    - 验收: 新人按 README 10-15 分钟可跑通 demo.

## 2026-02 计划(按周)

目标: 2026-02 完成 2 个 LLM 方向作品.

- P0: `RiskLLM-FineTunning`(主线, 质量与领域能力)
- P1: `RiskLLM-InferenceOptimization`(次线, 推理服务与性能)

时间分配建议:

- P0 主线(FineTunning): 70%-80%
- P1 次线(InferenceOptimization): 20%-30%

统一验收口径(二月底):

- FineTunning: 有固定 test set 与评测脚本, baseline vs finetuned 的对比结果可复现并落盘.
- InferenceOptimization: 有 OpenAI compatible API + 可复现压测脚本 + 至少 1-2 个优化点的对比报告.

Roadmap links:

- `RiskLLM-FineTunning/docs/ROADMAP.md`
- `RiskLLM-InferenceOptimization/ROADMAP.md`

### Week 1 (02-01 to 02-07): 任务定义 + 数据闭环 + baseline

- `RiskLLM-FineTunning`
  - 目标: 明确金融衍生品风险评估与报告的最小 MVP, 固定输入输出口径.
  - 交付: 数据 schema 与报告模板 v0, 训练/验证/测试切分, baseline 评测落盘.
  - 验收: 1 条命令可跑 baseline eval, 输出 artifacts(例如 jsonl + summary).

- `RiskLLM-InferenceOptimization`
  - 目标: 云 GPU 上跑通推理服务与最小压测.
  - 交付: OpenAI compatible server 跑通, 基线指标(TTFT, tokens/s, p95/p99)落盘.
  - 验收: curl 调用可用, 压测脚本可跑并输出报告.

### Week 2 (02-08 to 02-14): QLoRA 跑通 + 第一次对比

- `RiskLLM-FineTunning`
  - 目标: QLoRA 训练闭环跑通, 产出第一个可对比版本.
  - 交付: 可复现训练脚本, adapter 或合并权重, baseline vs finetuned 对比表.
  - 验收: 在固定 test set 上, 至少 1 个核心指标稳定优于 baseline.

- `RiskLLM-InferenceOptimization`
  - 目标: 做 1 次参数调优并给出对比.
  - 交付: batching 与并发相关参数的对比实验, 曲线或表格.
  - 验收: 有 tuned vs baseline 的可复现对比数据.

### Week 3 (02-15 to 02-21): 数据迭代 + 评测固化 + 可靠性

- `RiskLLM-FineTunning`
  - 目标: 迭代数据与训练策略, 固化评测口径.
  - 交付: failure cases 归因与补数据, eval 脚本与数据版本锁定.
  - 验收: 你能用固定样例说明提升点与退化点, 并解释原因.

- `RiskLLM-InferenceOptimization`
  - 目标: 可观测性与过载保护.
  - 交付: `/metrics`, 结构化日志, timeout 与 backpressure 策略.
  - 验收: 过载时行为可控, 指标与日志能定位问题.

### Week 4 (02-22 to 02-28): 收尾展示 + 文档 + 简历 bullet

- `RiskLLM-FineTunning`
  - 目标: 可展示, 可复现, 可写简历.
  - 交付: README(Quickstart, Train, Eval, Demo), 模型发布方式, 代表性案例.
  - 验收: 新人按 README 10-15 分钟可跑通 demo + eval.

- `RiskLLM-InferenceOptimization`
  - 目标: 选择 1 个额外优化点并固化报告.
  - 交付: 量化 or prompt cache or speculative decoding(三选一), 对比报告与配置记录.
  - 验收: 选定场景的收益可复现, 并输出 3-5 条简历 bullet.
