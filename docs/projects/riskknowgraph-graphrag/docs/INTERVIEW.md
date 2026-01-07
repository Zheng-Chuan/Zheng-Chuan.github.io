# INTERVIEW

目标: 收集 50 个偏硬核的 GraphRAG 面试题, 并在开发过程中逐题回答.

使用方式:

- 每个问题用 checklist 管理
- 你在实现某个模块时, 顺手补对应题目的答案

## 基础概念

- [x] Q1. 什么是 GraphRAG, 它和传统 RAG 有什么不同?
  - 答案: GraphRAG 可以理解为图感知的检索增强生成. 传统 RAG 通常只做向量相似度检索 chunks, 然后生成. GraphRAG 增加了显式的实体关系图, query 时走 seed retrieval -> seed entities -> graph expansion(k-hop) -> context selection. 核心价值是 explainability 和可控的扩展, 不只是答案更好.
- [ ] Q2. 在 GraphRAG 系统里, 这个 graph 具体表示什么?
  - 答案: TODO
- [x] Q3. 什么是 k-hop 扩展, 为什么 k 变大往往噪声也会变大?
  - 答案: k-hop 的意思是从 seed entities 出发, 沿着 graph edges 走 k 步, 收集可达节点. k 变大通常能提升 recall, 但也更容易引入弱相关实体, 导致 context explosion 和更弱的 groundedness. MVP 默认 k=1 可以保持扩展可解释, 并控制噪声.
- [x] Q4. seed retrieval 在 GraphRAG 里扮演什么角色?
  - 答案: seed retrieval 用来找到最初锚定问题的 seed chunks. 我们从 seed chunks 抽取 seed entities, 后续 graph expansion 都依赖这些 seed. 如果 seed retrieval 错了, expansion 会 drift, 并放大噪声.
- [ ] Q5. 相比 RAG, GraphRAG 常见的失败模式有哪些?
  - 答案: TODO
- [ ] Q6. 你如何定义并度量 GraphRAG 的 groundedness?
  - 答案: TODO
- [x] Q7. 为什么 GraphRAG 需要 explain contract, 而不仅是 answer?
  - 答案: GraphRAG 的价值来自 retrieval path, 而不是生成文本本身. 稳定的 explain contract 可以用于调试和做 ablation. 在这个 repo 里, explain 包含 seed_chunk_ids, seed_entities, expanded_entities, expanded_edges, context_chunk_ids, 以及 use_graph 和 context_strategy.
- [ ] Q8. 什么时候基于图的方法会变成负担而不是优势?
  - 答案: TODO

## 索引与建图

- [ ] Q9. 你如何从文本中抽取 entities 和 relationships? 规则抽取和 LLM 抽取各自的 tradeoff 是什么?
  - 答案: TODO
- [ ] Q10. 什么是 entity normalization? 为什么它会显著影响 graph 质量?
  - 答案: TODO
- [ ] Q11. 你如何处理 entity alias, 缩写, 以及 disambiguation?
  - 答案: TODO
- [ ] Q12. 你如何把 provenance 从 text chunks 关联到 graph nodes 和 edges?
  - 答案: TODO
- [ ] Q13. GraphRAG MVP 的最小 graph schema 应该长什么样?
  - 答案: TODO
- [ ] Q14. 你如何在建图阶段控制 graph density 和 noise?
  - 答案: TODO
- [ ] Q15. chunk size 和 chunk overlap 会如何影响 entity 抽取和图构建?
  - 答案: TODO
- [ ] Q16. 你如何检测并去除重复或近重复的 nodes 和 edges?
  - 答案: TODO
- [ ] Q17. 当 corpus 变化时, 你如何做 graph 的增量更新?
  - 答案: TODO
- [x] Q18. 为了保证 index 和 query 可复现, 必须落盘哪些 artifacts?
  - 答案: 必须落盘 chunking 结果和检索结构. 在这个 repo 的最小集合是: artifacts/chunks.jsonl(chunk_id, source, text, entities), artifacts/graph.json(nodes, edges), artifacts/tfidf_index.joblib(vectorizer, matrix, chunk_ids), 以及 query 输出 artifacts/last_query.json(question, answer, explain, citations). 没有这些, explain 和 citations 就无法回放.

## 检索与图感知检索

- [x] Q19. 实践中如何把向量检索和图扩展结合起来?
  - 答案: 向量检索只用来拿 seed chunks. 然后从 seed chunks 抽取 seed entities, 在图上做扩展(默认 k=1), 再用扩展后的 entities 选择 context chunks. 这个 repo 也提供了 ablation: use_graph=true vs no-graph baseline(只用 seed chunks).
- [ ] Q20. 为什么 seed retrieval 质量是 GraphRAG 的瓶颈?
  - 答案: TODO
- [ ] Q21. 从 seed chunks 里选 seed entities 有哪些方法?
  - 答案: TODO
- [ ] Q22. 对比 k-hop 扩展和 personalized PageRank 风格的扩展.
  - 答案: TODO
- [ ] Q23. 你如何判断 expanded nodes 和 edges 哪些与问题相关?
  - 答案: TODO
- [ ] Q24. 什么是 context explosion? 你如何在不破坏 explain 可复现性的前提下限制 context size?
  - 答案: TODO
- [ ] Q25. 如果 graph expansion 没有带来任何新实体, 你怎么处理?
  - 答案: TODO
- [ ] Q26. 什么是 reranking? 在 GraphRAG pipeline 里应该放在哪一层?
  - 答案: TODO
- [ ] Q27. 除了 recall 和 precision, 你如何评估 GraphRAG 的 retrieval quality?
  - 答案: TODO

## Explain 与引用

- [x] Q28. 一个可用于调试的 explain 输出, 至少必须包含哪些字段?
  - 答案: explain 必须可回放并且结构稳定. 最小字段: use_graph, context_strategy, seed_top_k, k_hop, seed_chunk_ids, seed_entities, expanded_entities, expanded_edges, context_chunk_ids. 有了这些你才能解释一个 chunk 为什么被选中, 以及扩展是否发生 drift.
- [x] Q29. 为什么 citations 必须来自 retriever artifacts, 而不能依赖 LLM 自己输出?
  - 答案: LLM 可能 hallucinate citations. citations 必须根据 retrieval 实际选中的 context chunks 计算出来. 在这个 repo 里, citations 来自 artifacts/chunks.jsonl 的 chunk_id 和 source, 所以可验证且可复现.
- [ ] Q30. 对于 multi-hop graph expansion 的答案, 你如何定义 valid citation?
  - 答案: TODO
- [ ] Q31. 你如何计算 citation coverage? MVP 阈值设多少更合理?
  - 答案: TODO
- [ ] Q32. explain 相关的常见 bug 有哪些? 你如何检测它们?
  - 答案: TODO
- [x] Q33. 你如何用 explain 和 citations 构建最小回归套件?
  - 答案: 固定 question set 并锁定输出 schema. index 跑一次, 然后每个问题分别跑 graph 和 no-graph, 落盘输出, 并断言 schema 和基本 invariants(例如 citations 非空, seed_entities 非空). 这个 repo 提供了 scripts/run_questions.py 和加固后的 scripts/smoke_test.py.

## 评测

- [ ] Q34. 设计一个基于固定 seed question set 的 GraphRAG 评测方案.
  - 答案: TODO
- [ ] Q35. 你如何区分收益是来自 graph expansion, 还是来自更好的 LLM prompting?
  - 答案: TODO
- [ ] Q36. index 阶段和 query 阶段你会记录哪些 metrics?
  - 答案: TODO
- [ ] Q37. 使用 GraphRAG 时你如何检测 hallucination?
  - 答案: TODO
- [x] Q38. 你如何做 GraphRAG 的 ablation studies?
  - 答案: 固定 corpus, chunking, seed retrieval, 然后只切换 graph 相关组件. 典型 ablations: use_graph=true vs no-graph, k=1 vs k=2, 不同的 seed_top_k, entity filters. 对比 explain metrics(例如 expanded_edge_count, context_chunk_count)以及固定问题集上的下游评测结果.
- [ ] Q39. 你如何为金融衍生品风险领域构建 test set?
  - 答案: TODO

## 工程与运维

- [ ] Q40. 为了保证可复现, GraphRAG artifacts 的关键 invariants 是什么?
  - 答案: TODO
- [ ] Q41. 你如何对 artifacts 和 datasets 做版本管理?
  - 答案: TODO
- [ ] Q42. 你如何处理 query workload 的并发与缓存?
  - 答案: TODO
- [ ] Q43. index pipeline 失败时你怎么处理? 哪些该 retry, 哪些不该 retry?
  - 答案: TODO
- [ ] Q44. GraphRAG MVP 的最小可观测性栈应该包含什么?
  - 答案: TODO
- [ ] Q45. 使用内部风险数据时, 最小安全基线应该是什么?
  - 答案: TODO

## 扩展与性能

- [ ] Q46. 当 graph 变得很大时会发生什么? 首要瓶颈通常在哪里?
  - 答案: TODO
- [ ] Q47. 你如何扩展 graph traversal 和 graph storage?
  - 答案: TODO
- [ ] Q48. 你如何降低 graph expansion 和 context selection 的延迟?
  - 答案: TODO

## LLM 集成

- [ ] Q49. 如果你把规则抽取替换为 LLM 抽取, 为了保持可复现必须锁定哪些 contract?
  - 答案: TODO
- [ ] Q50. 你如何把 deterministic answer generation 迁移到 OpenAI compatible LLM, 同时保持 citations 正确?
  - 答案: TODO
