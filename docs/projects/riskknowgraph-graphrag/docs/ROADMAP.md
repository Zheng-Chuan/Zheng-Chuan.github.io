# ROADMAP

目标: 2026-01 从 0 自研一个 mini GraphRAG, 用公开金融衍生品风险语料跑通 index + query, 并输出可解释的 graph 扩展过程与 citations.

范围说明:

- 本项目定位为 "自研 MVP + 可解释".
- MVP 阶段不依赖 LLM, 实体关系抽取采用规则.
- 优先保证可运行, 可复现, 可解释.

## GraphRAG 技术亮点(必须落地)

- graph 构建
  - 从 chunks 抽取 entities 与 relationships
  - graph 落盘为 artifacts/graph.json
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
- artifacts/: index pipeline 落盘产物
- scripts/: 可复现命令入口
- src/graphrag_mini/: 核心实现
- legacy/: 历史开源内容备份, 不作为主线

## artifacts contract(MVP)

- artifacts/chunks.jsonl
  - {chunk_id, source, text, entities}
- artifacts/graph.json
  - {nodes, edges}
- artifacts/tfidf_index.joblib
  - TF-IDF 向量索引与向量化器

## 2026-01 1 个月计划(按周, checklist)

### Week 1: MVP baseline 跑通(index + query + artifacts)

- [x] corpus 准备
  - [x] 选定 3-5 篇公开金融衍生品风险语料并落盘到 corpus/
  - [x] 记录每篇语料来源链接
- [x] index pipeline
  - [x] chunk -> entities/relationships(rule-based)
  - [x] graph 落盘到 artifacts/graph.json
  - [x] TF-IDF 索引落盘到 artifacts/tfidf_index.joblib
- [x] query pipeline
  - [x] seed retrieval -> seed entities -> k=1 graph expansion
  - [x] answer + explain + citations 输出落盘
- [x] 验收
  - [x] 1 条命令跑通 index, artifacts 可落盘
  - [x] 1 条命令跑通 query, explain + citations 可落盘

### Week 2: contract 固化 + 5 个可复现问题 + smoke

- [x] artifacts schema 固化
  - [x] chunk_id 与 entity 口径固定
  - [x] explain 输出结构固定(含 use_graph 与 context_strategy)
- [x] 回归问题集
  - [x] 固化 5 个可复现问题(scripts/questions.json)
  - [x] 增加 1 条 smoke 命令(scripts/smoke_test.py, 同时覆盖 graph vs no-graph)
  - [x] 增加 1 条回归命令(scripts/run_questions.py, 产出 artifacts/regression/summary.json)
- [x] 工程可复现
  - [x] 新建 conda 环境 GraphRAG, 并用 conda run 统一运行口径
  - [x] query 支持 ablation: --no-graph(对比 use_graph=true)
- [x] 面试题清单
  - [x] docs/INTERVIEW.md: 50 题清单, 已完成部分题目的短答案(其余保留 TODO)
- [x] 验收
  - [x] 5 个问题输出稳定
  - [x] 每个问题都能定位到 context 与扩展路径

### Week 3: 可解释性增强 + 关键参数对比

- [ ] explain 输出增强
  - [ ] 展示 seed chunks 与 expanded graph 的选择理由
- [ ] 参数对比
  - [ ] k(1 vs 2), seed top_k, entity 过滤规则
  - [ ] 输出对比报告(表格或 markdown)
- [ ] 验收
  - [ ] 你能口头复述 index 与 query 的完整数据流
  - [ ] 你能解释 graph expansion 带来的收益与噪声代价

### Week 4: 文档与对外展示

- [ ] README
  - [ ] 一键运行 index/query/smoke
  - [ ] 目录与 artifacts 说明
- [ ] NOTES
  - [ ] GraphRAG vs RAG 的核心差异
  - [ ] 讲清 k-hop 与 explain 的示例
- [ ] TROUBLESHOOTING
  - [ ] 至少 5 个常见问题与解决办法
- [ ] 验收
  - [ ] 新人按 README 可复现
  - [ ] 你能用 10 分钟讲清 GraphRAG 的核心流程与亮点

## 风险与注意事项

- 不要为了更好效果过早引入 LLM, 先保证 graph-aware retrieval 和 explain 的可复现.
- 规则抽取会有噪声, Week 2 的重点是固定 contract, 而不是追求高质量.
- 数据集过大时, 先用小语料验证流程再扩展.
