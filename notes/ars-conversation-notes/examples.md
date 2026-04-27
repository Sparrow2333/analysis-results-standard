# ARS 示例

本文件只保留本轮讨论中最有代表性的三类表。

## 示例 1：多 param、不同 method、横向按治疗组

### 目标表

| Parameter | Statistic / Level | Placebo | Drug X |
| --- | --- | ---: | ---: |
| Population | N | 86 | 84 |
| Sex | Male, n (%) | 40 (46.5) | 38 (45.2) |
|  | Female, n (%) | 46 (53.5) | 46 (54.8) |
| Age | N | 86 | 84 |
|  | Mean | 75.2 | 74.4 |
|  | SD | 8.59 | 7.89 |

### 建模要点

- 一个 `Output` / `OutputDisplay`
- treatment 一个 `GroupingFactor`
- sex 一个 `GroupingFactor`
- 多个 `Analysis`
  - population `N`
  - sex `n`
  - sex `%`
  - age continuous summary

### 要点

- 同一张表可拆成多个 analyses。
- 不同 param 可使用不同 variable 和 method。

## 示例 2：多 treatment、多 visit、多 parameter 的连续变量 AVAL 汇总

### 目标表

| Parameter | Visit | Statistic | Placebo | Drug X 50 mg | Drug X 100 mg |
| --- | --- | --- | ---: | ---: | ---: |
| ALT | Baseline | Mean | 35.2 | 34.8 | 36.1 |
| ALT | Baseline | Variance | 118.40 | 121.70 | 116.90 |
| ALT | Week 4 | Mean | 37.9 | 33.5 | 31.8 |
| ALT | Week 4 | Variance | 130.20 | 110.60 | 108.10 |
| AST | Baseline | Mean | 28.6 | 29.1 | 27.9 |
| AST | Baseline | Variance | 84.50 | 80.30 | 79.60 |

### 建模要点

- `Analysis.dataset = ADLB`
- `Analysis.variable = AVAL`
- `GroupingFactor`
  - treatment
  - parameter
  - visit
- `Method`
  - mean
  - variance

### 要点

- 如果主变量和方法结构一致，一个 analysis 可以承接多 `TRT x PARAM x VISIT` 组合。
- 如果不同 parameter 的方法结构明显不同，更适合拆成多个 analyses。

## 示例 3：问卷问题中 denominator 变化的 `n(%)`

### 目标表

| Question | Response | Placebo | Drug X |
| --- | --- | ---: | ---: |
| Subjects in group | N | 86 | 84 |
| Q1. 是否出现问询情况A | Yes, n (%) | 30 (34.9) | 28 (33.3) |
|  | No, n (%) | 56 (65.1) | 56 (66.7) |
| Female subjects in group | N | 46 | 48 |
| Q2. 您是否停止月经 | Yes, n (%) | 10 (21.7) | 12 (25.0) |
|  | No, n (%) | 36 (78.3) | 36 (75.0) |

### 建模要点

Q1:

- `An_N_All_ByTrt`
- `An_Q1_Count_ByTrtResp`
- `An_Q1_Pct_ByTrtResp`

Q2:

- `An_N_Female_ByTrt`
- `An_Q2_Count_ByTrtResp`
- `An_Q2_Pct_ByTrtResp`

### 要点

- Q1 的 denominator 是总体 `N`
- Q2 的 denominator 是女性 `N`
- 对问卷类 `n(%)`：
  - `Analysis.variable = USUBJID` 表示统计对象是受试者数
  - `DataSubset` 表示具体问题
  - `GroupingFactor` 表示答案分组

## 补充边界

1. `Analysis.dataset` 可以是 `ADQS` / `ADAE` / `ADLB`，而 `AnalysisSet` 或 subject-level grouping 可以定义在 `ADSL`。
2. 这类跨数据集语义成立，但实现层需要明确 join 规则。
3. dummy/template 的生成通常属于实现层，不是 ARS 原生对象。

