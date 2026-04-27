# ARS 对话要点

本目录整理本轮 ARS 讨论的高频结论、主要疑问和几个代表性示例。目标是便于后续继续讨论，不替代 `project/docs/` 中的对象定义。

## 主要结论

1. ARS 更擅长表达分析语义、结果归属和追踪关系，不擅长表达精确表格布局。
2. `AnalysisMethod` / `Operation` 更像分析规格，不等于执行引擎。
3. 一张视觉上的表，通常会拆成多个 `Analysis`，不要求 “一张表 = 一个 analysis”。
4. `n(%)` 场景下，denominator 更稳妥的做法是单独建 analysis；不要默认整列共享同一个大 `N`。
5. `Analysis.variable = USUBJID` 只说明 “数谁”；具体问题通常还需要 `DataSubset`，具体答案分组通常还需要 `GroupingFactor`。
6. 一个 `Analysis` 原生只有一个主 `dataset`，但可以依赖别的数据集提供：
   - `AnalysisSet`
   - `DataSubset`
   - `GroupingFactor`
   - denominator analysis
7. `analysisSetId` 定义在 `ADSL`、而 `Analysis.dataset` 是 `ADQS` / `ADAE` / `ADLB`，语义上可以成立；这表示 subject-level 集合投影到 record-level dataset。
8. `GroupingFactor.groupingDataset` 不必等于 `Analysis.dataset`；分组变量应绑定到它稳定、完整存在的数据集。
9. `AnalysisSet` 如何绑定到主分析数据集中的记录，ARS 原生没有单独 join-key 字段；本项目示例的简单场景默认按 `USUBJID` 连接。
10. “用 ADSL 补齐非 ADSL 数据集中的缺失受试者 / 缺失组组合” 更像实现层语义，不是 ARS 原生核心对象。

## 本轮最重要的修正

对问卷类 `n(%)`，更稳妥的拆法是：

- denominator `N` 单独一个 analysis
- numerator `n` 单独一个 analysis
- percent `%` 再单独一个 analysis

这样比把 `n` 和 `%` 混在一个 analysis 里更少歧义。

## 本轮主要疑问

1. `method` 到底是不是 “真的去分析”。
2. table 转置后，ARS 要不要重建。
3. `n(%)` 的 denominator 在不同 param / 不同问题下变化时怎么表达。
4. `Analysis.variable` 为什么常写成 `USUBJID`，而不是问题回答变量本身。
5. `referencedAnalysisOperations` 只给 `analysisId` 时，如何定位到具体 result。
6. `analysisSetId` 在 `ADSL`、而 `Analysis.dataset` 在 `ADQS` 时是否合理。
7. grouping variable 是否必须来自主分析数据集。
8. 一个 analysis 是否只能有一个主数据集。
9. `AnalysisSet` 与主分析数据集如何绑定受试者。
10. grouping factor 是否能只选部分 group，group 水平顺序是否能显式指定。
11. 分析非 `ADSL` 数据集时，常见的 dummy dataset / template 语义在 ARS 中如何承接。

## 仍需项目约定的点

1. subject-level 条件如何投影到非 `ADSL` 数据集。
2. grouping variable 不在主分析数据集时如何 join。
3. `n(%)` 中 numerator / denominator 的 result 级匹配规则。
4. dummy/template 如何生成，何时补 0，何时保留 missing。
5. `dataDriven` grouping 的排序规则。

## 配套文件

- [examples.md](examples.md)：本轮讨论中最常用的几类表格和简化建模思路。

