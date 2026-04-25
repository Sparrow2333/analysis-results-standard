# 能力边界摘要

本文件是 `notes/ars-capability-checklist.md` 的压缩入口。它帮助 AI 在回答“ARS 能不能表达某种 TFL 语义”时先做边界判断。完整结论以检查单中的“检查项判定索引”“最终判定表”和“已完成结论”为准。

## 总原则

ARS 更擅长表达：

- reporting event 的组织结构。
- planned analyses 的定义。
- output/display 的逻辑组织。
- 分析集、数据子集、分组和筛选条件。
- 方法、统计操作、结果值和结果分组。
- SAP、CSR、程序等外部资料的追踪引用。

ARS 不应被默认当作：

- 精确视觉布局模型。
- PDF/RTF/HTML 渲染规范。
- 完整统计计算引擎。
- EDC 到 ADaM 的转换模型。
- 前端拖拽建表交互模型。

## 能力等级

沿用现有检查单中的四级判断：

| 等级 | 含义 |
| --- | --- |
| A | ARS 原生支持，字段和关系语义清晰，无需额外模型 |
| B | ARS 可表达，但需要项目约定或渲染器解释规则 |
| C | ARS 只能表达部分语义，需要外部补充模型 |
| D | ARS 不适合表达，应放在外部模型 |

## 完成版边界判断

| 语义 | 倾向等级 | 判断要点 |
| --- | --- | --- |
| 一个 reporting event 收纳多张 outputs 和 analyses | A | `ReportingEvent` 是根对象并直接收纳相关集合 |
| LOPA/LOPO 目录层级和顺序 | A | `ListOfContents` / `OrderedListItem` 支持层级、顺序、output/analysis 引用 |
| output 文件元信息 | A | `Output.fileSpecifications` 和 `OutputFile` 负责文件信息 |
| display title、title、footnote 等文本 | A/B | 文本和顺序可表达；复杂样式和布局需要约定 |
| planned analysis 定义 | A | `Analysis` 覆盖变量、方法、分析集、数据子集、分组、结果等 |
| 分析集、数据子集、分组条件 | A/B | `WhereClause` 能表达简单条件和复合表达式；复杂业务规则需检查 |
| 统计操作和结果值 | A/B | `AnalysisMethod` / `Operation` / `OperationResult` 能表达操作和值；计算过程和格式解释可能需约定 |
| display 到 analysis/result 绑定 | B/C | 可经目录或约定间接推断；`OutputDisplay` 无直接 analysis/result binding 字段 |
| 表格行列视觉位置 | C/D | ARS 有语义 label 和分组信息，但不天然等于精确 layout |
| result 到 cell 绑定和组合 cell | C | ARS 能保存多个 result，但不定义 cell 坐标、组合顺序、分隔符或冲突检测 |
| 转置、shift matrix、block/panel、PK 混合结构 | C | 统计语义通常能部分表达，稳定 shell layout 需要 `DisplayLayout` / `CellBinding` |
| 未填模板和槽位状态 | C/D | ARS 最适合作为填充完成后的 `ReportingEvent`，不是模板编辑语言 |
| 字体、缩进、合并单元格、分页 | D | 应由外部渲染模型或输出模板处理 |

## 判断流程

遇到“ARS 能否表达 X”时：

1. 判断 X 是语义、追踪、组织，还是视觉布局。
2. 如果是语义，找对应对象：analysis、output、display、grouping、where clause、operation、result。
3. 如果是顺序或层级，优先看 `order`、`level`、`Ordered*` 类和 `ListOfContents`。
4. 如果是视觉布局，默认不要判定为 ARS 原生能力，除非能找到明确字段或项目约定。
5. 需要更细结论时，回到 `notes/ars-capability-checklist.md` 的“检查项判定索引”和“最终判定表”。

## 推荐回答格式

```text
结论：能/部分能/不适合。
能力等级：A/B/C/D。
ARS 原生承载对象：...
需要的项目约定或外部模型：...
边界说明：...
建议读取：...
```

## 高风险误判

- 把 `label` 当成精确行列位置。
- 把 `displayTitle` 当成多层标题布局。
- 把 `resultPattern` 当成完整格式化语言。
- 把 `ListOfContents` 当成表格主体结构。
- 把 `Analysis.results` 当成必须包含所有最终渲染单元格。
- 忽略 `AnalysisSet`、`DataSubset`、`GroupingFactor`、`WhereClause` 对结果语义的影响。
