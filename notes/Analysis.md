# Analysis 类说明

`Analysis` 类可以理解为 ARS 中“分析任务的完整规格说明”。它描述的不是分析结果本身，而是一次分析应该如何被定义、执行和追踪。

## 核心含义

一条 `Analysis` 记录通常会说明以下内容：

- 分析的对象变量是什么
- 使用什么分析方法
- 针对哪个分析集执行
- 是否限定某个数据子集
- 是否需要按某些分组因子分层或分组

换句话说，`Analysis` 负责把一次分析从“想法”变成“可执行、可追踪的定义”。

## 继承关系

`Analysis` 继承自 `NamedObject`，因此天然具备命名类对象的基础属性：

- `name`
- `description`
- `label`

这意味着它不仅是一个逻辑分析定义，也是一种可以被清晰标识、展示和引用的对象。

## 主要字段

### 必填字段

- `id`：实例标识
- `reason`：执行该分析的理由
- `purpose`：该分析在报告或证据链中的用途
- `methodId`：分析方法
- `name`：名称

### 可选字段

- `version`：版本号
- `documentRefs`：关联的外部文档
- `categoryIds`：适用的分类
- `dataset`：分析数据集名称
- `variable`：分析变量名称
- `analysisSetId`：分析集
- `dataSubsetId`：数据子集
- `orderedGroupings`：分组因子列表
- `referencedAnalysisOperations`：引用的分析操作
- `programmingCode`：分析实现代码或代码引用
- `results`：分析对应的结果

## 字段含义怎么理解

- `dataset` + `variable`
  - 说明“分析哪个数据集里的哪个变量”

- `analysisSetId`
  - 说明“对哪些受试者或样本集合做分析”

- `dataSubsetId`
  - 说明“是否还要进一步限定数据记录范围”

- `orderedGroupings`
  - 说明“是否按某些因素分组，以及分组顺序是什么”

- `methodId`
  - 说明“采用哪套统计方法或操作步骤”

- `programmingCode`
  - 说明“用什么程序实现该分析”

- `results`
  - 说明“这个分析最终产生了哪些操作结果”

## 相关关系

`Analysis` 不是孤立存在的，它会和多个对象发生关联：

- 会被 `ReportingEvent.analyses` 收纳，表示一个 reporting event 中包含多个分析
- 会被 `OrderedListItem.analysisId` 引用，用于目录或列表结构中定位分析
- 会被 `ReferencedOperationRelationship.analysisId` 引用，用于描述操作与分析之间的引用关系
- 会被 `ReferencedAnalysisOperation.analysisId` 引用，用于标记某个分析操作引用了哪条分析

## 一句话总结

`Analysis` 是 ARS 里“分析定义”的中心类，用来完整表达一次分析的目标、范围、方法、实现和结果。
