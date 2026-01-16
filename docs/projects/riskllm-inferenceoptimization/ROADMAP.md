# ROADMAP

目标: 2026-02 启动 `RiskLLM-InferenceOptimization` 项目, 用工程化方式完成一个可上线的 LLM 推理服务, 并通过系统化优化在吞吐, 延迟, 显存占用与成本上给出可量化改进与可复现实验报告.

定位: 算法开发工程师(非研究员)视角, 重点是 serving, 性能调优, 评测回归, 可靠性与可观测性.

## 项目边界

做:

- OpenAI compatible 推理服务, 至少支持 `/v1/chat/completions`
- 可复现 benchmark 与 report
- 性能优化与工程可靠性

不做:

- 提出新模型结构或训练新基座
- 大规模分布式集群调度(先做单机, 可扩展)

## 交付物清单(二月月底验收)

- 推理服务
  - OpenAI compatible API
  - 支持并发请求, 队列与限流
  - 支持超时, 熔断, 降级策略
- Benchmark
  - 合成压测数据生成器
  - 压测脚本与一键复现实验命令
  - 输出指标: TTFT, tokens/s, req/s, p50/p95/p99, GPU memory
- 性能报告
  - baseline vs optimized 对比
  - 不同并发, 不同 prompt 长度, 不同输出长度的曲线或表格
  - 成本估算: cost per 1k tokens(可用机器成本估算)
- 可观测性
  - `/metrics`(Prometheus) 指标
  - 结构化日志与 request id
- 文档
  - README: quickstart 与运行方式
  - ARCHITECTURE: 架构与组件职责
  - RUNBOOK: 常见故障与排障路径

## 指标定义(建议口径)

- TTFT: time to first token
- Latency: end to end latency, p50, p95, p99
- Throughput
  - tokens per second
  - requests per second
- Resource
  - GPU memory used
  - KV cache utilization(如果框架支持)
- Cost
  - cost per 1k tokens(用机器小时成本与 throughput 估算)

## 2026-01 理论自查框架

目标: 在 2026-02 开工前, 把推理服务与性能优化相关的关键概念补齐, 并能用自己的语言复述.

自查方式: 每一项都要能写出 5-10 行笔记, 并能回答 3 个 why.

- [ ] Transformer 推理路径: prefill vs decode, 影响 TTFT 与 tokens/s 的主要因素
- [ ] KV cache: 作用, 显存占用模型, 与 batch size 和 seq len 的关系
- [ ] Batching: continuous batching 的基本思想, 何时提升吞吐, 何时恶化 p99
- [ ] Sampling 参数: temperature, top_p, top_k, repetition penalty 对速度与质量的影响
- [ ] 并发模型: async, queue, backpressure, 超时与取消的语义
- [ ] 性能指标口径: TTFT, e2e latency, p50/p95/p99, tokens/s, req/s, GPU memory
- [ ] Benchmark 设计: prompt len x output len x concurrency 的实验矩阵, 如何避免误导
- [ ] 量化基础: 8bit/4bit, weight-only vs activation-aware, 质量回归的最小做法
- [ ] 服务接口: OpenAI compatible chat completions 的关键字段, streaming 的实现要点
- [ ] 可观测性: metrics vs logs vs traces, 以及如何用指标定位瓶颈
- [ ] 可靠性: rate limit, circuit breaker, fallback, overload protection 的基本策略

## 2026-02 时间计划(5 天一个周期)

### Cycle 1 (Day 1-5): baseline 跑通 + 指标口径统一

- 目标
  - 选定模型与推理框架, 先把服务跑通, 指标可测.
- 任务
  - 选择一个可本机运行的指令模型(优先 7B 或更小)
  - 选择 serving 框架(vLLM 优先, 备选 TGI)
  - 启动 OpenAI compatible server
  - 写最小压测脚本: 并发, prompt len, output len 可配置
- 验收
  - curl 调用可用, 并发压测可跑
  - baseline 指标输出到报告中

### Cycle 2 (Day 6-10): batching 与参数调优

- 目标
  - 把吞吐拉上去, 让 p99 延迟可控.
- 任务
  - 调参对比: `max_num_seqs`, `max_num_batched_tokens`, `gpu_memory_utilization`
  - 设计实验矩阵: 并发 x prompt len x output len
  - 输出图表: throughput 与 p99 随并发变化
- 验收
  - 给出 1 份对比 report: baseline vs tuned

### Cycle 3 (Day 11-15): 量化与显存优化

- 目标
  - 在可接受的质量退化下, 降低显存占用并提升并发能力.
- 任务
  - 引入量化版本(例如 4bit), 记录加载方式与参数
  - 对比显存占用, throughput, p99
  - 做轻量质量回归(小样本或自动 judge)
- 验收
  - 量化收益有量化数据, 并且质量回归可解释

### Cycle 4 (Day 16-20): 可靠性与可观测性

- 目标
  - 从 demo 走向服务化: 可观测, 可控, 可恢复.
- 任务
  - 增加队列与 backpressure 策略
  - 增加 timeout, retry policy(客户端侧或网关侧)
  - 增加 `/metrics`: qps, error rate, latency buckets, tokens generated
  - 增加结构化日志, 记录 request id, prompt/output tokens
- 验收
  - 过载时行为可控, 指标与日志能定位问题

### Cycle 5 (Day 21-25): prompt cache 或 speculative decoding(可选二选一)

- 目标
  - 做一个能明显提升真实场景体验的优化点.
- 任务(二选一)
  - 方案 A: prefix cache 或 prompt cache, 重点优化长 prompt 的 TTFT
  - 方案 B: speculative decoding, 重点优化生成速度
- 验收
  - 对选定场景给出可复现的收益曲线

### Cycle 6 (Day 26-28): 收尾, 报告固化, 简历 bullet 输出

- 目标
  - 让项目可复现, 可展示, 可写入简历.
- 任务
  - 整理 README, ARCHITECTURE, RUNBOOK
  - 固化 benchmark 命令与结果落盘
  - 产出一页性能报告摘要
  - 写 3-5 条简历 bullet, 用事实数据填充
- 验收
  - 新人按 README 可在 10-15 分钟内跑通服务与一次基准测试

## 风险与注意事项

- 模型与框架会受本机 GPU 型号与显存限制影响, 需要先做 baseline 确定可行配置.
- 量化可能带来质量下降, 必须有最小回归集.
- 任何性能数据都必须带上测试配置, 包括并发, prompt, 输出长度, 机器规格.
