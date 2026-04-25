# AI 加载协议

本文件定义后续 AI 对话如何按需读取 ARS 资料。优先目标是减少上下文占用，同时避免误把 ARS 当作纯 TFL 排版模型。

## 默认加载顺序

每次涉及 ARS 概念、TFL 表达或文档整理任务时，按下面顺序读取：

1. `notes/ars-ai-guide/README.md`
2. `notes/ars-ai-guide/00-ai-loading-protocol.md`
3. `notes/ars-ai-guide/01-mental-model.md`

只有当问题明确涉及 TFL 映射、能力边界、对象字段或示例时，才继续展开。

## 按问题类型选择资料

| 用户问题类型 | 先读 | 需要时再读 |
| --- | --- | --- |
| “ARS 是什么/怎么理解” | `01-mental-model.md` | `README.md`, `model/ars_ldm.md` |
| “一个 TFL 在 ARS 里怎么表达” | `02-tfl-flow.md` | `project/docs/ReportingEvent.md`, `project/docs/Output.md`, `project/docs/OutputDisplay.md`, `project/docs/Analysis.md` |
| “ARS 能不能表达某种表格语义” | `04-capability-boundaries.md` | `notes/ars-capability-checklist.md` |
| “某个类/字段是什么意思” | `03-source-map.md` | `project/docs/<ClassOrSlot>.md` |
| “需要 JSON/YAML 示例” | `03-source-map.md` | `workfiles/examples/**/*.yaml`, `workfiles/examples/**/*.json` |
| “需要 schema 或序列化约束” | `03-source-map.md` | `project/jsonschema/ars_ldm.schema.json`, `model/ars_ldm.yaml` |
| “需要官方生成站点页面” | `03-source-map.md` | `docs/<ObjectName>/index.html` |

## 读取深度规则

先用本目录回答方向性问题。只有出现以下信号时才读取官方对象页：

- 用户要求字段级准确性。
- 需要 cardinality、required/optional、range、枚举值。
- 需要判断某个对象是否能引用另一个对象。
- 需要构造或检查 JSON/YAML。
- 需要区分 ARS 原生能力、项目约定和外部渲染能力。

读取官方对象页时，优先读 `project/docs/*.md`，因为它比 `docs/*/index.html` 更适合文本上下文。

## 回答时的约束

- 始终区分“ARS 原生语义表达”和“外部渲染/布局解释”。
- 不要把 `OutputDisplay` 直接说成完整表格布局；它是一个或多个分析结果的 tabular representation，但行列位置和版式仍可能需要外部规则。
- 不要把 `OperationResult.formattedValue` 扩展成完整格式化引擎；它只是展示值或按 `resultPattern` 得到的结果值。
- 不要把 `ListOfContents` 当成表格 row/column 结构；它是分析和 outputs 的结构化目录。
- 不要假设所有 TFL shell 细节都能由 ARS 单独表达。
- 需要精确字段时引用官方生成文档路径，不凭记忆补字段。

## 最小上下文包

当上下文非常紧张时，只保留这段：

> ARS 是 CDISC 的分析结果逻辑模型。`ReportingEvent` 是根，收纳目录、outputs、analyses、analysis sets、data subsets、groupings、methods 和 reference documents。TFL 相关语义通常分散在 `Output`/`OutputDisplay` 的展示与输出组织、`Analysis` 的 planned analysis 定义、`AnalysisMethod`/`Operation` 的统计操作、`OperationResult`/`ResultGroup` 的结果和值分组、`WhereClause` 的筛选条件中。ARS 更偏语义和追踪，不等于精确渲染布局模型。遇到字段级问题读 `project/docs/<Class>.md`，遇到能力边界问题读 `notes/ars-capability-checklist.md`。

## 更新本指南的时机

当后续对话中发现新的高频问题、误解模式、对象关系或能力边界，应优先更新本目录的压缩笔记，而不是让每次对话重新探索。
