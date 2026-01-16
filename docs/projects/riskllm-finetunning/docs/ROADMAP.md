# ROADMAP

目标: 2026-02 完成一个面向金融衍生品风险评估与报告的 LLM 微调作品, 以 risk manager 视角输出结构化风险结论与可追溯依据, 并具备可复现训练与评测闭环.

定位: LLM application engineer 视角, 重点是数据与评测口径, 训练可复现, 版本管理, 与可展示 demo.

## 项目边界

做:

- 选定 1 个开源指令模型作为 base, 以 QLoRA/LoRA 完成领域适配
- 固定 test set + 可复现 eval, 输出 baseline vs finetuned 对比
- 输出 risk report(JSON or Markdown)与最小 demo(脚本或 API)

不做:

- 端到端替代真实 risk manager 的全部职责(先做 1 条最小用例闭环)
- 大规模在线学习与实时自更新流水线(后续扩展)

## 默认执行方案(易用性优先)

云 GPU 平台(默认): AutoDL.

- 原因: 上手快, 人民币支付, 社区成熟, 适合 1 个月冲刺.
- 成本控制: 训练时开机, 不训练就关机, 避免 GPU 空转.

GPU 规格(默认): 1x A10 24GB.

- 适配: 7B 级模型 QLoRA 通常可行.
- 降级: 若成本或资源紧张, 切到 1x L4 24GB 或 1x A4000/A5000 级别, 或把 base 模型降到 3B.

## 基座模型选择策略

默认起跑模型: `Qwen2.5-7B-Instruct`.

- 理由: 中文能力强, 生态完善, 微调资料多, 风险更可控.

Week 1 允许做一次 bakeoff(可选): 选 1-2 个 challenger 跑 baseline.

- 候选: DeepSeek 同尺寸 instruct/chat, GLM 同尺寸 instruct/chat.
- 规则: 只跑 baseline eval, 不提前投入训练, 以你的 test set 结果决定是否替换基座.

## 二月底交付物清单(验收)

- 数据
  - 数据 schema 与字段口径文档
  - train/val/test 切分固定, test set 不随迭代变化
- 训练
  - 1 条命令可复现训练, 依赖与随机种子锁定
  - 产物: LoRA adapter 或 merge 后权重
- 评测
  - 1 条命令可复现 eval
  - baseline vs finetuned 对比结果可落盘
- Demo
  - 输入: 最小衍生品风险数据样例
  - 输出: 结构化 risk report + 关键依据说明
- 文档
  - README: Quickstart, Train, Eval, Demo
  - RUNBOOK: 常见故障与排障

## 2026-01 理论自查框架

目标: 在 2026-02 开工前, 把 LLM 微调与评测相关的关键概念补齐, 并能用自己的语言复述.

自查方式: 每一项都要能写出 5-10 行笔记, 并能回答 3 个 why.

- [ ] 任务定义: risk assessment vs summarization vs classification, 以及对应的输出 schema
- [ ] SFT 基础: instruction tuning 的目标, 训练数据格式(JSONL, chat template)
- [ ] LoRA: rank, alpha, target modules, 为何能在低成本下适配
- [ ] QLoRA: 4bit 量化训练的关键点, NF4, double quant, 可能的数值风险
- [ ] 数据质量: 噪声标注的典型模式, 如何通过失败样例迭代数据
- [ ] 评测口径: 固定 test set, metrics(accuracy, rubric score), 与可复现性要求
- [ ] LLM-as-judge: 何时可用, 如何降低偏置, 如何固定 prompt 与版本
- [ ] 幻觉与可解释: 什么样的报告输出算可追溯, 如何设计 evidence 字段
- [ ] 安全与合规: 敏感信息处理, 无明文 secrets, license 基础检查
- [ ] 模型发布: adapter vs merged weights, 版本号与变更记录

## 2026-02 计划 按周 checklist

说明 这一段是你每天可以打勾的执行清单 同时覆盖业务交付与技术展示

### Week 1 任务定义 数据闭环 baseline

- [ ] 定义最小用例 v0
  - [ ] 明确输入字段 衍生品头寸 市场数据 假设与限制
  - [ ] 明确输出 schema v0 risk summary key drivers assumptions limitations evidence
  - [ ] 定义 5 到 10 条人工验收样例
- [ ] 数据集 v0 固化
  - [ ] 原始数据盘点 数据来源与口径
  - [ ] 清洗规则 去噪 去重 脱敏
  - [ ] 转换格式 Alpaca 或 ShareGPT
  - [ ] 切分 train val test
  - [ ] 固化 test set 并写入版本号
- [ ] 模型结构分析 Showcase
  - [ ] 运行 scripts/analyze_model.py
  - [ ] 记录 config 关键字段 hidden size layers heads context window
  - [ ] 写 docs/architecture_analysis.md
- [ ] baseline 推理与评测 v0
  - [ ] 选择 base 模型 并记录版本
  - [ ] 固定 prompt 模板
  - [ ] baseline 输出落盘 jsonl
  - [ ] baseline 指标落盘 评分口径先用 rubric v0
- [ ] 验收
  - [ ] 一条命令可跑 baseline eval
  - [ ] artifacts 可复现 可落盘

### Week 2 QLoRA 跑通 第一次对比

- [ ] 训练配置准备
  - [ ] 选择 QLoRA 配置 nf4 double quant compute dtype
  - [ ] 选择 LoRA target modules 并写下理由
  - [ ] 打印 trainable params 占比
- [ ] 训练闭环跑通
  - [ ] debug run 小数据 小 step
  - [ ] 正式训练 固化随机种子
  - [ ] WandB 记录 run config 与 metrics
- [ ] 推理与对比
  - [ ] finetuned 推理脚本
  - [ ] baseline vs finetuned 对比表
  - [ ] 收集 10 条代表性样例 做 case notes
- [ ] 验收
  - [ ] 固定 test set 上 至少 1 个核心指标稳定优于 baseline
  - [ ] 训练脚本与参数可一条命令复现

### Week 3 数据迭代 评测固化

- [ ] failure cases 分析
  - [ ] 收集模型失败样例 幻觉 漏项 格式不稳
  - [ ] 归因 数据问题 prompt 问题 训练问题
- [ ] 数据集 v1
  - [ ] 基于失败样例补齐数据
  - [ ] 记录变更 diff 与原因
  - [ ] 保持 test set 不变
- [ ] 评测固化
  - [ ] 固定评分 rubric v1
  - [ ] eval 脚本输出对比结果到 artifacts
  - [ ] 可选 引入 LLM as judge 并固定 judge 模型与 prompt
- [ ] 消融实验
  - [ ] LoRA rank 消融 例如 r 8 vs 16 vs 32
  - [ ] 学习率消融 例如 1e-4 vs 2e-4
- [ ] 验收
  - [ ] 用 5 到 10 个样例说明提升与退化 并解释原因

### Week 4 收尾展示 文档 部署

- [ ] 发布策略
  - [ ] 决定产物 adapter 或 merged weights
  - [ ] 版本号与变更记录
- [ ] 量化
  - [ ] 选择 GGUF 或 AWQ 或 GPTQ
  - [ ] 对比 FP16 vs INT4 文件大小 显存 推理速度
- [ ] Demo
  - [ ] 选择形态 CLI 或 API
  - [ ] demo 输入样例与输出样例
- [ ] 部署
  - [ ] 优先 AutoDL 跑通线上推理
  - [ ] 可选 AWS EC2 作为工程化加分
  - [ ] 可选 Docker 化
- [ ] 文档
  - [ ] README quickstart train eval demo
  - [ ] RUNBOOK 常见故障与排障
  - [ ] 代表性案例 baseline vs finetuned
  - [ ] 简历 bullet 3 到 5 条 数据规模 指标提升 成本 时长
- [ ] 验收
  - [ ] 新人按 README 10 到 15 分钟可跑通 demo 与 eval

### 工程化补充

- [ ] requirements.txt 已补充 accelerate trl wandb einops

### 开放问题

- [ ] demo 形态 选择 API 服务 或简单 Web 页面
- [ ] 云部署平台 选择 AutoDL 或 AWS EC2
