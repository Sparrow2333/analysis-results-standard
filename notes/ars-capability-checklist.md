# ARS 能力边界检查单

## 使用目的

本文是一份用于评估 ARS 作为统计分析表格底层接口能力边界的检查单。

本检查单不评估 EDC 建库文件解析能力，不评估 EDC 到 ADaM 的映射能力，也不评估前端拖拽交互能力。测试过程中所需的变量、label、分组、取值、标题、脚注、结果值等信息均可手工赋值。

本文只回答一个问题：

> 给定一个手工构造的 `ReportingEvent` 实例，ARS 本身能够表达统计分析表格的哪些语义？哪些能力需要约定、扩展或外部渲染模型？

## 评估对象

主要评估 ARS 中以下对象作为底层接口时的表达能力：

- `ReportingEvent`
- `ListOfContents`
- `Output`
- `OutputDisplay`
- `DisplaySection`
- `DisplaySubSection`
- `Analysis`
- `AnalysisSet`
- `DataSubset`
- `GroupingFactor`
- `Group`
- `AnalysisMethod`
- `Operation`
- `OperationResult`
- `ResultGroup`
- `WhereClause`
- `DocumentReference`

## 评估方式

每个检查项建议按以下方式执行：

1. 手工构造一个最小 `ReportingEvent` JSON/YAML。
2. 尽量只使用 ARS 原生字段表达目标语义。
3. 判断该语义是否能被稳定、无歧义地表达。
4. 判断渲染器是否能仅依赖 `ReportingEvent` 实例和通用渲染规则生成目标 shell。
5. 记录是否需要额外约定或外部模型。

## 能力等级

建议为每个检查项标注一个能力等级：

| 等级 | 含义 |
| --- | --- |
| A | ARS 原生支持，字段和关系语义清晰，无需额外模型 |
| B | ARS 可表达，但需要项目约定或渲染器解释规则 |
| C | ARS 只能表达部分语义，需要外部补充模型 |
| D | ARS 不适合表达，应放在外部模型 |

## 记录模板

每个检查项可以按以下格式记录：

```text
检查项：
目标语义：
手工输入：
使用的 ARS 对象：
是否能表达：
能力等级：
需要的额外约定：
边界判断：
```

## 已完成评估说明

状态：已完成初版能力边界评估。

评估日期：2026-04-25。

渐进式披露入口：`notes/ars-ai-guide/README.md`。

字段依据优先级：

1. `project/docs/*.md` 中生成的对象和 slot 文档。
2. `notes/ars-ai-guide/*.md` 中已整理的渐进式披露笔记。
3. 本检查单中的手工 TFL 场景。

勾选含义：

- `[x]` 表示该问题已经纳入评估，不表示答案一定是“ARS 原生支持”。
- 具体支持程度以“最终判定表”和“已完成结论”为准。

总体结论：

- ARS 可以作为最终 `ReportingEvent` 语义实例，稳定承载 planned analysis、output/display 文本、analysis set、data subset、grouping、method、operation、result 和追踪引用。
- ARS 可以支持“语义预览”和“约定驱动的可用 shell”，但不能单独承担精确 sponsor shell 的物理布局。
- row/column/cell 轴向、span、分页、列宽、复杂表头、block/panel、cell binding、模板槽位和冲突检测，应作为应用层 `DisplayLayout`、`CellBinding`、`ReportingEventTemplate` 或 conformance rules 设计。

## 检查项判定索引

| 检查项 | 等级 | 判定 |
| --- | --- | --- |
| RE-01 完整 reporting event | A | `ReportingEvent` 原生收纳主要语义对象。 |
| RE-02 多张表组成 shell | A | 多 outputs 和共享定义可由同一根对象表达。 |
| ORG-01 LOPA / LOPO | A | `ListOfContents` / `OrderedListItem` 支持层级、顺序、output/analysis 引用。 |
| ORG-02 output 与 display | A | `Output.displays` 是有序 display 列表；display 内部 layout 另议。 |
| ORG-03 output 文件信息 | A | `OutputFile` 支持 file type、location、style 占位等文件元信息。 |
| TXT-01 表标题 | A | `OutputDisplay.displayTitle` 可表达 display title。 |
| TXT-02 subtitle / population / footnote | A/B | `DisplaySection`、`DisplaySubSection` 可表达文本和顺序；样式需外部规则。 |
| TXT-03 行 label / 列 label | B | name/label 字段可承载语义 label，但不定义视觉位置。 |
| ANA-01 基础分析定义 | A | `Analysis` 覆盖 dataset、variable、method、analysis set、subset、grouping 等。 |
| ANA-02 多条 planned analysis | A/B | 多 analysis 可表达；排列成表格 row 需约定。 |
| ANA-03 display 由哪些 analysis 支撑 | B/C | 可通过目录或约定间接表达；`OutputDisplay` 无直接 analysis 绑定字段。 |
| SET-01 分析集 | A | `AnalysisSet` 继承 `WhereClause` 并可被 `Analysis` 引用。 |
| SET-02 数据子集 | A | `DataSubset` 继承 `WhereClause` 并可被 `Analysis` 引用。 |
| SET-03 复合筛选条件 | A/B | compound expression 支持嵌套与逻辑组合；人可读还原需渲染规则。 |
| GRP-01 treatment 分组 | A/B | `GroupingFactor` 与 `OrderedGroupingFactor` 可表达分组语义和顺序；列角色需约定。 |
| GRP-02 固定分组水平 | A/B | `Group` 可表达 label、where clause、order、level；作为列/行需 layout。 |
| GRP-03 多重分组 | B | 多 grouping 可排序；映射为 row/column/block 需外部规则。 |
| GRP-04 row grouping vs column grouping | C | ARS 无原生 row/column/page axis 字段。 |
| MTH-01 统计方法 | A | `AnalysisMethod.operations` 和 `Operation.order` 原生支持。 |
| MTH-02 operation 依赖 | B | 可表达 referenced operation relationship；角色标准化和执行解释需约定。 |
| MTH-03 常见统计量集合 | B | 可用 operations 表达；统计量术语和显示模式需约定。 |
| RES-01 operation result | A | `OperationResult` 支持 operationId、rawValue、formattedValue、resultGroups。 |
| RES-02 按 group 产生结果 | A/B | `ResultGroup` 支持 groupId 或 groupValue；多维 cell 定位需外部规则。 |
| RES-03 结果到 cell 绑定 | C | ARS 无明确 cell binding / 坐标 / 组合 cell 模型。 |
| ROW-01 简单行列表 | B | 可用 analysis、group、operation 和顺序字段约定生成语义 row。 |
| ROW-02 多层 row stub | C | 可间接表达部分层级；稳定 row stub 需要 row layout。 |
| ROW-03 派生行 | B | 可用独立 analysis + data subset 追踪条件；单 analysis 多派生 row 不自然。 |
| COL-01 简单列分组 | B/C | treatment 语义可表达；列物理结构需渲染约定。 |
| COL-02 多层列表头 | C/D | 语义层级可表达，header span/repeat 不是 ARS 原生能力。 |
| COL-03 统计量作为列 | B/C | operation 可表达统计量；是否成列和排序需 layout。 |
| RND-01 语义预览 | A/B | 标题、文本、grouping、method、result 语义足够。 |
| RND-02 可用 shell | B | 可在通用约定下生成基本 shell。 |
| RND-03 精确 sponsor shell | C/D | 精确版式、分页、复杂表头和样式需要外部模板。 |
| TPL-01 未填模板 | C/D | ARS 不是模板语言，不适合表达槽位状态。 |
| TPL-02 模板实例化最终状态 | A | 填充完成后应回到合法 `ReportingEvent`。 |
| REF-01 id 引用 | A | 主要对象引用字段完整。 |
| REF-02 display 到 analysis 直接引用 | B/C | 缺少 display-analysis 直接 binding。 |
| VAL-01 schema 结构校验 | A | JSON Schema / LinkML 可校验结构、类型、required、枚举。 |
| VAL-02 业务连通性校验 | B/C | 需额外 conformance rules 做跨对象引用和可渲染性检查。 |
| SPC-01 表格转置 | C | 语义不变时 ARS 无原生 axis 区分。 |
| SPC-02 多 denominator / N | B | 可追踪多个 denominator；显示和角色规则需约定。 |
| SPC-03 多层表头 | C/D | grouping 语义可表达，物理表头需 layout。 |
| SPC-04 方法落点 | B/C | 方法属于 `AnalysisMethod` / `Operation`；显示为 row/column/cell 需 layout。 |
| SPC-05 shift table | B/C | 二维分类语义可表达；矩阵布局、total、分母规则需约定。 |
| SPC-06 baseline change | A/B | 多变量和 subset 可表达；派生/配对/相邻展示需程序引用或 layout。 |
| SPC-07 SOC / PT | B/C | grouping 可表达 SOC/PT；层级 row stub、排序、术语版本需约定。 |
| SPC-08 PK 明细与汇总混合 | C | 多 analysis/method 可容纳；同 display 混合 block 需外部 block layout。 |
| SPC-09 数据驱动多个分组 | B/C | data-driven grouping 和 groupValue 可表达；排序/label/layout 需约定。 |
| SPC-10 方法冲突 | C | ARS 容纳多个 method，但不定义 cell 层冲突检测。 |
| SPC-11 Total / Missing / Unknown | B | 条件语义可表达；计算范围、denominator、展示位置需约定。 |
| SPC-12 左上角文本 | B/C | `Rowlabel Header` 可表达文本；更深 stub 语法和位置仍需 layout。 |
| SPC-13 多 result 组合 cell | C | 多 result 可保存；组合顺序、分隔符、换行和单元格归属需 `CellBinding`。 |
| SPC-14 display block / panel | C | ARS 可容纳多个 analysis 和文本；block 结构需 `DisplayLayout`。 |
| SPC-15 排序规则与派生排序 | B/C | 固定顺序可表达；结果驱动/术语驱动排序需外部规则。 |
| CASE-01 Demographics Summary | B | 核心语义可表达；真实 row/column shell 需 layout。 |
| CASE-02 Overall Summary of TEAE | B/C | 派生行和 denominator 可追踪；cell binding 和百分比规则需约定。 |
| CASE-03 Vital Signs by Visit | B/C | 多 grouping 和变量可表达；visit/parameter 轴向与重复 block 需 layout。 |
| CASE-04 Laboratory Shift Table | B/C | 交叉分类结果可表达；矩阵 layout 和分母规则需外部模型。 |
| CASE-05 Change from Baseline Summary | B | 多变量分析可表达；派生和相邻布局需约定。 |
| CASE-06 Adverse Events by SOC and PT | B/C | SOC/PT 与结果语义可表达；层级排序和组合 cell 需约定。 |
| CASE-07 PK Individual and Summary Table | C | 明细与汇总混合 display 需要 block/layout 模型。 |

## 一、ReportingEvent 作为根对象

### RE-01 能否表达一次完整 reporting event

- [x] 能否定义一个 reporting event 的唯一标识。
- [x] 能否定义 reporting event 的名称、版本、描述和 label。
- [x] 能否在同一个根对象下收纳 outputs、analyses、analysis sets、data subsets、groupings、methods。
- [x] 能否把同一张表所需的分析定义、展示定义和输出定义组织在同一实例中。

判定重点：

- 如果一张统计表所需的所有语义对象都能被同一个 `ReportingEvent` 收纳，能力等级倾向 A。
- 如果必须借助外部对象才能知道哪些 `Analysis` 属于哪个 `Output`，能力等级下降到 B 或 C。

### RE-02 能否表达多张表组成的一组 shell

- [x] 一个 `ReportingEvent` 是否可以包含多张 output。
- [x] 多张 output 是否可以共享同一批 analysis set、data subset、grouping factor 和 method。
- [x] 多张 output 是否可以共享 display subsection 或 global display section。
- [x] 能否通过 list of contents 表达表格顺序。

判定重点：

- 如果能表达一组 TFL，而不是只能表达单张表，能力等级倾向 A。

## 二、目录与输出组织

### ORG-01 能否表达 LOPA / LOPO 目录结构

- [x] 能否定义主目录。
- [x] 能否定义其他目录。
- [x] 目录项能否引用 `analysisId`。
- [x] 目录项能否引用 `outputId`。
- [x] 目录项能否形成多级嵌套结构。
- [x] 目录项能否保留顺序。

手工测试建议：

构造一个 `mainListOfContents`，包含 2 个 output，每个 output 下包含 2 到 3 个 analysis。

判定重点：

- 层级目录和顺序若能稳定表达，能力等级倾向 A。
- 如果要把目录当成表格 row 结构使用，应另行评估，不能直接等同。

### ORG-02 能否表达 output 与 display 的关系

- [x] 一个 `Output` 能否关联一个 `OutputDisplay`。
- [x] 一个 `Output` 能否关联多个 `OutputDisplay`。
- [x] 多个 display 是否能在 output 内排序。
- [x] display 是否能拥有独立标题。

判定重点：

- 对于一张统计表对应一个 display 的场景，能力等级倾向 A。
- 对于一个 output 包含多个复杂 display 的场景，需确认渲染器如何解释顺序。

### ORG-03 能否表达 output 文件信息

- [x] 能否表达输出文件类型。
- [x] 能否表达输出文件名称。
- [x] 能否表达输出文件路径或 location。
- [x] 能否表达输出文件 style。

判定重点：

- 文件元信息可以表达，能力等级倾向 A。
- 文件内部物理版式不应由 `OutputFile` 承担。

## 三、展示文本能力

### TXT-01 能否表达表标题

- [x] 能否表达 display title。
- [x] 能否区分 output name、output label 和 display title。
- [x] display title 是否能被渲染器稳定获取。

手工测试建议：

构造 `OutputDisplay.displayTitle = "Table 14.3.1 Overall Summary of TEAE"`。

判定重点：

- 简单标题能力等级倾向 A。
- 多行标题、带动态占位符标题、带样式标题可能需要额外约定。

### TXT-02 能否表达 subtitle、population line、footnote 等展示文本

- [x] 能否使用 `DisplaySection` 区分 title、footnote 等 section type。
- [x] 能否在同一 section 下放多个 `DisplaySubSection`。
- [x] 多个 subsection 是否能排序。
- [x] 能否复用 global display subsection。
- [x] 能否为每段文本定义 id。

判定重点：

- 如果只需表达文本内容和顺序，能力等级倾向 A。
- 如果需要表达字体、缩进、对齐、换行规则，能力等级下降到 C 或 D。

### TXT-03 能否表达行 label 或列 label

- [x] `Analysis.name` / `label` 是否足以作为行 label。
- [x] `Group.name` / `label` 是否足以作为分类行 label。
- [x] `Operation.name` / `label` 是否足以作为统计量 label。
- [x] `GroupingFactor.name` / `label` 是否足以作为列分组 label。

判定重点：

- ARS 有足够的 name/label 字段，但没有明确规定这些 label 在表格中的视觉位置。
- 若仅表达语义 label，能力等级倾向 B。
- 若要求精确行列位置，需要外部 layout。

## 四、分析对象能力

### ANA-01 能否表达一个基础分析定义

- [x] 能否表达分析 id。
- [x] 能否表达分析名称和 label。
- [x] 能否表达分析原因 `reason`。
- [x] 能否表达分析目的 `purpose`。
- [x] 能否表达分析数据集 `dataset`。
- [x] 能否表达分析变量 `variable`。
- [x] 能否引用分析方法 `methodId`。

手工测试建议：

构造一个分析：

```text
Analysis:
  dataset = ADAE
  variable = USUBJID
  methodId = MTH_COUNT_PCT
```

判定重点：

- 基础分析定义能力等级倾向 A。

### ANA-02 能否表达同一表中多条 planned analysis

- [x] 能否为每一行或每个统计目标定义独立 `Analysis`。
- [x] 多个 `Analysis` 是否可以共享 `AnalysisSet`。
- [x] 多个 `Analysis` 是否可以共享 `DataSubset`。
- [x] 多个 `Analysis` 是否可以共享 `AnalysisMethod`。
- [x] 多个 `Analysis` 是否可以在目录中按顺序组织。

判定重点：

- 多 analysis 的语义表达能力倾向 A。
- 但多个 `Analysis` 如何排列为表格 row，需要额外约定或 layout。

### ANA-03 能否表达一个 display 由哪些 analysis 支撑

- [x] 是否能通过 `ListOfContents` 间接表达 output 下的 analysis。
- [x] 是否能通过命名约定表达某些 analysis 属于某个 output。
- [x] 是否存在 ARS 原生字段直接把 `OutputDisplay` 与多个 `Analysis` 绑定。

判定重点：

- 如果只能通过目录或外部约定推断 display 与 analysis 的关系，能力等级倾向 B 或 C。
- 这是评估 ARS 能否直接作为渲染接口的关键检查项。

## 五、分析集和数据子集能力

### SET-01 能否表达分析集

- [x] 能否定义 `AnalysisSet`。
- [x] 能否表达分析集名称和 label。
- [x] 能否用 where clause 表达入组条件。
- [x] 能否在 `Analysis` 中引用 `analysisSetId`。

手工测试建议：

```text
AnalysisSet:
  id = SAF
  condition = ADSL.SAFFL EQ Y
```

判定重点：

- 简单和复合分析集定义能力等级倾向 A。

### SET-02 能否表达数据子集

- [x] 能否定义 `DataSubset`。
- [x] 能否表达数据子集名称和 label。
- [x] 能否用 where clause 表达筛选条件。
- [x] 能否在 `Analysis` 中引用 `dataSubsetId`。

手工测试建议：

```text
DataSubset:
  id = TEAE
  condition = ADAE.TRTEMFL EQ Y
```

判定重点：

- 数据子集能力等级倾向 A。

### SET-03 能否表达复合筛选条件

- [x] 能否表达 AND。
- [x] 能否表达 OR。
- [x] 能否表达 NOT。
- [x] 能否表达嵌套条件。
- [x] 能否表达引用已有 `AnalysisSet` / `DataSubset` / `Group`。
- [x] 能否保留条件顺序和层级。

手工测试建议：

```text
TRTEMFL = Y AND (AESER = Y OR AEOUT = FATAL)
```

判定重点：

- 若复合条件可以完整表达，能力等级倾向 A。
- 需额外检查渲染器能否把 where clause 还原为人可读文本。

## 六、分组能力

### GRP-01 能否表达按 treatment 分组

- [x] 能否定义 `GroupingFactor`。
- [x] 能否表达 grouping dataset。
- [x] 能否表达 grouping variable。
- [x] 能否表达 data-driven grouping。
- [x] 能否在 `Analysis` 中通过 `orderedGroupings` 引用分组。
- [x] 能否表达分组顺序。

手工测试建议：

```text
GroupingFactor:
  id = TRT
  groupingDataset = ADSL
  groupingVariable = TRT01A
```

判定重点：

- treatment 分组语义能力等级倾向 A。
- treatment 分组作为表格列结构时，通常需要渲染约定。

### GRP-02 能否表达固定分组水平

- [x] 能否定义多个 `Group`。
- [x] 每个 group 能否定义 label。
- [x] 每个 group 能否定义 where clause。
- [x] group 是否能排序。
- [x] group 是否能表达层级。

手工测试建议：

```text
Group:
  Placebo: ADSL.TRT01A EQ Placebo
  Active: ADSL.TRT01A EQ Active
```

判定重点：

- 固定分组语义能力等级倾向 A。
- 如果 group 要直接变成表格 column，需要 layout 或渲染约定。

### GRP-03 能否表达多重分组

- [x] 一个 `Analysis` 能否引用多个 `GroupingFactor`。
- [x] 多个 grouping 是否能排序。
- [x] 能否区分 first grouping、second grouping。
- [x] 能否表达 treatment + visit。
- [x] 能否表达 treatment + parameter + visit。

手工测试建议：

```text
orderedGroupings:
  1. TRT01A
  2. AVISIT
  3. PARAM
```

判定重点：

- 多重分组语义可以表达，能力等级倾向 A 或 B。
- 多重分组如何映射为 row block、column block 或 page block，需要外部规则。

### GRP-04 能否表达 row grouping 与 column grouping 的差异

- [x] ARS 是否原生标注某个 grouping 用于 row。
- [x] ARS 是否原生标注某个 grouping 用于 column。
- [x] ARS 是否原生标注某个 grouping 用于 page 或 block。

判定重点：

- 如果 ARS 只能表达 grouping 顺序，而不能表达 row/column 角色，能力等级倾向 C。
- 这通常是 `DisplayLayout` 的职责。

## 七、统计方法和操作能力

### MTH-01 能否表达统计方法

- [x] 能否定义 `AnalysisMethod`。
- [x] 能否表达方法名称、描述、label。
- [x] 能否定义一组 `Operation`。
- [x] operation 是否能排序。
- [x] operation 是否能有 result pattern。

手工测试建议：

```text
AnalysisMethod:
  id = COUNT_PCT
  operations:
    1. Count subjects
    2. Calculate percentage
```

判定重点：

- 方法和 operation 列表能力等级倾向 A。

### MTH-02 能否表达 operation 之间的依赖关系

- [x] 能否表达某个 operation 使用另一个 operation 的结果。
- [x] 能否标注引用结果的角色。
- [x] 能否引用同一 analysis 中的 operation。
- [x] 能否引用其他 analysis 中的 operation。

手工测试建议：

```text
Percentage operation uses denominator operation result.
```

判定重点：

- 如果依赖关系能被表达，能力等级倾向 B。
- 需要检查渲染器或计算引擎是否能无歧义解释这些关系。

### MTH-03 能否表达常见统计量集合

- [x] n。
- [x] n (%)。
- [x] mean。
- [x] standard deviation。
- [x] median。
- [x] min / max。
- [x] confidence interval。
- [x] p-value。

判定重点：

- ARS 可以用 `Operation` 表达统计操作，但标准化统计量语义可能依赖 operation 命名、result pattern 或术语约定。
- 能力等级通常为 B。

## 八、结果值和结果绑定能力

### RES-01 能否表达 operation result

- [x] 能否表达 operation id。
- [x] 能否表达 raw value。
- [x] 能否表达 formatted value。
- [x] 能否表达结果对应的 group。

手工测试建议：

```text
OperationResult:
  operationId = COUNT
  rawValue = 10
  formattedValue = 10 (25.0%)
```

判定重点：

- 单个结果值能力等级倾向 A。

### RES-02 能否表达按 group 产生的结果

- [x] 能否表达 treatment group 下的结果。
- [x] 能否表达 visit group 下的结果。
- [x] 能否表达 parameter group 下的结果。
- [x] 能否表达多个 group 组合下的结果。

手工测试建议：

```text
Result:
  treatment = Placebo
  visit = Week 4
  parameter = Systolic Blood Pressure
```

判定重点：

- 结果与 group 的语义绑定能力倾向 A 或 B。
- 多维 group 组合如何定位到表格 cell，需要进一步检查。

### RES-03 能否表达结果到单元格的绑定

- [x] ARS 是否能明确说明某个 result 应显示在第几行第几列。
- [x] ARS 是否能明确说明某个 operation result 对应哪个 cell。
- [x] ARS 是否能表达一个 cell 由多个 operation result 组合而成。
- [x] ARS 是否能表达 `n (%)` 这种组合展示。

判定重点：

- 如果需要外部规则才能把 result 放入 cell，能力等级倾向 C。
- 如果需要精确 shell 渲染，通常需要 `CellBinding` 或 `DisplayLayout`。

## 九、表格行结构能力

### ROW-01 能否表达简单行列表

- [x] 能否用多个 `Analysis` 表达多个 row。
- [x] 能否用多个 `Group` 表达分类变量的多个 row。
- [x] 能否通过 `ListOfContents` 表达 row 顺序。
- [x] 能否通过 `order` 字段表达 row 顺序。

判定重点：

- 语义 row 能力倾向 B。
- ARS 没有独立的 row model，因此渲染时需要约定 row 来源。

### ROW-02 能否表达多层 row stub

- [x] 能否表达 section row。
- [x] 能否表达 category row。
- [x] 能否表达 statistic row。
- [x] 能否表达 row 缩进层级。
- [x] 能否表达 row block。

手工测试建议：

```text
Age
  n
  Mean (SD)
  Median
Sex
  Male
  Female
```

判定重点：

- ARS 可用 `ListOfContents`、`Group.level`、`Operation.order` 等间接表达部分语义。
- 若要求稳定生成多层 row stub，能力等级通常为 C，需要 row layout。

### ROW-03 能否表达派生行

- [x] 能否表达 "Subjects with at least one TEAE"。
- [x] 能否表达 "Related TEAE"。
- [x] 能否表达 "Serious TEAE"。
- [x] 能否表达 "TEAE leading to death"。
- [x] 每个派生行是否可追溯到对应 where clause。

判定重点：

- 如果每个派生行建模为独立 `Analysis` + `DataSubset`，能力倾向 B。
- 如果希望一个 analysis 内直接表达多个派生 row，可能需要外部 row model。

## 十、表格列结构能力

### COL-01 能否表达简单列分组

- [x] 能否表达按 treatment 生成列。
- [x] 能否表达 treatment group 的顺序。
- [x] 能否表达 total 列。
- [x] 能否表达列 label。

判定重点：

- treatment 语义可由 `GroupingFactor` / `Group` 表达。
- 作为表格列的物理结构通常需要渲染约定，能力等级倾向 B 或 C。

### COL-02 能否表达多层列表头

- [x] 能否表达 treatment 下再分 visit。
- [x] 能否表达 visit 下再分 statistic。
- [x] 能否表达跨列 header。
- [x] 能否表达 column span。
- [x] 能否表达 header repeat。

判定重点：

- ARS 不适合表达物理多层列表头。
- 能力等级通常为 C 或 D。

### COL-03 能否表达统计量作为列

- [x] 能否表达 n、%、mean、SD 分别作为列。
- [x] 能否表达 n 和 % 合并为一列。
- [x] 能否表达一组 operation result 如何形成多列。

判定重点：

- 统计量本身可用 `Operation` 表达。
- 统计量是否成为列、以及如何排列，需要 layout 或渲染规则。

## 十一、表格 shell 渲染能力

### RND-01 给定 ReportingEvent 能否渲染语义预览

- [x] 能否渲染表标题。
- [x] 能否渲染 population。
- [x] 能否渲染 subset。
- [x] 能否渲染 grouping。
- [x] 能否渲染 analysis variable。
- [x] 能否渲染 method。
- [x] 能否渲染 footnote。

判定重点：

- 语义预览能力通常可达到 A 或 B。

### RND-02 给定 ReportingEvent 能否渲染可用 shell

- [x] 能否生成基本 row。
- [x] 能否生成基本 column。
- [x] 能否把 analysis 和 grouping 映射到 row/column。
- [x] 能否生成空白结果区域。
- [x] 能否保留标题和脚注。

判定重点：

- 若只追求可用 shell，能力可能为 B。
- 需要明确通用渲染规则。

### RND-03 给定 ReportingEvent 能否渲染精确 sponsor shell

- [x] 能否还原列宽。
- [x] 能否还原跨行跨列。
- [x] 能否还原缩进。
- [x] 能否还原分页。
- [x] 能否还原 RTF/PDF 样式。
- [x] 能否还原复杂表头。

判定重点：

- ARS 本身通常不足，能力等级倾向 C 或 D。
- 需要 `DisplayLayout` 或外部渲染模板。

## 十二、模板化能力

### TPL-01 能否表达未填模板

- [x] ARS 是否允许必要字段为空。
- [x] ARS 是否能区分字段为空是合法空值还是待填槽位。
- [x] ARS 是否能表达槽位类型。
- [x] ARS 是否能表达槽位必填性。
- [x] ARS 是否能表达槽位默认值。

判定重点：

- ARS 不是模板语言，该能力通常为 C 或 D。
- 应引入 `ReportingEventTemplate` 和 `TemplateSlot`。

### TPL-02 能否表达模板实例化后的最终状态

- [x] 模板填充完成后是否能生成合法 `ReportingEvent`。
- [x] 所有 required 字段是否都能被填入真实值。
- [x] 所有引用 id 是否能解析。
- [x] 所有 display、analysis、method、grouping 是否能连通。

判定重点：

- 最终状态应尽量回到 ARS 原生实例，能力倾向 A。
- 模板编辑过程不应强求完全符合 ARS 校验。

## 十三、引用完整性能力

### REF-01 能否通过 id 建立对象引用

- [x] `analysisSetId` 是否能引用 `AnalysisSet`。
- [x] `dataSubsetId` 是否能引用 `DataSubset`。
- [x] `groupingId` 是否能引用 `GroupingFactor`。
- [x] `methodId` 是否能引用 `AnalysisMethod`。
- [x] `operationId` 是否能引用 `Operation`。
- [x] `outputId` 是否能引用 `Output`。
- [x] `analysisId` 是否能引用 `Analysis`。

判定重点：

- 引用字段较完整，能力倾向 A。
- 需要验证工具层是否能做跨对象引用完整性校验。

### REF-02 能否表达 display 到 analysis 的直接引用

- [x] `OutputDisplay` 是否原生引用 `Analysis`。
- [x] `DisplaySection` 是否原生引用 `Analysis`。
- [x] `Output` 是否原生列出支撑它的 `Analysis`。

判定重点：

- 如果缺少直接引用，需要通过目录、命名或外部 binding 建立关系，能力倾向 B 或 C。
- 这是决定是否需要外部 `DisplayLayout` / `CellBinding` 的关键点。

## 十四、可验证性和校验能力

### VAL-01 能否用 schema 校验结构

- [x] 是否能使用 JSON Schema 校验基础结构。
- [x] 是否能使用 LinkML runtime 校验类型和 required 字段。
- [x] 是否能检查枚举值。
- [x] 是否能检查字段类型。

判定重点：

- 结构校验能力倾向 A。

### VAL-02 能否校验业务连通性

- [x] 是否能校验所有 id 引用存在。
- [x] 是否能校验每个 analysis 都有 method。
- [x] 是否能校验每个 output 至少有 display。
- [x] 是否能校验每个 display 是否可找到可渲染内容。
- [x] 是否能校验每个 result 是否能映射到 operation。

判定重点：

- ARS schema 不一定覆盖全部业务连通性。
- 需要额外 conformance rules 或应用层校验，能力倾向 B 或 C。

## 十五、专项能力边界问题

本节用于承接真实 TFL shell 中最容易误判的能力点。建议每个专项都构造一个最小 `ReportingEvent`，并明确区分：

- ARS 是否能表达统计语义。
- ARS 是否能表达 display 与 analysis/result 的绑定。
- ARS 是否能表达物理 row/column/cell layout。
- 是否必须引入项目约定、`DisplayLayout`、`CellBinding` 或渲染模板。

### SPC-01 能否区分同一分析语义下的表格转置

目标语义：

在 analysis、analysis set、data subset、grouping、method 和 result 不变的情况下，只改变表格视觉方向，例如 treatment 作为列 vs treatment 作为行，或 statistic 作为行 vs statistic 作为列。

检查：

- [x] 能否用同一组 `Analysis` / `GroupingFactor` / `OperationResult` 表达两张语义等价但转置的 display。
- [x] ARS 是否有原生字段标识某个 grouping 应落在 row axis。
- [x] ARS 是否有原生字段标识某个 grouping 应落在 column axis。
- [x] ARS 是否有原生字段标识某个 operation/statistic 应落在 row axis 或 column axis。
- [x] 两张转置表能否在不依赖外部 layout 的情况下被渲染器稳定区分。

手工测试建议：

构造一张 treatment by statistic 表，再构造它的转置版本。两者共享同一批 `Analysis.results`，只改变 display 期望。

判定重点：

- ARS 能表达分组和结果语义，但通常不能原生表达视觉轴向。
- 如果转置只存在于渲染规则中，能力等级倾向 C。
- 这是判断是否需要 `DisplayLayout.axis` 或类似外部结构的关键用例。

### SPC-02 能否支持同一列下多个 denominator / N

目标语义：

同一 treatment 列中，不同行或不同分析使用不同总人数，例如 safety population N、某访视有观测值的 N、某检查项非缺失 N、AE subject denominator。

检查：

- [x] 能否为不同 `Analysis` 指定不同 `AnalysisSet`。
- [x] 能否为不同 row 指定不同 `DataSubset`。
- [x] 能否把 denominator 建模为独立 `Operation`。
- [x] percentage operation 是否能通过 `referencedOperationRelationships` 或 `referencedAnalysisOperations` 指向对应 denominator。
- [x] display 是否能标明列头中的 N 与单元格百分比分母不是同一个 N。
- [x] 同一列多个 N 是否能被结果级别追踪，而不是只作为列头文本。

手工测试建议：

构造一张 AE summary：列头显示 Safety N；某些行的百分比分母使用 TEAE evaluable N；另一些行使用 exposed subjects N。

判定重点：

- ARS 可表达多个 analysis set、subset、operation 和 operation result，语义能力倾向 B。
- denominator 的角色标准化、列头 N 与行内 N 的显示规则通常需要项目约定。
- 如果要求渲染器自动判断每个百分比使用哪个 N，需额外 conformance rule。

### SPC-03 能否表达多层表头

目标语义：

表头包含多个层级，例如 treatment 下分 visit，visit 下分 statistic，或 dose group 下分 parameter / unit。

检查：

- [x] 能否用多个 `GroupingFactor` 表达表头对应的语义层级。
- [x] 能否通过 `OrderedGroupingFactor.order` 表达分组顺序。
- [x] 能否表达 header span。
- [x] 能否表达 header repeat。
- [x] 能否表达空表头单元格、跨列标题和单位行。
- [x] 能否区分“语义层级”与“物理表头层级”。

判定重点：

- 分组语义通常可由 `GroupingFactor` / `Group` 表达。
- 物理多层表头、span、repeat 通常不是 ARS 原生能力，能力等级倾向 C 或 D。

### SPC-04 分析方法应落在 row、column 还是 analysis 本身

目标语义：

某些表中 row 和 column 都是临床分类值，例如 post-baseline worst result by baseline result，单元格中显示 count/percentage。需要判断 count/percentage 这种统计方法属于 row、column、cell，还是属于支撑该 display 的 `AnalysisMethod`。

检查：

- [x] 能否把 clinical categories 建模为 row/column grouping。
- [x] 能否把 count/percentage 建模为 `AnalysisMethod.operations`。
- [x] 能否表达 operation 与 row grouping、column grouping 的关系。
- [x] 当 row 和 column 都是 normal/abnormal 这类分类值时，是否仍能无歧义定位统计方法。
- [x] 是否存在把 statistic 当 row label 或 column label 的外部约定。

手工测试建议：

构造 baseline status x worst post-baseline status 的二维频数表，统计量为 n 和 percentage。

判定重点：

- ARS 中统计方法更自然地落在 `AnalysisMethod` / `Operation` 上，而不是物理 row 或 column 上。
- 统计方法是否显示为行、列或 cell 内组合，通常属于 layout / rendering 决策。

### SPC-05 能否支持 shift table

目标语义：

表达从 baseline category 到 post-baseline category 的转移矩阵，例如 normal / abnormal / missing 的基线状态与治疗后最差状态交叉。

检查：

- [x] 能否表达 baseline category grouping。
- [x] 能否表达 post-baseline 或 worst post-baseline category grouping。
- [x] 能否表达两个分类维度组合下的结果。
- [x] 能否表达 row total 和 column total。
- [x] 能否表达 missing / not done / no post-baseline assessment。
- [x] 能否表达百分比的 denominator 是 row total、column total 还是 overall N。
- [x] 能否表达 shift table 的二维矩阵 layout。

手工测试建议：

构造 lab shift table：Baseline Normal/Abnormal/Missing by Worst Post-baseline Normal/Abnormal/Missing，单元格为 n (%)。

判定重点：

- 二维分类结果语义可以通过多个 grouping 和 `ResultGroup` 表达，能力倾向 B。
- 矩阵轴向、total 边栏、百分比分母规则、missing 展示通常需要外部约定。

### SPC-06 能否支持基于基线变化的 table

目标语义：

同一参数/访视下同时展示 observed value、baseline、change from baseline、percent change from baseline，或基于 baseline change 的分类频数。

检查：

- [x] 能否用不同 `Analysis.variable` 表达 `AVAL`、`BASE`、`CHG`、`PCHG`。
- [x] 能否用 `DataSubset` 表达 baseline、post-baseline、on-treatment 等记录范围。
- [x] 能否表达 change from baseline 的派生定义或程序引用。
- [x] 能否表达 observed value 与 change from baseline 共用同一 parameter / visit / treatment 分组。
- [x] 能否表达基于 change 阈值的分类行。
- [x] 能否表达 baseline 与 post-baseline 的配对关系。

判定重点：

- `Analysis` 可表达不同分析变量和方法，能力倾向 A/B。
- change 的派生算法、配对规则和 shell 中 observed/change 的相邻布局通常需要程序引用或 layout 约定。

### SPC-07 能否支持 SOC / PT 统计类 table

目标语义：

AE 表按 SOC、PT 层级展开，常见单元格为 subjects n (%)、events E，可能包含总体行、SOC 行、PT 行、严重程度或相关性子层级。

检查：

- [x] 能否表达 SOC grouping。
- [x] 能否表达 PT grouping。
- [x] 能否表达 SOC > PT 的层级顺序。
- [x] 能否表达 subject count 与 event count 两类结果。
- [x] 能否表达同一 PT 下按 severity / relatedness 分层。
- [x] 能否表达排序规则，例如按 SOC 字母序、PT 频数降序。
- [x] 能否表达 MedDRA 版本或外部术语引用。

手工测试建议：

构造 TEAE by SOC and PT 表，列为 treatment，单元格为 `subjects n (%)` 和 `events E`。

判定重点：

- SOC/PT 可作为 grouping 或 data-driven group 语义表达，能力倾向 B。
- 层级 row stub、排序规则、术语版本和 n/E 组合展示通常需要项目约定。

### SPC-08 能否支持 PK 参数混合结构 table

目标语义：

同一 display 上半部分展示每个参与者在每个时间点或参数下的结果，下半部分展示每个时间点或参数的描述性统计。

检查：

- [x] 能否在同一 `OutputDisplay` 下表达 individual listing-like rows 与 summary rows。
- [x] 能否区分 participant-level results 与 aggregate results。
- [x] 能否表达 subject、time point、parameter、treatment 的多重分组。
- [x] 能否表达上下两个 panel 或 block。
- [x] 能否表达上半部分和下半部分使用不同 `AnalysisMethod`。
- [x] 能否表达 summary block 的 n、mean、SD、CV%、median、min/max。

手工测试建议：

构造 PK concentration by time 表：上半部分每个 subject 一行；下半部分按 time point 汇总 descriptive statistics。

判定重点：

- ARS 可表达多个 analysis 和多个 method，但“同一 display 内两个不同 row block”的 layout 需要外部规则。
- listing-like 明细值与 aggregate summary 的混合边界需要重点验证。

### SPC-09 能否支持数据驱动的多个分组

目标语义：

分组水平来自数据本身，例如不同 visit、不同 parameter、不同 category 下重复生成相同分析结构，而不是预先列死所有 group。

检查：

- [x] `GroupingFactor.dataDriven = true` 是否足以表达数据驱动分组。
- [x] `ResultGroup.groupValue` 是否能承载数据驱动分组值。
- [x] 能否同时使用多个 data-driven grouping。
- [x] 能否表达 data-driven group 的排序规则。
- [x] 能否表达 data-driven group 的显示 label 与原始值不同。
- [x] 能否表达某些数据值应被排除、合并或重命名。

判定重点：

- 数据驱动分组是 ARS 明确支持的语义，但排序、label 标准化和展示规则通常需要项目约定。
- 多个 data-driven grouping 映射为 row/column/block 时仍需要 layout。

### SPC-10 行/列统计分析方法冲突时如何处理

目标语义：

某个 display 的 row 期望一种统计方法，column 期望另一种统计方法，或同一 cell 被多个不同 analysis/method 声称生成。

检查：

- [x] ARS 是否允许同一 display 中多个 `Analysis` 使用不同 `methodId`。
- [x] 能否表达某个 result 来自哪个 `Analysis` 和哪个 `Operation`。
- [x] 能否检测同一 cell 绑定多个不兼容 result。
- [x] 能否检测 row-level method 与 column-level method 的冲突。
- [x] 能否表达冲突优先级或覆盖规则。

判定重点：

- ARS 可以容纳多个 method，但不一定定义 display/cell 层面的冲突检测。
- 冲突处理更像应用层 conformance rule 和 `CellBinding` 职责，能力等级倾向 C。

### SPC-11 能否支持汇总、缺失、未分类等特殊分组

目标语义：

表格中存在 Total、Overall、Missing、Unknown、Not applicable、Not done、Unclassified 等特殊行/列/分组。

检查：

- [x] 能否把 Total / Overall 表达为预定义 `Group`。
- [x] 能否把 Missing / Unknown 表达为预定义 `Group` 或 `DataSubset`。
- [x] 能否表达特殊分组与普通数据值分组的排序关系。
- [x] 能否表达 Total 是跨哪些 group 汇总得到的。
- [x] 能否表达 Missing 是否纳入 denominator。
- [x] 能否表达缺失分组显示在 row、column 还是 footnote 中。

判定重点：

- 特殊分组的条件语义可表达，能力倾向 B。
- total 的计算范围、缺失纳入规则和展示位置需要项目约定。

### SPC-12 表格左上角文本是否有语法意义

目标语义：

表格左上角常出现 row stub header，例如 `Parameter`、`Visit`、`System Organ Class Preferred Term`，也可能为空。需要判断它只是视觉文本，还是承载 row axis / stub / hierarchy 的语法意义。

检查：

- [x] 能否用 `GroupingFactor.label` 表达 row stub header。
- [x] 能否用 `Analysis.label` 或 `Operation.label` 表达 row stub header。
- [x] 能否用 `DisplaySection` 表达左上角文本。
- [x] ARS 是否能标明该文本位于左上角 stub header cell。
- [x] 左上角文本是否影响 row grouping 的解释。

判定重点：

- 文本本身可用 label 或 display text 表达。
- “左上角”位置和 stub header 语法通常不是 ARS 原生能力，能力等级倾向 C。

### SPC-13 能否支持同一结果单元格多个分析结果

目标语义：

一个 cell 中展示多个 operation result 或多个 analysis result，例如 AE 表常见 `subjects n (%) events E`，或 `mean (SD)`、`median [Q1, Q3]`。

检查：

- [x] 能否为同一 group 组合保存多个 `OperationResult`。
- [x] 能否区分 subject count、percentage、event count。
- [x] 能否用 `Operation.resultPattern` 或 `OperationResult.formattedValue` 表达组合展示。
- [x] 能否表达多个 result 在同一 cell 中的顺序。
- [x] 能否表达多个 result 之间的分隔符、括号和换行规则。
- [x] 能否区分“多个 result 组合成一个 cell”与“多个 statistic column”。

手工测试建议：

构造 AE SOC/PT 表，单元格同时展示 `n (%)` 和 `E`；再构造 descriptive statistics 表，比较 `Mean (SD)` 作为一个 cell 和 Mean、SD 作为两列的差异。

判定重点：

- 多个 `OperationResult` 可表达，但 cell 组合规则通常不是 ARS 原生能力。
- 如果只把组合后的字符串放入 `formattedValue`，可渲染但会损失结构化结果的可复用性。

### SPC-14 能否表达 display block / panel 结构

目标语义：

一个 display 内存在多个逻辑区块，例如 overall summary block、by-visit block、individual rows block、descriptive statistics block。

检查：

- [x] 能否表达多个 analysis block 属于同一个 display。
- [x] 能否表达 block 顺序。
- [x] 能否表达 block header。
- [x] 能否表达不同 block 使用不同 row/column 规则。
- [x] 能否表达 block 间共享或不共享 column header。

判定重点：

- ARS 可容纳多个 analysis 和 display section 文本。
- display 内部 block layout 通常需要外部 `DisplayLayout`。

### SPC-15 能否表达排序规则与派生排序

目标语义：

行或列不只是按原始值排序，而是按频数、临床顺序、字典顺序、访视计划顺序、SOC/PT 层级或 sponsor-defined order 排序。

检查：

- [x] 预定义 `Group.order` 是否能表达固定顺序。
- [x] data-driven group 是否能表达排序依据。
- [x] 能否表达按某列结果值降序排序。
- [x] 能否表达 tie-breaker。
- [x] 能否表达排序规则来自外部术语、SAP 或程序。

判定重点：

- 固定分组顺序可表达，能力倾向 A/B。
- 基于结果值或外部术语的派生排序通常需要项目约定或程序引用。

## 十六、典型表格测试用例

以下测试用例只使用手工赋值，不依赖建库文件。

### CASE-01 Demographics Summary

目标：

评估 ARS 是否能表达人口学表的核心语义。

手工输入：

```text
AnalysisSet:
  SAF = ADSL.SAFFL EQ Y

GroupingFactor:
  TRT = ADSL.TRT01A

Analyses:
  Age summary = ADSL.AGE with method descriptive statistics
  Sex summary = ADSL.SEX with method count percentage
  Race summary = ADSL.RACE with method count percentage

OutputDisplay:
  title = Summary of Demographics and Baseline Characteristics
```

检查：

- [x] 能否表达连续变量 age 的统计量。
- [x] 能否表达分类变量 sex/race 的水平。
- [x] 能否表达 treatment 分列语义。
- [x] 能否表达 n、mean、SD、median、min、max。
- [x] 能否表达 n (%)。
- [x] 能否表达 row 顺序。
- [x] 能否表达列顺序。
- [x] 能否渲染语义预览。
- [x] 能否渲染接近真实 shell 的行列结构。

重点边界：

- 连续变量统计量行是否需要 row layout。
- 分类变量水平是否用 `Group` 表达足够自然。
- treatment 作为列是否需要 `DisplayLayout`。

### CASE-02 Overall Summary of TEAE

目标：

评估 ARS 是否能表达安全性 AE 总结表的核心语义。

手工输入：

```text
AnalysisSet:
  SAF = ADSL.SAFFL EQ Y

GroupingFactor:
  TRT = ADSL.TRT01A

DataSubsets:
  TEAE = ADAE.TRTEMFL EQ Y
  Related TEAE = ADAE.TRTEMFL EQ Y AND ADAE.AEREL IN (RELATED, POSSIBLY RELATED)
  Serious TEAE = ADAE.TRTEMFL EQ Y AND ADAE.AESER EQ Y
  Fatal TEAE = ADAE.TRTEMFL EQ Y AND ADAE.AEOUT EQ FATAL

Method:
  count subjects and percentage
```

检查：

- [x] 能否表达每个 AE 派生 row 的条件。
- [x] 能否表达每个 row 对应的 analysis。
- [x] 能否表达 treatment 分组。
- [x] 能否表达 denominator。
- [x] 能否表达 count。
- [x] 能否表达 percentage。
- [x] 能否表达 count 和 percentage 的关系。
- [x] 能否表达行顺序。
- [x] 能否表达最终结果如何填入 cell。

重点边界：

- 每个 row 用独立 `Analysis` 还是一个 `Analysis` 下多个 subset。
- 百分比 denominator 如何标准化表达。
- result 到 cell 的映射是否需要 `CellBinding`。

### CASE-03 Vital Signs by Visit

目标：

评估 ARS 是否能表达 visit、parameter、baseline/change 等复杂结构。

手工输入：

```text
AnalysisSet:
  SAF = ADSL.SAFFL EQ Y

Dataset:
  ADVS

Variables:
  AVAL
  CHG

GroupingFactors:
  TRT = ADSL.TRT01A
  PARAM = ADVS.PARAM
  VISIT = ADVS.AVISIT

Methods:
  descriptive statistics
```

检查：

- [x] 能否表达 parameter 分组。
- [x] 能否表达 visit 分组。
- [x] 能否表达 treatment 分组。
- [x] 能否表达 observed value。
- [x] 能否表达 change from baseline。
- [x] 能否表达每个 parameter / visit / treatment 组合下的结果。
- [x] 能否表达多层 row。
- [x] 能否表达多层 column。
- [x] 能否表达重复 block。
- [x] 能否渲染可用 shell。

重点边界：

- visit 应作为 row、column 还是 block，ARS 是否能直接说明。
- 多层表头通常是否必须由 `DisplayLayout` 表达。
- observed 与 change 是否建模为两个 `Analysis` 更自然。

### CASE-04 Laboratory Shift Table

目标：

评估 ARS 是否能表达 baseline 到 post-baseline / worst post-baseline 的二维转移矩阵。

手工输入：

```text
AnalysisSet:
  SAF = ADSL.SAFFL EQ Y

Dataset:
  ADLB

GroupingFactors:
  TRT = ADSL.TRT01A
  BASECAT = ADLB.BNRIND
  WORSTCAT = derived worst post-baseline ANRIND

Methods:
  count subjects
  calculate percentage
```

检查：

- [x] 能否表达 baseline category。
- [x] 能否表达 worst post-baseline category。
- [x] 能否表达 baseline category x worst category 的结果组合。
- [x] 能否表达 treatment 分组。
- [x] 能否表达 row total / column total / overall total。
- [x] 能否表达 missing baseline 或 missing post-baseline。
- [x] 能否表达百分比分母规则。
- [x] 能否渲染二维 shift matrix。

重点边界：

- 两个分类 grouping 的语义可表达，但哪个放 row、哪个放 column 需要 layout。
- total 和 missing 的统计口径需要明确约定。

### CASE-05 Change from Baseline Summary

目标：

评估 ARS 是否能表达 observed value、baseline、change from baseline 和 percent change from baseline 的组合展示。

手工输入：

```text
AnalysisSet:
  SAF = ADSL.SAFFL EQ Y

Dataset:
  ADVS

Variables:
  AVAL
  BASE
  CHG
  PCHG

GroupingFactors:
  TRT = ADSL.TRT01A
  PARAM = ADVS.PARAM
  VISIT = ADVS.AVISIT

Methods:
  descriptive statistics
```

检查：

- [x] 能否表达 baseline value。
- [x] 能否表达 observed value。
- [x] 能否表达 change from baseline。
- [x] 能否表达 percent change from baseline。
- [x] 能否表达同一 parameter / visit 下多个分析变量。
- [x] 能否表达变量派生规则或程序引用。
- [x] 能否表达 observed/change 的相邻 row 或 column layout。

重点边界：

- 多个分析变量可用多个 `Analysis` 表达。
- 变量之间的配对关系和展示相邻性需要额外规则。

### CASE-06 Adverse Events by SOC and PT

目标：

评估 ARS 是否能表达 SOC/PT 层级、安全性计数、事件计数和组合单元格。

手工输入：

```text
AnalysisSet:
  SAF = ADSL.SAFFL EQ Y

DataSubset:
  TEAE = ADAE.TRTEMFL EQ Y

GroupingFactors:
  TRT = ADSL.TRT01A
  SOC = ADAE.AEBODSYS
  PT = ADAE.AEDECOD

Methods:
  count subjects
  calculate percentage
  count events
```

检查：

- [x] 能否表达 SOC 层级。
- [x] 能否表达 PT 层级。
- [x] 能否表达 treatment 分列。
- [x] 能否表达 subjects n (%)。
- [x] 能否表达 events E。
- [x] 能否表达 `n (%) E` 或分行展示。
- [x] 能否表达 SOC/PT 排序规则。
- [x] 能否表达 MedDRA version。

重点边界：

- SOC/PT 的 row stub 层级和排序通常需要外部约定。
- subject count、percentage、event count 的组合 cell 需要 `CellBinding` 或 result pattern 约定。

### CASE-07 PK Individual and Summary Table

目标：

评估 ARS 是否能表达同一 display 内 participant-level 数据和 aggregate descriptive statistics 的混合结构。

手工输入：

```text
AnalysisSet:
  PK = ADSL.PKFL EQ Y

Dataset:
  ADPC or ADPP

GroupingFactors:
  TRT = ADSL.TRT01A
  SUBJECT = ADPC.USUBJID
  TIME = ADPC.ATPT
  PARAM = ADPC.PARAM

Methods:
  individual value
  descriptive statistics
```

检查：

- [x] 能否表达每个 subject / time / parameter 的 individual value。
- [x] 能否表达每个 time / parameter 的 descriptive statistics。
- [x] 能否表达同一 display 的上、下两个 block。
- [x] 能否表达两个 block 使用不同 method。
- [x] 能否表达 subject rows 与 summary rows 的排序关系。
- [x] 能否表达不同 block 是否共享 column header。

重点边界：

- ARS 可表达多组 analyses，但混合 listing-summary layout 需要外部 display block 规则。

## 最终判定表

完成上述检查后，汇总判定如下：

| 能力区域 | 等级 | 主要依据 | 是否需要外部模型 |
| --- | --- | --- | --- |
| ReportingEvent 根容器 | A | `ReportingEvent` 直接收纳目录、outputs、analyses、analysis sets、data subsets、groupings、methods、global display sections。 | 否 |
| Output 组织 | A | `Output.displays` 是 `OrderedDisplay` 列表，可表达一个 output 下一个或多个 display 及顺序。 | 否，复杂 display 内部结构除外 |
| LOPA / LOPO | A | `ListOfContents.contentsList`、`OrderedListItem.analysisId` / `outputId` / `sublist` 支持目录层级和顺序。 | 否 |
| Display title / footnote | A/B | `OutputDisplay.displayTitle`、`DisplaySection.sectionType`、`DisplaySubSection.text` 支持标题和展示文本。 | 样式、缩进、分页需要 |
| Analysis 定义 | A | `Analysis` 覆盖 reason、purpose、dataset、variable、analysisSetId、dataSubsetId、orderedGroupings、methodId、results。 | 否 |
| AnalysisSet / DataSubset | A | 两者继承 `WhereClause`，可被 `Analysis` 引用。 | 否，复杂业务口径说明除外 |
| Where clause | A/B | 简单 condition 与 compound expression 支持逻辑组合和嵌套。 | 人可读表达、复杂业务口径需要 |
| GroupingFactor / Group | A/B | grouping dataset/variable、dataDriven、groups、group condition/order/level 可表达。 | 作为行列轴和展示排序时需要 |
| AnalysisMethod / Operation | A/B | method 包含有序 operations；operation 可有 resultPattern 和 referenced operation relationships。 | 统计术语、执行解释、格式规则需要 |
| OperationResult | A | operationId、rawValue、formattedValue、resultGroups 可表达单个结果和分组归属。 | 否，cell 映射除外 |
| Row 结构 | B/C | 可由 analysis、group、operation 和 order 约定生成语义行。 | 是，需要 row layout 规则 |
| Column 结构 | B/C | treatment/visit/statistic 等语义可由 grouping/operation 表达。 | 是，需要 column layout 规则 |
| Cell binding | C | ARS 无 cell 坐标、cell 组合、cell 冲突模型。 | 是，需要 `CellBinding` |
| 表格转置识别 | C | ARS 无 row/column axis 字段，无法仅凭语义区分转置 display。 | 是，需要 `DisplayLayout.axis` 类规则 |
| 多 denominator / N | B | 可用不同 analysis set/subset 和 referenced operations 追踪。 | 角色标准化、列头 N 与百分比分母展示需要 |
| 多层表头 | C/D | grouping 语义可表达，header span/repeat/空 header cell 不原生。 | 是 |
| Shift table | B/C | baseline 和 post-baseline grouping 及结果组合可表达。 | 矩阵轴向、total、missing、分母规则需要 |
| Baseline / change from baseline | A/B | 可用不同 `Analysis.variable` 表达 AVAL/BASE/CHG/PCHG。 | 派生算法、配对规则、相邻布局需要 |
| SOC / PT 层级统计 | B/C | SOC/PT 可作为 grouping 或 data-driven group；结果可区分 subject/event operations。 | 层级 row stub、排序、术语版本、组合 cell 需要 |
| PK 明细与汇总混合结构 | C | 可容纳多 analysis/method，但同 display 内 listing-summary block 不原生。 | 是，需要 block/panel layout |
| 数据驱动多重分组 | B/C | `GroupingFactor.dataDriven` 和 `ResultGroup.groupValue` 支持数据驱动值。 | 排序、label 标准化、行列/block 映射需要 |
| Total / Missing / Unknown 分组 | B | 可建模为 `Group` 或 `DataSubset` 条件。 | 汇总范围、denominator、展示位置需要 |
| 多 result 组合 cell | C | 多 `OperationResult` 可存在，但组合成一个 cell 的顺序、分隔符、换行不原生。 | 是，需要 `CellBinding` 或强约定 |
| Display block / panel | C | ARS 可容纳多个 display sections 和 analyses，但无 display 内 block 模型。 | 是，需要 `DisplayLayout` |
| 排序规则与派生排序 | B/C | 固定 `order` 可表达；按频数、术语、结果值等派生排序需规则。 | 是，复杂排序需要 |
| 精确 shell layout | C/D | ARS 不是物理渲染模型。 | 是，需要模板或渲染模型 |
| 模板槽位 | C/D | ARS 没有未填槽位、槽位类型、槽位必填性模型。 | 是，需要 `ReportingEventTemplate` / `TemplateSlot` |
| 最终实例校验 | A/B | 合法 `ReportingEvent` 可用 schema / LinkML 做结构校验。 | 业务连通性需要 conformance rules |

## 已完成结论

ARS 可作为底层接口原生承载：

- reporting event 的根对象和多 output 组织。
- LOPA / LOPO 目录层级、顺序、output 和 analysis 引用。
- output 文件元信息和 display 列表。
- display title、title、row label header、footnote、legend、abbreviation、footer 等展示文本。
- planned analysis 的 dataset、variable、reason、purpose、analysis set、data subset、grouping、method、result。
- analysis set、data subset、group、where clause 的简单和复合筛选条件。
- grouping factor、固定 group、数据驱动 group value。
- analysis method、operation、operation result、result group。
- SAP、CSR、程序等外部资料引用。

ARS 可承载但需要约定：

- 多个 `Analysis` 如何排列成表格行。
- `GroupingFactor` 如何映射为 row、column、page 或 block。
- 常见统计量名称、角色、展示 label 和 result pattern。
- percentage denominator 的角色、来源和列头 N 的关系。
- total、overall、missing、unknown、not done 等特殊分组的计算口径。
- data-driven group 的排序、重命名、排除、合并规则。
- schema 之外的业务连通性校验，例如 output/display/analysis 是否能形成可渲染闭环。

ARS 只能部分承载，需要外部模型：

- display 到 analysis/result 的直接绑定。
- row model、column model、axis、block/panel 和多层 row stub。
- result 到 cell 的绑定，以及一个 cell 中多个 result 的组合规则。
- 表格转置的稳定区分。
- shift table 的二维矩阵 layout、row/column total 和 denominator 规则。
- SOC/PT 层级 row stub、排序和组合 cell。
- PK individual rows 与 summary rows 混合 display。
- 派生排序、结果驱动排序和 sponsor-defined order。

ARS 不适合承载：

- 精确 sponsor shell 的列宽、缩进、合并单元格、分页、RTF/PDF 样式。
- 完整物理渲染规范。
- 未填模板状态、槽位类型、槽位默认值和槽位必填性。
- 前端拖拽编辑交互状态。
- EDC 建库解析或 EDC 到 ADaM 映射。

下一步必须设计的应用层对象：

- `DisplayLayout`：表达 display 内 row/column/page/block/panel 轴向、表头层级、span、排序、分页和样式桥接。
- `CellBinding`：表达 operation result 到 cell 的绑定、多个 result 的组合顺序、分隔符、换行和冲突检测。
- `ReportingEventTemplate`：表达未填模板、模板元数据和实例化过程。
- `TemplateSlot`：表达槽位类型、必填性、默认值、候选来源和填充状态。
- `ConformanceRule`：表达跨对象引用完整性、display-analysis 连通性、denominator 规则、layout 可渲染性和冲突检测。

## 更新后的工作假设

- `ReportingEvent` 是最终统计语义实例的合适落点。
- ARS 的强项是 planned analysis 语义、结果追踪和输出组织，不是像素级或 RTF/PDF 级 shell 布局。
- `OutputDisplay`、`DisplaySection`、`DisplaySubSection` 可以表达展示文本和部分 row label header 文本，但不是完整 layout 模型。
- `OperationResult` 可以表达结果值和分组归属，但不等同于 cell binding。
- 对于“可用 shell”，可以用项目约定从 ARS 生成基本行列结构。
- 对于“精确 sponsor shell”，必须引入外部 `DisplayLayout` / `CellBinding` / 渲染模板。
- 模板编辑过程不应强求是合法 ARS；模板填充完成后应生成合法 `ReportingEvent`。
