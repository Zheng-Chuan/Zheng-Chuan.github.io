# QUICKSTART

## 目标

你可以在本地跑通三个命令

- index, 读取 corpus, 生成 chunks 和 triples, 保存 artifacts
- query, 输入问题, 走 seed retrieval, seed entities, k=1 graph expansion, 生成 last_query.json
- extract, 输入单个文档, 调用 LLM 抽 triples, 输出 jsonl

说明

- index 和 query 是主链路
- extract 用于单文档调试 方便做归一化和后续写入 Neo4j

## 安装

本项目默认使用 conda 环境 `GraphRAG`

```bash
conda activate GraphRAG
```

```bash
python -m pip install -r requirements.txt
```

## 配置 OpenRouter

你需要准备一个 `.env` 文件, 放在仓库根目录

- `.env` 只放在本地, 不要提交到 git
- 仓库里有 `.env.example`, 你可以复制一份

示例

```bash
cp .env.example .env
```

然后编辑 `.env`

```bash
OPENROUTER_API_KEY=your_key
OPENROUTER_MODEL=meta-llama/llama-3.1-8b-instruct:free
OPENROUTER_BASE_URL=https://openrouter.ai/api/v1
```

## 启动 Neo4j(dev)

```bash
docker compose -f deploy/dev/docker-compose.yml up -d
```

然后在 `.env` 里配置 Neo4j 连接信息

```bash
NEO4J_URI=bolt://localhost:7687
NEO4J_USERNAME=neo4j
NEO4J_PASSWORD=neo4j_password
NEO4J_DATABASE=neo4j
```

## 运行

### index

```bash
python -m riskknowledgegraph_graphrag index --corpus-dir corpus --artifacts-dir artifacts
```

### query

```bash
python -m riskknowledgegraph_graphrag query --question "what is cva" --artifacts-dir artifacts
```

### extract

```bash
python -m riskknowledgegraph_graphrag extract --input-path path/to/doc.md --output-path artifacts/single_doc_triples.jsonl
```

### no graph 版本

```bash
python -m riskknowledgegraph_graphrag query --question "what is cva" --artifacts-dir artifacts --no-graph
```
