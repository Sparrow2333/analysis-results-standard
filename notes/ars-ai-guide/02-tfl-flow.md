# TFL 表达流

本文件只回答一个问题：如果把一张 TFL 看成 planned analysis 的结构化表达，ARS 中哪些对象通常参与表达？

## 从一张表开始

一张常见统计表可以拆成几类语义：

| TFL 语义 | ARS 常见承载对象 | 说明 |
| --- | --- | --- |
| 这张表属于哪个报告事件 | `ReportingEvent` | 根对象，收纳所有相关定义 |
| 表在目录中的位置 | `ListOfContents`, `OrderedListItem` | 目录层级、顺序、引用 output 或 analysis |
| 表作为一个输出 | `Output` | 输出定义、文件规格、display 列表、分类、程序信息 |
| 表标题和展示文本 | `OutputDisplay`, `DisplaySection`, `DisplaySubSection` | display title、header、title、footnote 等 |
| 表中计划的分析 | `Analysis` | 分析变量、分析集、数据子集、分组、方法、结果 |
| 分析人群或记录范围 | `AnalysisSet`, `DataSubset`, `WhereClause` | subject population 和 record subset |
| 分组/分层 | `GroupingFactor`, `Group`, `OrderedGroupingFactor` | treatment arm、visit、category 等分组语义 |
| 统计量 | `AnalysisMethod`, `Operation` | count、percent、mean、SD 等操作 |
| 结果值 | `OperationResult`, `ResultGroup` | raw/formatted result 和结果所属分组 |
| SAP/CSR/程序引用 | `ReferenceDocument`, `DocumentReference`, programming code objects | 外部文档和代码追踪 |

## 推荐追踪路径

### 1. 先定位 reporting event

从 `ReportingEvent` 看全局有哪些 outputs、analyses、analysis sets、data subsets、groupings 和 methods。

如果问题是“这组 shell 是否能整体表达”，优先检查 `ReportingEvent` 是否能把这些对象放在同一实例里。

### 2. 再定位目录和 output

通过 `mainListOfContents` 找 outputs 和 analyses 的组织方式。目录项中的 `outputId` 和 `analysisId` 是理解顺序和层级的关键。

通过 `Output.displays` 找一个 output 对应哪些 `OutputDisplay`，以及它们的顺序。

### 3. 再看 display 文本

通过 `OutputDisplay.displayTitle` 和 `displaySections` 看标题、脚注、页眉、图例、缩略语等展示文本。

注意：这里通常不是最终版式规则。多行标题、缩进、合并单元格、分页等需要额外规则。

### 4. 再看 analysis 定义

每个 `Analysis` 说明一个 planned analysis：

- 分析哪个 dataset / variable
- 使用哪个 `methodId`
- 使用哪个 `analysisSetId`
- 是否有 `dataSubsetId`
- 是否有 `orderedGroupings`
- 是否包含 `results`

### 5. 再看 method、operation 和 result

`Analysis.methodId` 指向 `AnalysisMethod`。方法包含一个或多个 `Operation`，每个 operation 产生一个分析结果值。

`Analysis.results` 中的 `OperationResult.operationId` 指回对应 `Operation`。结果值可放在 `rawValue` 和 `formattedValue`，结果所属分组放在 `resultGroups`。

## 一个紧凑对象流

```text
ReportingEvent
  -> mainListOfContents / otherListsOfContents
     -> OrderedListItem.outputId / analysisId
  -> outputs
     -> Output.displays
        -> OutputDisplay.displayTitle
        -> OutputDisplay.displaySections
  -> analyses
     -> Analysis.dataset / variable
     -> Analysis.analysisSetId / dataSubsetId
     -> Analysis.orderedGroupings
     -> Analysis.methodId
     -> Analysis.results
  -> methods
     -> AnalysisMethod.operations
        -> Operation.resultPattern
  -> analysisSets / dataSubsets / groups
     -> WhereClause
```

## 重要边界

- `OutputDisplay` 说明 display，不保证完整 shell layout。
- `Analysis` 说明 planned analysis，不保证能自动计算结果。
- `Operation.resultPattern` 可表达展示模式或格式指令，但不等于完整格式化规范。
- `WhereClause` 能表达筛选条件，但复杂业务规则仍要看是否能转成 condition 或 compound expression。
- `ListOfContents` 是目录结构，不是表格结构。

## 示例材料入口

查 TFL-like 示例时先看：

- `workfiles/examples/ARS v1/Common Safety Displays.yaml`
- `workfiles/examples/ARS v1/Common Safety Displays.json`
- `workfiles/examples/ARS v1/FDA Standard Safety Tables and Figures.yaml`
- `workfiles/examples/Hackathon/Common Safety Displays.yaml`
- `workfiles/examples/US Interchange/Common Safety Displays.xlsx`

如果只是理解对象关系，优先读 `project/docs/*.md`，不要先打开大型 Excel 或 PDF。

