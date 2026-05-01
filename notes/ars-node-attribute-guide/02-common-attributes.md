# 常用属性说明

本文件按使用场景解释高频属性。它关注“怎么理解”和“什么时候用”，不是完整字段清单。

## 1. 标识与人类可读信息

### `id`

`id` 是对象实例的标识，常用于其他对象引用。比如 `Analysis.methodId: M_GRP_CNT` 指向 `methods` 中 `id: M_GRP_CNT` 的 `AnalysisMethod`。

常见用法：

```yaml
methods:
- id: M_GRP_CNT
  name: Count by group for a categorical variable

analyses:
- id: A_SAF_SUM_USUBJID_TRT
  methodId: M_GRP_CNT
```

理解要点：

- `id` 偏机器引用和追踪。
- 同一引用范围内应稳定、唯一、可读。
- 不要把 `id` 当作最终展示标题。

### `name`

`name` 是对象名称，通常用于人类识别对象。很多继承自 `NamedObject` 的类都有 `name`，如 `ReportingEvent`、`Output`、`Analysis`、`GroupingFactor`、`Operation`。

示例：

```yaml
- id: A_SAF_SUM_USUBJID_TRT
  name: Summary of Subjects by Treatment
```

理解要点：

- `name` 可以比 `id` 更自然。
- `name` 不一定是最终 TFL 标题。
- `name` 适合在目录、说明和人工检查中使用。

### `label`

`label` 是短标签，适合显示在紧凑位置或作为简短说明。

示例：

```yaml
operations:
- id: M_GRP_SUM_CATEG_1_N
  name: Count of subjects
  label: n
```

理解要点：

- `label` 往往比 `name` 更短。
- 常见于 operation 的统计量标签，如 `n`、`%`、`SD`。

### `description`

`description` 用于更长解释，适合说明对象意图、计算逻辑或项目约定。

示例：

```yaml
methods:
- id: M_GRP_SUM_CATEG
  description: Descriptive summary statistics across groups for a categorical variable,
    based on subject occurrence
```

理解要点：

- 当 `name` 和 `label` 不足以解释口径时，用 `description`。
- 适合记录人类语义，不适合替代结构化字段。

### `version`

`version` 表示对象版本序号，常见于 `ReportingEvent`、`Analysis`、`Output`、`OutputDisplay`。

示例：

```yaml
outputs:
- id: O_T2
  version: 1
```

理解要点：

- 它用于表达同一对象定义的版本变化。
- 不应和文件版本、程序版本或研究版本混为一谈，除非项目明确约定。

## 2. 目录和引用

### `mainListOfContents`

`mainListOfContents` 是 reporting event 的主目录，组织 analyses 和 outputs。

示例：

```yaml
mainListOfContents:
  name: List of Planned Analyses
  label: LOPA
  contentsList:
    listItems:
    - name: Table 2. Baseline Demographic and Clinical Characteristics
      level: 1
      order: 1
      outputId: O_T2
```

理解要点：

- 它表达目录结构，不表达表格布局。
- 目录项可通过 `outputId` 指向 output，也可通过 `analysisId` 指向 analysis。

### `otherListsOfContents`

`otherListsOfContents` 用于主目录之外的其他目录，例如单独的 planned outputs 列表。

示例：

```yaml
otherListsOfContents:
- name: List of Planned Outputs
  label: LOPO
```

理解要点：

- 可用于从不同视角组织同一批 analyses/outputs。
- 它不复制对象本身，只是建立另一套目录组织。

### `level` 和 `order`

`level` 表示层级，`order` 表示同层顺序。它们常见于目录项、where clause、group、operation、ordered grouping、ordered display 等对象。

示例：

```yaml
listItems:
- name: Sex
  level: 2
  order: 2
```

理解要点：

- `level: 1` 通常是最高层。
- `order` 只表达顺序，不表达缩进像素、行高或分页。

### `outputId` 和 `analysisId`

`outputId` 指向一个 `Output`，`analysisId` 指向一个 `Analysis`。

示例：

```yaml
- name: Table 2
  outputId: O_T2

- name: Summary of Age by Treatment
  analysisId: A_SAF_SUM_AGE_TRT
```

理解要点：

- 目录项可以组织 output，也可以组织 analysis。
- 这两个属性帮助从目录跳到实际对象定义。

## 3. 输出和展示

### `outputs`

`ReportingEvent.outputs` 收纳本次 reporting event 中定义的 outputs。

示例：

```yaml
outputs:
- id: O_T2
  name: Table 2. Baseline Demographic and Clinical Characteristics
```

理解要点：

- output 是报告成果层面的对象。
- 一个 output 可以包含文件规格、分类、文档引用、程序信息和 displays。

### `displays`

`Output.displays` 是 output 中的有序 display 列表。

示例：

```yaml
displays:
- order: 1
  display:
    id: D_T2
    name: Table 2
```

理解要点：

- `displays` 中的每个元素通常是 `OrderedDisplay`。
- `order` 控制 display 在 output 中的顺序。

### `displayTitle`

`displayTitle` 是报告中唯一识别 display 的描述性标题。

示例：

```yaml
display:
  id: D_T2
  displayTitle: Table 2. Baseline Demographic and Clinical Characteristics, Safety Population
```

理解要点：

- 它更接近 display 的标题语义。
- 它不是完整 title block 的唯一来源；具体展示文本还可能在 `displaySections` 中。

### `displaySections`

`displaySections` 表达 display 中的展示文本部分，比如 title、footer、abbreviation。

示例：

```yaml
displaySections:
- sectionType: Title
  orderedSubSections:
  - order: 1
    subSection:
      id: D_T2_Title_1
      text: Table 2. Baseline Demographic and Clinical Characteristics
- sectionType: Footer
  orderedSubSections:
  - order: 1
    subSection:
      id: D_T2_Footer_1
      text: 'Source dataset: adsl'
```

理解要点：

- `sectionType` 说明这是哪类展示文本。
- `orderedSubSections` 说明同一 section 中多段文本的顺序。
- 它不等于完整版式规则，例如字体、对齐、分页、合并单元格通常需要外部约定。

### `fileSpecifications`

`fileSpecifications` 描述 output 的输出文件规格，例如 PDF、RTF 文件位置。

示例：

```yaml
fileSpecifications:
- name: t2-demog (PDF)
  label: t2-demog
  fileType:
    controlledTerm: pdf
  location: ./t2-demog.pdf
```

理解要点：

- 它说明产物文件是什么、在哪里。
- 它不直接说明分析如何计算。

## 4. 分析定义

### `analyses`

`ReportingEvent.analyses` 收纳 planned analyses。

示例：

```yaml
analyses:
- id: A_SAF_SUM_USUBJID_TRT
  name: Summary of Subjects by Treatment
```

理解要点：

- 每个 `Analysis` 是一次分析规格。
- 它连接分析变量、分析方法、分析集、数据子集、分组和结果。

### `reason` 和 `purpose`

`reason` 表达为什么做这个分析，`purpose` 表达该分析在证据链或报告中的用途。

示例：

```yaml
reason:
  controlledTerm: SPECIFIED IN SAP
purpose:
  controlledTerm: PRIMARY OUTCOME MEASURE
```

理解要点：

- 它们是 `Analysis` 的重要语义字段。
- 常用于区分 SAP 指定、探索性分析、主要/次要/安全性用途等。

### `dataset` 和 `variable`

`dataset` 说明分析数据集，`variable` 说明分析变量。

示例：

```yaml
dataset: ADSL
variable: USUBJID
```

理解要点：

- 这组字段回答“分析哪个数据集里的哪个变量”。
- 它们不定义人群或记录筛选；人群和记录范围通常由 `analysisSetId`、`dataSubsetId`、`WhereClause` 表达。

### `analysisSetId`

`analysisSetId` 引用一个 `AnalysisSet`，说明对哪个分析人群执行。

示例：

```yaml
analysisSets:
- id: AS_SAF
  name: Safety Population
  condition:
    dataset: ADSL
    variable: SAFFL
    comparator: EQ
    value:
    - Y

analyses:
- id: A_SAF_SUM_USUBJID_TRT
  analysisSetId: AS_SAF
```

理解要点：

- 常用于 Safety Population、ITT、FAS 等人群。
- `AnalysisSet` 本身可以通过 `condition` 或 `compoundExpression` 定义筛选逻辑。

### `dataSubsetId`

`dataSubsetId` 引用一个 `DataSubset`，说明分析只取某些数据记录。

示例：

```yaml
dataSubsets:
- id: DS_TRTEM_AE
  name: Treatment-emergent adverse events
  condition:
    dataset: ADAE
    variable: TRTEMFL
    comparator: EQ
    value:
    - Y

analyses:
- id: A_TEAE_SUM
  dataSubsetId: DS_TRTEM_AE
```

理解要点：

- `analysisSetId` 偏受试者集合。
- `dataSubsetId` 偏记录集合。
- 两者可以同时出现。

### `orderedGroupings`

`orderedGroupings` 说明一个 analysis 使用哪些分组因子、分组顺序，以及是否每组都产生结果。

示例：

```yaml
orderedGroupings:
- order: 1
  groupingId: AG_TRT
  resultsByGroup: true
```

理解要点：

- `groupingId` 指向 `GroupingFactor`。
- `resultsByGroup: true` 表示该 grouping 的每个 group 预期有单独结果。
- 多个 grouping 时，`order` 帮助表达分组层级或排序。

### `methodId`

`methodId` 引用一个 `AnalysisMethod`，说明这个 analysis 使用哪组统计操作。

示例：

```yaml
analyses:
- id: A_SAF_SUM_USUBJID_TRT
  methodId: M_GRP_CNT

methods:
- id: M_GRP_CNT
  operations:
  - id: M_GRP_CNT_1_N
    label: n
```

理解要点：

- `methodId` 是 analysis 和 method 的关键连接点。
- 方法可复用：多个 analysis 可以引用同一个 method。

## 5. 筛选条件

### `condition`

`condition` 是简单筛选条件，通常由 dataset、variable、comparator、value 组成。

示例：

```yaml
condition:
  dataset: ADSL
  variable: SAFFL
  comparator: EQ
  value:
  - Y
```

理解要点：

- 适合表达 `ADSL.SAFFL = "Y"` 这类简单条件。
- `AnalysisSet`、`DataSubset`、`Group` 都可以使用 `condition`。

### `compoundExpression`

`compoundExpression` 用于组合多个 where clause，常见逻辑是 AND/OR。

示例：

```yaml
compoundExpression:
  logicalOperator: AND
  whereClauses:
  - level: 2
    order: 1
    condition:
      dataset: ADAE
      variable: TRTEMFL
      comparator: EQ
      value:
      - Y
  - level: 2
    order: 2
    condition:
      dataset: ADAE
      variable: AESDTH
      comparator: EQ
      value:
      - Y
```

理解要点：

- 同一个 `WhereClause` 实例通常应该有 `condition` 或 `compoundExpression`，不要两者同时用。
- 复杂业务规则如果无法清晰转成条件表达，需要在项目约定或外部文档中补充。

## 6. 分组

### `analysisGroupings`

`ReportingEvent.analysisGroupings` 收纳可复用的分组因子。

示例：

```yaml
analysisGroupings:
- id: AG_TRT
  name: Treatment
  groupingDataset: ADSL
  groupingVariable: TRT01A
  dataDriven: false
```

理解要点：

- 它定义“按什么因素分组”。
- analysis 使用时通过 `orderedGroupings.groupingId` 引用。

### `groupingDataset` 和 `groupingVariable`

这两个字段说明分组基于哪个数据集的哪个变量。

示例：

```yaml
groupingDataset: ADSL
groupingVariable: TRT01A
```

理解要点：

- 对单变量分组很重要。
- 对复杂分组，可能还需要 `groups` 中的条件或项目约定。

### `dataDriven`

`dataDriven` 表示分组是预先定义，还是来自数据中的 distinct values。

示例：

```yaml
dataDriven: false
```

理解要点：

- `false`：通常会列出预定义 `groups`，如 Placebo、Low Dose、High Dose。
- `true`：分组值来自数据，结果中可通过 `groupValue` 表达具体值。

### `groups`

`groups` 是一个 grouping factor 下的预定义组。

示例：

```yaml
groups:
- id: AG_TRT_1
  name: Placebo
  level: 1
  order: 1
  condition:
    dataset: ADSL
    variable: TRT01A
    comparator: EQ
    value:
    - Placebo
```

理解要点：

- `Group` 可以用 `condition` 定义组成员。
- `groupId` 可在结果中被引用。

## 7. 方法、操作和结果

### `methods`

`ReportingEvent.methods` 收纳分析方法，每个 `AnalysisMethod` 包含一组 `Operation`。

示例：

```yaml
methods:
- id: M_GRP_SUM_CATEG
  name: Summary by group of a categorical variable
  operations:
  - id: M_GRP_SUM_CATEG_1_N
    label: n
  - id: M_GRP_SUM_CATEG_2_PCT
    label: '%'
```

理解要点：

- method 定义“要做哪些统计操作”。
- operation 定义“每一个结果值怎么产生”。

### `operations`

`operations` 是方法中的统计操作列表。每个 operation 通常产生一个结果值。

示例：

```yaml
operations:
- id: M_GRP_CNT_1_N
  name: Count of subjects
  label: n
  order: 1
  resultPattern: N = XXX
```

理解要点：

- `order` 表示统计量展示或计算顺序。
- `label` 可用于显示统计量名称。

### `resultPattern`

`resultPattern` 是结果展示的默认模式或格式。

示例：

```yaml
resultPattern: N = XXX
```

理解要点：

- 它可以表达 table shell 中的占位模式，如 `XX.X`、`N = XXX`、`(%)`。
- 它不是完整格式化引擎；舍入、空格、对齐等可能需要项目约定或渲染层处理。

### `referencedOperationRelationships`

`referencedOperationRelationships` 表达一个 operation 如何使用其他 operation 的结果，例如百分比的分子和分母。

示例：

```yaml
referencedOperationRelationships:
- id: M_GRP_SUM_CATEG_2_PCT_NUM
  referencedOperationRole:
    controlledTerm: NUMERATOR
  operationId: M_GRP_SUM_CATEG_1_N
- id: M_GRP_SUM_CATEG_2_PCT_DEN
  referencedOperationRole:
    controlledTerm: DENOMINATOR
  operationId: M_GRP_CNT_1_N
```

理解要点：

- 常用于百分比、比值、差值等依赖其他操作的统计量。
- `operationId` 指向被引用的 operation。

### `results`

`Analysis.results` 保存分析产生的 operation results。

示例：

```yaml
results:
- operationId: M_GRP_CNT_1_N
  rawValue: '86'
  formattedValue: N = 86
```

理解要点：

- `results` 是结果值集合。
- 每个结果必须能通过 `operationId` 找到对应 operation。

### `operationId`

`operationId` 在 `OperationResult` 中指向产生该结果的 `Operation`。

示例：

```yaml
- operationId: M_GRP_CNT_1_N
  formattedValue: N = 86
```

理解要点：

- 它把“值”连接回“统计操作定义”。
- 同一个 operation 会在不同分组下产生多个结果值。

### `rawValue` 和 `formattedValue`

`rawValue` 是未格式化或更接近原始计算结果的值，`formattedValue` 是按展示规则处理后的值。

示例：

```yaml
rawValue: '86'
formattedValue: N = 86
```

理解要点：

- `rawValue` 适合追踪和复核。
- `formattedValue` 适合展示。
- 如果存在舍入、百分号、括号、前缀等展示规则，二者可能不同。

### `resultGroups`

`resultGroups` 说明一个结果值对应哪些分组。

示例：

```yaml
resultGroups:
- groupingId: AG_TRT
  groupId: AG_TRT_1
```

理解要点：

- 当 `orderedGroupings.resultsByGroup: true` 时，结果通常应给出对应的 `resultGroups`。
- 多重分组时，一个结果可以带多个 `ResultGroup`。

### `groupingId`、`groupId` 和 `groupValue`

`groupingId` 指分组因子，`groupId` 指预定义组，`groupValue` 指 data-driven 分组的具体值。

预定义组示例：

```yaml
resultGroups:
- groupingId: AG_TRT
  groupId: AG_TRT_1
```

data-driven 分组示例：

```yaml
resultGroups:
- groupingId: AG_VISIT
  groupValue: Week 4
```

理解要点：

- 有预定义 `Group` 时，优先用 `groupId`。
- 分组来自数据 distinct values 时，可用 `groupValue`。

## 8. 文档和程序引用

### `documentRefs`

`documentRefs` 用于引用外部文档中的相关信息，如 SAP 页码或标准指南。

示例：

```yaml
documentRefs:
- referenceDocumentId: CDISCPILOT01_SAP
  pageRefs:
  - refType: PhysicalRef
    label: 6. ANALYSIS POPULATIONS
    pageNumbers:
    - 9
```

理解要点：

- 它适合做可追溯性。
- 它不直接定义计算逻辑，但可指向计算逻辑所在文档。

### `programmingCode`

`programmingCode` 可用于记录分析或 output 的程序语句，或引用程序文件。

理解要点：

- `Analysis.programmingCode` 偏具体分析实现。
- `Output.programmingCode` 偏具体 output 生成程序。
- `AnalysisMethod.codeTemplate` 偏可复用方法模板。

