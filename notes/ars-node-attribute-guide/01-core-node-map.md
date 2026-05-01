# 核心节点地图

ARS 可以先看成一个“报告事件中的分析结果语义网络”。它的核心不是直接画出 PDF/RTF 表格，而是说明一次 reporting event 里有哪些输出、哪些分析、用哪些方法、对哪些人群和数据记录、按哪些分组、产生哪些结果，以及这些内容如何被目录和 display 组织起来。

## 一条最常用主线

```text
ReportingEvent
  -> mainListOfContents / otherListsOfContents
     -> OrderedListItem.outputId / analysisId
  -> outputs
     -> Output.displays
        -> OrderedDisplay.display
           -> OutputDisplay.displayTitle / displaySections
  -> analyses
     -> Analysis.dataset / variable
     -> Analysis.analysisSetId / dataSubsetId
     -> Analysis.orderedGroupings
     -> Analysis.methodId
     -> Analysis.results
  -> methods
     -> AnalysisMethod.operations
        -> Operation.resultPattern
  -> analysisSets / dataSubsets / analysisGroupings
     -> WhereClause.condition / compoundExpression
  -> OperationResult.resultGroups
     -> ResultGroup.groupingId / groupId / groupValue
```

## 节点角色速查

| 节点 | 可以先理解为 | 常见问题 |
| --- | --- | --- |
| `ReportingEvent` | 一次报告需求的根容器 | 这次 CSR、interim analysis 或安全表集合里有哪些对象？ |
| `ListOfContents` | 分析和输出的目录 | 某张表或某个分析在目录中排在哪里？ |
| `OrderedListItem` | 目录中的一个有序条目 | 这一行目录指向一个 output，还是一个 analysis？ |
| `Output` | 一个输出成品或输出定义 | 这张表/图/listing 作为 output 有哪些 display 和文件规格？ |
| `OrderedDisplay` | output 中按顺序排列的 display | 一个 output 里多个 display 的顺序是什么？ |
| `OutputDisplay` | 一个或多个分析结果的表格式表示 | display 标题、脚注、页眉等展示文本是什么？ |
| `DisplaySection` | display 中一类展示文本 | 这是 title、footnote、legend、abbreviation 还是 footer？ |
| `Analysis` | 一次 planned analysis 的规格 | 分析哪个变量、用什么方法、用哪个分析集、按什么分组？ |
| `AnalysisSet` | 受试者层面的分析人群 | Safety Population、ITT、FAS 这类人群怎么定义？ |
| `DataSubset` | 数据记录层面的分析子集 | 只分析 treatment-emergent AE、某个 visit、某类记录吗？ |
| `WhereClause` | 筛选条件 | 条件是简单比较，还是 AND/OR 组合？ |
| `GroupingFactor` | 一个分组因子 | 按 treatment、sex、visit、SOC/PT 等哪个因素分组？ |
| `Group` | 分组因子下的一个预定义组 | Placebo、High Dose、Male、Female 这样的组如何定义？ |
| `OrderedGroupingFactor` | 某个 analysis 使用分组的顺序 | 先按 treatment，再按 sex，还是相反？每组是否有结果？ |
| `AnalysisMethod` | 一组统计操作 | 这个分析方法包括 n、%、mean、SD 等哪些操作？ |
| `Operation` | 产生单个结果值的统计操作 | 这个 operation 产出的是 n、%、mean 还是 SD？格式模式是什么？ |
| `OperationResult` | 某个 operation 的实际结果值 | 算出来的 raw value 和 formatted value 是什么？ |
| `ResultGroup` | 结果值对应的分组坐标 | 这个结果属于哪个 treatment、sex、visit 或 data-driven group？ |

## 输出主线和分析主线要分开看

输出主线回答“报告里展示什么”：

```text
ReportingEvent -> outputs -> Output -> displays -> OutputDisplay
```

分析主线回答“这些值怎么定义和产生”：

```text
ReportingEvent -> analyses -> Analysis -> AnalysisMethod -> Operation -> OperationResult
```

两条线会在目录、display、analysis 引用和结果展示时发生联系，但它们不是同一个层面。`OutputDisplay` 不是完整 shell layout，`Analysis` 也不是最终渲染后的表格行列。

## 三个容易混淆的边界

### `Output` 和 `OutputDisplay`

`Output` 更像“输出成品/输出定义”，可以包含文件规格、分类、文档引用、程序信息，以及一个或多个有序 display。

`OutputDisplay` 更像“展示内容单元”，包含 `displayTitle` 和 display sections。一个 output 可以有多个 display。

### `AnalysisSet` 和 `DataSubset`

`AnalysisSet` 通常描述受试者集合，比如 Safety Population。

`DataSubset` 通常描述记录集合，比如只取 treatment-emergent adverse events。

一个 `Analysis` 可以同时引用 `analysisSetId` 和 `dataSubsetId`：前者限定人群，后者限定记录。

### `GroupingFactor`、`Group` 和 `ResultGroup`

`GroupingFactor` 定义分组维度，比如 treatment。

`Group` 定义该维度下的预设组，比如 Placebo。

`ResultGroup` 出现在结果值上，说明某个 `OperationResult` 属于哪个分组维度和哪个组。

## 查官方定义的位置

| 节点 | 官方生成文档 |
| --- | --- |
| `ReportingEvent` | `project/docs/ReportingEvent.md` |
| `Output` | `project/docs/Output.md` |
| `OutputDisplay` | `project/docs/OutputDisplay.md` |
| `DisplaySection` | `project/docs/DisplaySection.md` |
| `Analysis` | `project/docs/Analysis.md` |
| `AnalysisSet` | `project/docs/AnalysisSet.md` |
| `DataSubset` | `project/docs/DataSubset.md` |
| `WhereClause` | `project/docs/WhereClause.md` |
| `GroupingFactor` | `project/docs/GroupingFactor.md` |
| `Group` | `project/docs/Group.md` |
| `OrderedGroupingFactor` | `project/docs/OrderedGroupingFactor.md` |
| `AnalysisMethod` | `project/docs/AnalysisMethod.md` |
| `Operation` | `project/docs/Operation.md` |
| `OperationResult` | `project/docs/OperationResult.md` |
| `ResultGroup` | `project/docs/ResultGroup.md` |

