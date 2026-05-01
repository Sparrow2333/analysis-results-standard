# ARS 节点与属性理解指南

这个目录用于持续整理 ARS 标准中常见节点和属性的作用、关系、使用场景与示例。它不是官方文档的替代品，而是一个面向理解和应用的中文速查层：先帮助你知道“这个节点/属性解决什么问题”，再指向需要精确验证时应查的原始资料。

现阶段只覆盖常见、经常使用、对理解 TFL 表达很关键的对象和属性。未来可以继续补充更细字段、复杂示例、项目约定以及自定义标准。

## 当前内容

| 文件 | 用途 |
| --- | --- |
| [01-core-node-map.md](01-core-node-map.md) | 从 `ReportingEvent` 到 output、display、analysis、method、result 的核心节点地图 |
| [02-common-attributes.md](02-common-attributes.md) | 高频属性解释：标识、目录、输出、展示、分析、筛选、分组、方法、结果 |
| [03-mini-examples.md](03-mini-examples.md) | 几个紧凑示例，展示常见属性如何串起来 |
| [custom-standards/README.md](custom-standards/README.md) | 未来放置自定义标准、项目口径和扩展约定 |

## 推荐阅读顺序

1. 先读 [01-core-node-map.md](01-core-node-map.md)，建立对象关系。
2. 再读 [02-common-attributes.md](02-common-attributes.md)，理解常见属性的用途。
3. 遇到“这些字段怎么组合”时读 [03-mini-examples.md](03-mini-examples.md)。
4. 需要精确字段类型、cardinality、枚举或完整 schema 时，回到 `project/docs/`、`model/ars_ldm.yaml` 或 `project/jsonschema/ars_ldm.schema.json`。

## 初版范围

本目录初版重点覆盖以下对象：

- `ReportingEvent`
- `ListOfContents` / `OrderedListItem`
- `Output` / `OrderedDisplay` / `OutputDisplay`
- `DisplaySection`
- `Analysis`
- `AnalysisSet` / `DataSubset` / `WhereClause`
- `GroupingFactor` / `Group` / `OrderedGroupingFactor`
- `AnalysisMethod` / `Operation`
- `OperationResult` / `ResultGroup`

刻意暂不全面展开：

- 所有 reference document 细节
- 所有 programming code 细节
- 全量 terminology extension
- 完整 table shell、版式、分页、字体、缩进和渲染规则
- 所有枚举值和 schema 约束

## 和现有资料的关系

本目录基于以下入口整理：

- `notes/ars-ai-guide/README.md`
- `notes/ars-ai-guide/01-mental-model.md`
- `notes/ars-ai-guide/02-tfl-flow.md`
- `notes/ars-ai-guide/03-source-map.md`
- `notes/Analysis.md`
- `project/docs/*.md`
- `workfiles/examples/ARS v1/FDA Standard Safety Tables and Figures.yaml`

如果本目录和官方生成文档存在冲突，以 `project/docs/`、`model/ars_ldm.yaml` 和 schema 为准。

## 使用原则

- 先理解对象角色，再看字段定义。
- 先追踪引用链，再判断某个属性是否缺失。
- `id` 类字段用于对象间引用，不等于展示文本。
- `name` / `label` / `description` 更偏人类阅读和展示。
- `Analysis` 定义“做什么分析”，`AnalysisMethod` 定义“怎么算”，`OperationResult` 保存“算出来的值”。
- `OutputDisplay` 能表达 display 的标题和展示文本，但不等于完整物理版式模型。

