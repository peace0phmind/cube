# AGENTS.md

本文件为在本仓库中工作的 agent 提供补充约束。通用构建、测试和仓库结构说明仍以 `CLAUDE.md` 为准。

## 指标运行时原型

在处理 Rust-only 指标运行时、NL2Metric、OpenUI 建模、多数据库 adapter 相关任务前，先阅读：

- `CONTEXT.md`
- `docs/adr/0001-rust-only-metric-runtime-prototype.md`

必须沿用 `CONTEXT.md` 中的领域语言。特别是：

- 使用“语义目录”表示持久化的业务语义事实来源。
- 使用“指标”“维度”“指标查询”“NL2Metric”，不要把这些概念混成 SQL、prompt 或 YAML schema。
- 语言模型只负责理解意图、建议建模和澄清问题；不要让语言模型直接成为 SQL 生成层。
- 指标查询到 SQL 的转换必须由确定性的 Rust planner 完成。

## 架构约束

- Rust-only 原型不追求兼容 Cube 的 JS/YAML schema compiler。
- 原型不依赖 YAML 文件表达模型；OpenUI/对话流程产出的结构化结果应写入语义目录。
- 多数据库兼容层应拆成探查 adapter、方言渲染 adapter、执行 adapter。
- 第一条垂直切片优先支持 Postgres；第二个数据库优先选择 ClickHouse，用于验证 adapter interface 是否足够稳固。
- Cube 现有 Postgres driver 是 Node 包，不作为 Rust-only 原型的数据库 adapter 复用。
