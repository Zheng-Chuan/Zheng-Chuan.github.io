# Skill Tree (AI Agent Dev + LLM App Engineer)

本技能树用于 2026 年从传统后端开发转型到 AI Agent 开发与 LLM 应用算法工程师. 口径尽量严格.

分级定义:

- L1: 能用. 能把东西跑起来, 能解决常见报错.
- L2: 能解释能调优. 能做取舍, 能定位瓶颈, 能写出可复现实验.
- L3: 能设计能落地. 能把系统做成可上线版本, 有治理能力, 可持续迭代.

## A. 基础工程能力(底座)

- Python 工程化
  - L1
    - 虚拟环境与依赖管理(pip, uv, conda)
    - logging, config, CLI
    - 基本测试(pytest)
  - L2
    - typing, 数据结构与抽象边界
    - 可复现实验与环境锁定(requirements lock)
    - profiling(cProfile, py-spy), 性能定位
  - L3
    - 插件化架构, 清晰分层(api, service, core)
    - 大型项目可维护性(模块拆分, 依赖治理)
    - 统一错误分层与可观测性规范

- Linux, 网络, 调试
  - L1
    - 进程与端口, curl
    - 基础 shell, log 排查
  - L2
    - HTTP/TLS 基础, proxy, timeout
    - latency 分解(网络, 排队, 推理)
  - L3
    - 故障定位与复盘方法论
    - tail latency 成因与治理直觉

- Git 与协作
  - L1
    - 分支, rebase, PR
  - L2
    - code review, tag/release
  - L3
    - 多仓协作与版本治理

## B. LLM 应用基础(必须过关)

- Prompt engineering
  - L1
    - 模板化 prompt, few-shot
  - L2
    - structured output(JSON schema), 约束与校验
    - tool calling prompt, 错误恢复策略
  - L3
    - prompt failure taxonomy, prompt as program
    - prompt 版本管理与回归

- Provider 与模型选型
  - L1
    - OpenAI compatible API 调用
  - L2
    - 供应商对比(质量, 延迟, 成本, 限流)
    - 失败降级(fallback)策略
  - L3
    - 多模型路由(model router), 成本控制(cost budget)
    - 可观测的 provider adapter 层

## C. RAG 系统(核心竞争力)

- Ingestion 与数据治理
  - L1
    - 文档解析, chunking, metadata
  - L2
    - 增量更新, 去重, 版本化
    - 多格式(pdf, html)与解析失败兜底
  - L3
    - 数据质量指标(覆盖率, 新鲜度, 可追溯)
    - 可回滚的数据管线

- Retrieval
  - L1
    - 向量检索, top-k
  - L2
    - hybrid retrieval, rerank
    - query rewrite, 多路召回
  - L3
    - 召回-精排体系与路由策略
    - 查询分析与调参与回归

- Grounding 与引用
  - L1
    - citations
  - L2
    - faithfulness checks, 低置信拒答策略
  - L3
    - 可审计证据链, 防注入与来源可信度策略

- RAG evaluation
  - L1
    - 人工抽检与 checklist
  - L2
    - 离线评测脚本(groundedness, coverage)
  - L3
    - 在线反馈闭环(A/B, canary), 指标与报警

## D. Agent 系统(面试核心)

- Tooling(函数调用, MCP)
  - L1
    - function calling, schema 基础
    - MCP tools 基本开发
  - L2
    - 参数校验, 错误分层, retries
    - tool 合约设计(input/output schema)
  - L3
    - 权限模型(RBAC), 审计日志
    - sandbox 与 allowlist, 风险隔离

- Orchestration(单体到多智能体)
  - L1
    - 单 agent workflow
  - L2
    - planner/executor/reflector, 多 agent 协作
  - L3
    - 可控性设计(预算, 步数, 失败恢复)
    - 可测性设计(task success, tool accuracy)

- Memory 与 state
  - L1
    - conversation memory
  - L2
    - retrieval memory, task memory
  - L3
    - 记忆治理(过期, 合规, 可解释)

## E. GraphRAG 与知识图谱(加分项)

- 图谱基础
  - L1
    - entity, relation, schema
  - L2
    - graph query(Cypher), entity resolution 基础
  - L3
    - 图构建质量评估, schema 演进

- GraphRAG
  - L1
    - 跑通开源 repo, 能复现 demo
  - L2
    - 解释 pipeline, 调参, 固化回归问题集
  - L3
    - 与传统 RAG 融合策略, 指标体系与治理

## F. LLM serving 与性能优化(LLM 应用算法岗核心)

- Serving 框架
  - L1
    - vLLM/TGI 启动, OpenAI compatible endpoint
  - L2
    - continuous batching, kv cache, 参数调优
  - L3
    - 负载治理(backpressure), 多实例扩缩容策略

- Benchmark 方法论
  - L1
    - TTFT, tokens/s, req/s
  - L2
    - p50/p95/p99, 并发矩阵设计
  - L3
    - 实验可复现, 统计口径, 误差解释

- Optimization
  - L1
    - 量化尝试(INT4/INT8)
  - L2
    - 质量回归与收益解释
  - L3
    - 针对业务流量的定制优化与成本模型

## G. 生产化与 MLOps(最容易被低估的短板)

- Observability
  - L1
    - structured logs
  - L2
    - metrics + tracing, dashboards
  - L3
    - SLO, alerting, runbook, incident response

- Security
  - L1
    - secrets 管理, 不写明文 key
  - L2
    - prompt injection, data exfiltration 防护
  - L3
    - policy engine, 细粒度权限, 审计与合规

- Deployment
  - L1
    - Docker, 本地一键启动
  - L2
    - CI, basic CD
  - L3
    - canary, rollback, blue-green

## H. 训练工程(取决于你投递的岗位比例)

- Fine-tuning
  - L1
    - LoRA/QLoRA 跑通
  - L2
    - 数据构造与超参可解释
  - L3
    - 训练稳定性体系, 分布式训练基础(DDP/ZeRO)

- Training evaluation
  - L1
    - before/after demo
  - L2
    - 回归集与指标
  - L3
    - 数据污染(contamination)与泛化分析

## I. 严格缺口清单(完成当前 Timeline 后仍建议补齐)

- Production readiness
  - 鉴权(API key), RBAC
  - quota, rate limit, cost attribution
  - 日志脱敏, 合规与审计
  - SLO, 报警, runbook

- Evaluation hardening
  - RAG: retrieval 召回指标, groundedness, citations coverage
  - Agent: task success rate, tool accuracy, cost per task
  - Online: A/B, canary, feedback loop

- System design 口头表达
  - 负载模型, tail latency, capacity planning
  - 故障场景与降级策略

## J. 作品集映射(你当前计划能覆盖哪些节点)

- RiskAgent-MultiAgent
  - 覆盖: C(RAG), D(Agent), 部分 G(工程化)
- RiskMonitor-MCP
  - 覆盖: D(Tooling), G(可观测性与安全的基础)
- RiskDataQuality-Airflow
  - 覆盖: A(工程化), 数据管线思维, 部分 G(部署)
- RiskKnowGraph-GraphRAG
  - 覆盖: E(GraphRAG), 补强 C 的检索视角
- RiskLLM-InferenceOptimization
  - 覆盖: F(Serving, benchmark, optimization), 部分 G(可观测性)
- RiskLLM-FineTunning
  - 覆盖: H(训练工程)的 L1-L2, 取决于你收敛范围
