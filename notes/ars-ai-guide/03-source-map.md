# ARS 资料地图

本文件用于在需要更深上下文时快速定位原始资料。原则：先读小笔记，只有问题需要精确证据时才读原始资料。

## 压缩笔记

| 文件 | 用途 |
| --- | --- |
| `notes/ars-ai-guide/README.md` | 总入口和上下文预算 |
| `notes/ars-ai-guide/00-ai-loading-protocol.md` | AI 读取顺序和任务路由 |
| `notes/ars-ai-guide/01-mental-model.md` | ARS 概念模型 |
| `notes/ars-ai-guide/02-tfl-flow.md` | TFL 语义到 ARS 对象流 |
| `notes/ars-ai-guide/04-capability-boundaries.md` | 能力边界摘要 |
| `notes/ars-capability-checklist.md` | 更完整的能力检查单 |
| `notes/Analysis.md` | `Analysis` 类中文说明 |

## 官方/原始项目资料

| 路径 | 用途 |
| --- | --- |
| `README.md` | 项目目标、背景、future state |
| `model/ars_ldm.md` | Mermaid 模型概览 |
| `model/ars_ldm.yaml` | 模型源定义 |
| `project/docs/` | 生成的 Markdown 类和字段文档 |
| `docs/` | 生成的 HTML 文档站点 |
| `project/jsonschema/ars_ldm.schema.json` | JSON Schema |
| `project/graphql/ars_ldm.graphql` | GraphQL 形式 |
| `project/protobuf/ars_ldm.proto` | Protobuf 形式 |
| `workfiles/examples/` | 示例文件 |
| `documents/references/` | SAP、ICH E9 等参考材料 |

## 高频对象页

| 主题 | 首选文件 |
| --- | --- |
| 根对象 | `project/docs/ReportingEvent.md` |
| 目录 | `project/docs/ListOfContents.md`, `project/docs/OrderedListItem.md`, `project/docs/NestedList.md` |
| 输出 | `project/docs/Output.md`, `project/docs/OutputFile.md` |
| 展示 | `project/docs/OutputDisplay.md`, `project/docs/DisplaySection.md`, `project/docs/DisplaySubSection.md` |
| 分析 | `project/docs/Analysis.md`, `notes/Analysis.md` |
| 分析集/数据子集 | `project/docs/AnalysisSet.md`, `project/docs/DataSubset.md`, `project/docs/WhereClause.md` |
| 分组 | `project/docs/GroupingFactor.md`, `project/docs/Group.md`, `project/docs/OrderedGroupingFactor.md` |
| 方法和统计操作 | `project/docs/AnalysisMethod.md`, `project/docs/Operation.md` |
| 结果 | `project/docs/OperationResult.md`, `project/docs/ResultGroup.md` |
| 文档引用 | `project/docs/ReferenceDocument.md`, `project/docs/DocumentReference.md`, `project/docs/PageRef.md` |

## 查字段的规则

如果要查某个字段，优先读 `project/docs/<field>.md`。例如：

- `project/docs/displayTitle.md`
- `project/docs/analysisSetId.md`
- `project/docs/dataSubsetId.md`
- `project/docs/orderedGroupings.md`
- `project/docs/resultPattern.md`
- `project/docs/formattedValue.md`

如果要查某个类有哪些字段，读 `project/docs/<Class>.md`。

## 查示例的规则

先用 `rg` 搜索对象名或字段名：

```bash
rg "ReportingEvent|mainListOfContents|displayTitle|methodId|resultPattern" workfiles/examples
```

如果要找 YAML 示例，先用：

```bash
rg "displayTitle|methodId|operationId|formattedValue" workfiles/examples -g "*.yaml"
```

如果要找 JSON 示例，先用：

```bash
rg "displayTitle|methodId|operationId|formattedValue" workfiles/examples -g "*.json"
```

## 何时读 schema

读 `project/jsonschema/ars_ldm.schema.json` 或 `model/ars_ldm.yaml` 的条件：

- 需要验证 JSON/YAML 是否符合结构。
- 需要确认字段类型、枚举、required。
- 需要自动生成或转换数据。
- 生成文档不够明确，需要回到模型源。

普通概念解释优先不用 schema，避免上下文过大。

