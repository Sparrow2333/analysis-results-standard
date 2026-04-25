# ARS AI Guide

这组笔记是给后续 AI 对话使用的渐进式披露入口。目标不是替代 CDISC ARS 官方文档，而是让 AI 用最少上下文先建立正确心智模型，再按任务需要读取更深的原始资料。

## 先读这个结论

ARS 可以先理解为一个“分析结果逻辑模型”：

- `ReportingEvent` 是一次报告需求下的根对象，收纳 outputs、analyses、analysis sets、data subsets、groupings、methods、reference documents 和目录结构。
- TFL 不是直接等同于一个视觉版式文件。ARS 更擅长表达 planned analysis、output/display、结果值、分组、筛选条件和追踪关系。
- 物理排版、精确行列坐标、字体、缩进、分页和渲染细节通常需要外部约定或渲染模型。
- `notes/ars-capability-checklist.md` 已整理为完成判定版：勾选表示已评估，等级 A/B/C/D 才表示支持程度。

## 推荐读取顺序

如果只需要快速参与对话，读：

1. [00-ai-loading-protocol.md](00-ai-loading-protocol.md)
2. [01-mental-model.md](01-mental-model.md)

如果问题涉及 TFL 如何落到 ARS 对象，继续读：

1. [02-tfl-flow.md](02-tfl-flow.md)
2. [04-capability-boundaries.md](04-capability-boundaries.md)

如果问题涉及“ARS 能否表达某种 shell / row / column / cell / denominator / shift table / SOC-PT / PK 混合结构”，继续读：

1. [../ars-capability-checklist.md](../ars-capability-checklist.md)
2. 优先看其中的“已完成评估说明”“检查项判定索引”“最终判定表”和“已完成结论”。

如果需要查官方对象定义或示例，读：

1. [03-source-map.md](03-source-map.md)
2. 再跳转到 `project/docs/`、`model/`、`workfiles/examples/` 或已有 `notes/`

## 上下文预算

| 预算 | 建议读取 | 适用情况 |
| --- | --- | --- |
| 极小 | 本文件 + `00-ai-loading-protocol.md` | 只需要知道后续该读什么 |
| 小 | 再加 `01-mental-model.md` | 讨论概念、任务拆解、写提示词 |
| 中 | 再加 `02-tfl-flow.md` 和 `04-capability-boundaries.md` | 讨论 TFL 表达能力、对象分工 |
| 大 | 再加 `notes/ars-capability-checklist.md` 的完成判定段落 | 需要判断复杂 shell 能力边界 |
| 很大 | 按 `03-source-map.md` 读取官方对象页和示例 | 需要精确字段、cardinality、JSON/YAML 示例 |

## 不要一开始就读什么

不要默认整批读取 `docs/`、`project/docs/` 或完整 schema。它们很有价值，但信息量大。除非问题需要字段级定义、cardinality、序列化格式或官方示例，否则先读本目录中的压缩笔记。

## 主要原始资料

- `README.md`：项目目标和背景。
- `model/ars_ldm.md`：整体模型图。
- `project/docs/`：从模型生成的 Markdown 对象文档。
- `project/jsonschema/ars_ldm.schema.json`：JSON Schema。
- `model/ars_ldm.yaml`：LinkML 风格的模型源。
- `workfiles/examples/`：示例 Excel、YAML、JSON、PDF、SAS 等材料。
- `notes/ars-capability-checklist.md`：已完成初版评估的能力边界检查单，包含判定索引、最终能力矩阵和应用层对象建议。
- `notes/Analysis.md`：用户维护的 `Analysis` 类说明。
