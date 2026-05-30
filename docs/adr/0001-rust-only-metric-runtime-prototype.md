---
status: accepted
---

# 围绕语义目录构建 Rust-only 原型

Rust-only 原型不尝试兼容 Cube 的 JS/YAML schema compiler。原型使用语义目录作为事实来源，保存用户确认过的数据源、源表、源字段、指标、维度、关联关系、描述和别名。

建模体验可以由 OpenUI 风格界面和对话流程驱动，但持久化结果必须是结构化目录数据，而不是生成出来的 YAML。自然语言问题必须通过 NL2Metric 解析为引用语义目录条目的指标查询；语言模型不能直接成为 SQL 生成层。只有确定性的 Rust planner 可以把指标查询转换成 SQL。

数据库支持采用分层 adapter：探查 adapter、方言渲染 adapter、执行 adapter。这样指标查询和语义目录可以保持数据库无关，同时允许 Postgres、ClickHouse、MySQL 和未来数据库在元数据查询、标识符引用、参数占位符、时间粒度函数、结果类型转换、流式读取和连接行为上各自不同。

**考虑过的选项**

- 继续使用 YAML 作为模型格式：拒绝。它会保留太多 Cube schema compiler 的复杂度，也无法验证目标产品流程。
- 从自然语言直接生成 SQL：拒绝。它会绕过认证指标和维度，导致结果难以信任。
- 复用 Cube 现有 Node driver：拒绝。Rust-only 验证需要 Rust adapter，而当前 Cube Postgres driver 是基于 `pg` 的 Node 包。

**后果**

该原型是一个新的 metric-first runtime，而不是 Cube 兼容运行时。第一个有价值的垂直切片应支持一个数据源，优先选择 Postgres，并包含 schema 探查、基于语义目录创建指标和维度、基于已确认目录条目的 NL2Metric、确定性 SQL 生成、查询执行和表格结果。多数据库验证应在泛化 adapter interface 之前加入第二个 adapter，优先选择 ClickHouse。
