# 数据模型`DataSchema`

数据模型领域模型的设计旨在提供通用、灵活的模型，以适应不同数据源和应用场景。该模型包含了数据模型的基本信息和字段信息，使其能够处理结构化、半结构化和部分非结构化格式的数据。

字段定义：

- **id (String?):** 数据模型的唯一标识符，由系统生成。
- **name (String?):** 数据模型的标识，由开发者定义。
- **verboseName (String?):** 数据模型的名称。
- **readme (String?):** 数据模型的说明文档。
- **type (String?):** 数据模型的类型。
- **createdAt (DateTime?):** 数据模型的创建时间。
- **updatedAt (DateTime?):** 数据模型的更新时间。
- **fields (List<DataSchemaField>?):** 包含数据模型字段的列表。

其中`DataSchemaField`的字段定义：

- **name (String?):** 数据模型字段的标识，由开发者定义。
- **verboseName (String?):** 数据模型字段的名称。
- **type (String?):** 数据模型字段的类型。
- **defaultValue (String?):** 数据模型字段的默认值。
