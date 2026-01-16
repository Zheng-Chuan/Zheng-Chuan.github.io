# ROADMAP

目标: 2026-01 从 0 自研一个 GraphRAG, 用公开金融衍生品风险语料跑通 index + query, 输出可解释的 graph 扩展过程与 citations. 近期优先事项: 引入 AI 实体关系抽取并将图存入图数据库, 接入云端开源 LLM 提供生成能力.

范围说明:

- 本项目定位为 "自研 MVP + 可解释".
- 现阶段引入 AI 实体关系抽取, 图数据存储迁移到图数据库(Neo4j 优先), 保持可解释与可复现.
- LLM 选用开源免费模型的云端推理服务, 避免本地大模型性能瓶颈.
- 优先保证可运行, 可复现, 可解释.

## GraphRAG 技术亮点(必须落地)

- graph 构建
  - 从 chunks 抽取 entities 与 relationships
  - 规则抽取升级为 AI 实体关系抽取(开源模型, 云端推理)
  - graph 写入图数据库(Neo4j) 并仍可导出 artifacts/graph.json 供回放
- graph-aware retrieval
  - seed retrieval -> seed entities -> k=1 graph expansion
  - 用扩展后的 entities 选择 context chunks
- explain
  - 输出 seed chunks, seed entities, expanded entities, expanded edges
  - 输出最终 context 由哪些 chunks 组成
- citations
  - citations 必须能回指到具体 chunk_id 与 source

## 目录约定

- corpus/: 公开语料 markdown, 后续再融合你的个人语料
- artifacts/: index pipeline 保存产物
- src/riskknowledgegraph_graphrag/: 核心实现

## 项目成熟度 Phase 0 to 3

- Phase 0: toy
  - [ ] 能跑通 demo, 但缺少工程约束
  - [ ] 无固定 schema, 无回归测试, 无 ablation
  - [ ] 不记录 model 版本 prompt 版本, 结果不可复现
- Phase 1: mvp
  - [x] index 与 query 两条命令稳定可跑
  - [x] artifacts contract v1 固化: chunks, triples, graph_export, last_query
  - [x] OpenRouter 作为唯一 LLM 调用入口, 支持 env 配置 key model temperature timeout retry
  - [x] pytest 覆盖 schema 字段存在性与小语料 smoke
- Phase 2: internal usable
  - [ ] Neo4j docker 一键启动, 图写入幂等, 多次 index 不膨胀
  - [ ] query 支持 ablation: use_graph vs no_graph, 输出差异指标
  - [ ] explain v2 全链路可回放: seed_chunk_scores, seed_chunk_entities, expansion_paths, context_selection_reasons
  - [ ] 成本控制与缓存策略, 失败重试与限流
- Phase 3: production grade
  - [ ] 全参数版本化与可审计: run_id, corpus_version, extractor_version, model_id, prompt_version
  - [ ] 可观测性: 日志, 耗时, token, 费用估算
  - [ ] CI: 单测, 回归集, 性能预算
  - [ ] 可扩展: 100+ 文档, 增量更新策略
  - [ ] 文档齐全: 架构图, 数据契约, 运行手册, 故障排查

## 模块划分

- 实体关系抽取与存储: LLM 实体关系抽取与存储
  - 输入: chunks
  - 输出: triples + provenance + confidence
  - 存储: Neo4j 为主, artifacts 导出为辅(用于回放与回归)
- Module B: Graph aware RAG 查询
  - seed retrieval -> seed entities -> k=1 graph expansion
  - 用 expanded entities 指导 context selection
  - 输出 answer explain citations, citations 可回指 chunk_id 与 source

## 2026-01 4 week checklist

### Week 1: 立项与骨架(Phase 0 -> Phase 1)

- [x] 定义两段式 pipeline: indexing 与 query
- [x] 固化 artifacts contract v1
  - [x] artifacts/chunks.jsonl: chunk_id doc_id source text
  - [x] artifacts/triples.jsonl: head relation tail confidence source_chunk_id
  - [x] artifacts/graph_export.json: nodes edges(用于回放)
  - [x] artifacts/last_query.json: answer explain citations
- [x] OpenRouter client 封装
  - [x] env 配置: OPENROUTER_API_KEY OPENROUTER_MODEL OPENROUTER_BASE_URL
  - [x] retry backoff timeout, 结构化 JSON 输出解析与校验
- [x] 工程骨架与文档
  - [x] src/riskknowledgegraph_graphrag/ 目录骨架
  - [x] tests/ 目录骨架
  - [x] docs/QUICKSTART.md: 最小运行方式
  - [x] docs/ARCHITECTURE.md: 新架构图
- [x] 验收
  - [x] pytest 可运行
  - [x] 小语料可产出 chunks.jsonl 与 last_query.json(空壳 schema)

### Week 2: 实体关系抽取与保存(Phase 1)

- [x] LLM triples 抽取
  - [x] chunk 级抽取, prompt 固化并记录 prompt_version
  - [x] 输出去重与归一化
  - [x] 解析失败可降级到规则抽取
- [x] artifacts 导出
  - [x] triples.jsonl 与 graph_export.json 可回放
- [x] 质量基线与测试
  - [x] ablation: 规则抽取 vs LLM 抽取
  - [x] pytest 覆盖: 抽取输出字段存在性与降级策略
- [x] 验收
  - [x] index pipeline 稳定输出 triples

### Week 3: Neo4j 落地 + Module B 图增强检索(Phase 2)

- [x] Neo4j docker
  - [x] docker compose 一键启动
  - [x] env 管理连接信息与 auth
- [x] Neo4j 写入与读取
  - [x] 幂等写入: MERGE node 与 edge
  - [x] 按 seed entities 做 k=1 expansion, 输出 expansion_paths
- [ ] Graph aware retrieval
  - [ ] seed retrieval(先用 TFIDF 或 BM25)
  - [ ] seed chunks -> seed entities
  - [x] Neo4j k=1 expansion
  - [ ] expanded entities 指导 context selection
- [ ] explain v2 与 citations
  - [ ] 输出 context_selection_reasons
  - [ ] citations 回指 chunk_id 与 source
- [ ] 验收
  - [ ] use_graph 与 no_graph 两条路径都可跑
  - [x] Neo4j 写入读回一致性测试通过

### Week 4: 稳定性与工程化(Phase 2 -> Phase 3)

- [ ] 可复现与版本化
  - [ ] index_manifest.json 记录参数与版本
  - [ ] last_query.json 记录 run_id 与依赖版本
- [ ] 回归集与评测
  - [ ] 固化内部问题集
  - [ ] 回归输出结构稳定性测试
  - [ ] ablation 差异报告可复现
- [ ] 成本与性能
  - [ ] token 统计与费用估算
  - [ ] chunk -> triples 缓存
- [ ] 文档交付
  - [ ] README 作为目录链接 docs
  - [ ] QUICKSTART 增加 OpenRouter 与 Neo4j 配置
- [ ] 验收
  - [ ] 一键跑通 index query pytest
  - [ ] 100+ 文档试运行可控且可追溯

## 未来可选做的

- 抽取质量评测闭环
  - [ ] 建立 gold triples 小数据集
  - [ ] 定义 precision recall 与错误类型统计
  - [ ] 每次改 model 或 prompt 自动跑评测并产出对比报告
- 抽取可追溯性增强
  - [ ] relation 增加 evidence_span 或 sentence
  - [ ] 记录 extractor_version prompt_version model_id temperature
  - [ ] run_id 级别图版本化 支持跨版本对比
- Neo4j 工程化
  - [ ] 幂等写入策略完善 MERGE 语义与唯一约束
  - [ ] 增量更新与删除策略 处理语料更新
  - [ ] Neo4j 读写一致性与性能基线测试
- Graph retrieval 策略升级
  - [ ] relation type 白名单与 traversal policy
  - [ ] k hop 扩展的预算控制 与路径去重
  - [ ] 支持多策略并行检索 并在 explain 中输出每条策略贡献
- 成本控制与稳定性
  - [ ] chunk_id 到 triples 的缓存与失效策略
  - [ ] OpenRouter 限流退避重试 与失败重放
  - [ ] token 统计与费用估算报表
- 安全与合规
  - [ ] prompt 注入防护 明确 chunk 仅为被抽取对象
  - [ ] query 阶段避免 text to cypher 优先参数化查询
  - [ ] 敏感信息与访问控制策略
