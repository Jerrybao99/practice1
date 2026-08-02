# 数据集`DataSet`

数据集领域模型的设计旨在提供一个简单而灵活的模型，以表示数据集的基本信息。

字段定义为：

- **id (String):** 数据集的唯一标识符。
- **name (String):** 数据集的名称。
- **verboseName (String?):** 数据集的详细名称。
- **readme (String?):** 数据集的描述信息。

## 相关概念

### Hugging Face Datasets

Hugging Face 的 [`datasets`](https://huggingface.co/docs/datasets/index) 提供了他们平台的数据集的下载，并且提供了一系列的加工和处理功能，是我们的数据集特性的重要参考。

## 实体关系

### DataSet 和 DataSchema

两种方案：

1. **保守方案**。DataSet 和 DataSchema 只能一对多，即 DataSchema 必须属于一个 DataSet。比较符合正常的理解，且可以和 DataSchema 和 DataRecord 的设计统一。
2. **激进方案**。多对多，即认为 DataSchema 全局唯一，DataSet 根据需要可选择不同的 Schema 所属的交付。这样比较灵活，但是工程上风险更大。

如果没有明确理由选激进方案，则优先考虑保守方案。

### DataSchema 和 DataRecord

一对多，DataRecord 是 DataSchema 下的一条记录

### Isolating 特性

初步考虑是允许他们不 isolated，理解为对真实数据的抽象。

比如，模型 1 和模型 2 都基于某个 CSV 文件，都使用了 `id` 字段，模型 1 使用了 `raw` 字段，模型 2 使用了 `marked` 字段。这样可以用来建模人工标记前后的数据变化。

### 嵌套支持

可以考虑支持。

- **DataSet** 可以考虑有子数据集的概念。
- **DataSchema** 可以把其他 Schema 作为组成部分表达更复杂的概念。
- **DataRecord** 可以把其他 Record 作为组成部分表达更复杂的概念。
