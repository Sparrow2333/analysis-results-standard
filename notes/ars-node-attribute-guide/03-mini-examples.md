# 迷你示例

这些示例用于展示常见节点和属性如何一起工作。它们是教学用的紧凑写法，不覆盖完整 schema，也不代表完整 TFL 渲染模型。

## 示例 1：Safety Population 的 subject count by treatment

这个例子回答：如何表达“在 Safety Population 中，按 treatment 统计受试者数”。

```yaml
id: DEMO_EVENT
name: Demo Reporting Event

analysisSets:
- id: AS_SAF
  name: Safety Population
  level: 1
  order: 1
  condition:
    dataset: ADSL
    variable: SAFFL
    comparator: EQ
    value:
    - Y

analysisGroupings:
- id: AG_TRT
  name: Treatment
  groupingDataset: ADSL
  groupingVariable: TRT01A
  dataDriven: false
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
  - id: AG_TRT_2
    name: Xanomeline Low Dose
    level: 1
    order: 2
    condition:
      dataset: ADSL
      variable: TRT01A
      comparator: EQ
      value:
      - Xanomeline Low Dose

methods:
- id: M_GRP_CNT
  name: Count by group
  operations:
  - id: M_GRP_CNT_1_N
    name: Count of subjects
    label: n
    order: 1
    resultPattern: N = XXX

analyses:
- id: A_SAF_SUM_USUBJID_TRT
  name: Summary of Subjects by Treatment
  reason:
    controlledTerm: SPECIFIED IN SAP
  purpose:
    controlledTerm: PRIMARY OUTCOME MEASURE
  dataset: ADSL
  variable: USUBJID
  analysisSetId: AS_SAF
  methodId: M_GRP_CNT
  orderedGroupings:
  - order: 1
    groupingId: AG_TRT
    resultsByGroup: true
  results:
  - operationId: M_GRP_CNT_1_N
    resultGroups:
    - groupingId: AG_TRT
      groupId: AG_TRT_1
    rawValue: '86'
    formattedValue: N = 86
  - operationId: M_GRP_CNT_1_N
    resultGroups:
    - groupingId: AG_TRT
      groupId: AG_TRT_2
    rawValue: '84'
    formattedValue: N = 84
```

怎么读：

- `AS_SAF` 定义 Safety Population。
- `AG_TRT` 定义 treatment 分组和预定义组。
- `M_GRP_CNT` 定义统计操作：count。
- `A_SAF_SUM_USUBJID_TRT` 把数据集、变量、人群、方法和分组串起来。
- `results` 保存每个 treatment 组下的结果值。

## 示例 2：一个 output display 的标题、页脚和缩略语

这个例子回答：如何表达一张表的 display 标题和展示文本。

```yaml
outputs:
- id: O_T2
  name: Table 2. Baseline Demographic and Clinical Characteristics
  displays:
  - order: 1
    display:
      id: D_T2
      name: Table 2
      version: 1
      displayTitle: Table 2. Baseline Demographic and Clinical Characteristics, Safety Population
      displaySections:
      - sectionType: Title
        orderedSubSections:
        - order: 1
          subSection:
            id: D_T2_Title_1
            text: Table 2. Baseline Demographic and Clinical Characteristics, Safety Population
      - sectionType: Footer
        orderedSubSections:
        - order: 1
          subSection:
            id: D_T2_Footer_1
            text: 'Source dataset: adsl'
      - sectionType: Abbreviation
        orderedSubSections:
        - order: 1
          subSection:
            id: D_T2_Abbrv_1
            text: N, number of patients in treatment arm
        - order: 2
          subSection:
            id: D_T2_Abbrv_2
            text: SD, standard deviation
```

怎么读：

- `Output` 是输出定义。
- `displays` 说明这个 output 包含哪些 display 以及顺序。
- `displayTitle` 是 display 的识别标题。
- `displaySections` 组织 title、footer、abbreviation 等展示文本。

注意边界：

- 这里没有定义字体、字号、缩进、分页、列宽、合并单元格。
- 如果需要完整渲染，需要 ARS 之外的渲染约定或项目自定义标准。

## 示例 3：百分比统计量引用分子和分母 operation

这个例子回答：如何表达“% 的计算依赖 n 作为分子，依赖另一个 count 作为分母”。

```yaml
methods:
- id: M_GRP_SUM_CATEG
  name: Summary by group of a categorical variable
  operations:
  - id: M_GRP_SUM_CATEG_1_N
    name: Count of subjects
    label: n
    order: 1
    resultPattern: n
  - id: M_GRP_SUM_CATEG_2_PCT
    name: Percent of subjects
    label: '%'
    order: 2
    referencedOperationRelationships:
    - id: M_GRP_SUM_CATEG_2_PCT_NUM
      referencedOperationRole:
        controlledTerm: NUMERATOR
      operationId: M_GRP_SUM_CATEG_1_N
      description: The count operation whose result provides the numerator.
    - id: M_GRP_SUM_CATEG_2_PCT_DEN
      referencedOperationRole:
        controlledTerm: DENOMINATOR
      operationId: M_GRP_CNT_1_N
      description: The count operation whose result provides the denominator.
    resultPattern: (%)
```

怎么读：

- `M_GRP_SUM_CATEG_1_N` 产生 n。
- `M_GRP_SUM_CATEG_2_PCT` 产生 %。
- `%` operation 通过 `referencedOperationRelationships` 说明自己的分子和分母来自哪些 operation。
- 这表达统计操作之间的依赖，不代表一定已经提供了完整计算公式或执行代码。

## 示例 4：简单条件和复合条件

简单条件适合单个比较：

```yaml
condition:
  dataset: ADAE
  variable: TRTEMFL
  comparator: EQ
  value:
  - Y
```

复合条件适合 AND/OR 组合：

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
      variable: AESER
      comparator: EQ
      value:
      - Y
```

怎么读：

- `condition` 是叶子条件。
- `compoundExpression` 是条件组合。
- 一个 `WhereClause` 通常应该在 `condition` 和 `compoundExpression` 之间二选一。

## 示例 5：目录同时指向 output 和 analysis

这个例子回答：为什么目录里既会看到 `outputId`，也会看到 `analysisId`。

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
      sublist:
        listItems:
        - name: Summary of Subjects by Treatment
          level: 2
          order: 1
          analysisId: A_SAF_SUM_USUBJID_TRT
        - name: Summary of Age by Treatment
          level: 2
          order: 2
          analysisId: A_SAF_SUM_AGE_TRT
```

怎么读：

- 顶层目录项指向 `O_T2` 这个 output。
- 子目录项指向各个 planned analyses。
- 目录表达层级和顺序，不表达表格行列坐标。

