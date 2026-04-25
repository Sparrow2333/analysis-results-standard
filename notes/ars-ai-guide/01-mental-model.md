# ARS 心智模型

ARS 最适合从“报告事件中的分析结果语义网络”来理解，而不是从“画一张表”的角度开始理解。

## 一句话模型

一个 `ReportingEvent` 描述一次报告需求下的分析、输出、目录、文档引用、术语扩展和可复用定义；其中 TFL 相关信息分散在 output/display、planned analysis、method/operation、result/grouping、where clause 等对象中。

## 四层理解

### Level 0：根容器

`ReportingEvent` 是树根。它收纳：

- `mainListOfContents` / `otherListsOfContents`
- `referenceDocuments`
- `terminologyExtensions`
- `analysisOutputCategorizations`
- `analysisSets`
- `dataSubsets`
- `analysisGroupings`
- `methods`
- `analyses`
- `globalDisplaySections`
- `outputs`

读 ARS 对象时，先问：这个对象是被 `ReportingEvent` 直接收纳，还是通过某个对象间接引用？

### Level 1：目录与输出组织

`ListOfContents` 和 `OrderedListItem` 表达 reporting event 中 analyses 和 outputs 的结构化目录。它们负责层级、顺序和引用，不负责表格行列布局。

`Output` 表达一次 planned analyses 的结果报告及评价。它可以包含文件规格、一个或多个有序 displays、分类、文档引用和输出程序信息。

### Level 2：展示与文本

`OutputDisplay` 是一个或多个分析结果的表格式表示。它有 `displayTitle`，也可以通过 `DisplaySection` / `DisplaySubSection` 表达 title、footnote、legend、abbreviation、footer 等展示文本。

这一层能表达“有哪些展示文本、按什么顺序出现”，但不等于完整的物理排版规则。

### Level 3：分析定义与结果

`Analysis` 表达 planned analysis 的规格：分析变量、分析方法、分析集、数据子集、分组因子、代码引用和结果。

`AnalysisMethod` 是统计操作集合。`Operation` 是产生单个分析结果值的统计操作。`OperationResult` 绑定某个 `operationId`，并通过 `rawValue` / `formattedValue` 表达结果值，通过 `ResultGroup` 表达该结果对应的分组值。

## 两条主线

### 输出主线

```text
ReportingEvent
  -> outputs
  -> Output
  -> displays
  -> OrderedDisplay
  -> OutputDisplay
  -> DisplaySection / DisplaySubSection
```

这条线回答：报告里有什么 output，output 中有哪些 display，display 上有哪些标题、脚注等展示文本。

### 分析主线

```text
ReportingEvent
  -> analyses
  -> Analysis
  -> AnalysisMethod
  -> Operation
  -> OperationResult
  -> ResultGroup
```

这条线回答：计划做什么分析，使用什么方法和操作，结果值是什么，结果属于哪个分组。

## 常见误解

- ARS 不是 PDF/RTF/HTML 表格布局模型。
- ARS 不是完整统计计算引擎。
- ARS 不是 ADaM 或 SDTM 数据集本身。
- ARS 可以引用程序代码或模板，但这不意味着它规定了所有执行环境。
- ARS 能表达很多 TFL 语义，但复杂 shell 的视觉结构通常需要项目约定或外部渲染模型。

## 需要精确时读哪里

- 根对象：`project/docs/ReportingEvent.md`
- 输出：`project/docs/Output.md`
- 展示：`project/docs/OutputDisplay.md`, `project/docs/DisplaySection.md`
- 分析：`project/docs/Analysis.md`, `notes/Analysis.md`
- 方法和操作：`project/docs/AnalysisMethod.md`, `project/docs/Operation.md`
- 结果：`project/docs/OperationResult.md`, `project/docs/ResultGroup.md`
- 筛选条件：`project/docs/WhereClause.md`

