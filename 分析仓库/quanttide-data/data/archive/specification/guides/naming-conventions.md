# 命名规范

## 数据工程领域模型类名的命名习惯

选择"数据实体 `DataEntity`"这个名字主要是考虑给个统一的基类名。来源于领域模型的实体类模型 Entity。不过 Dispatcher 也是 Entity 模型。因此用 DataEntity 表示是关于数据的实体。

其他的命名可以增加 Data 前缀也可以不加，比如 DataHandler 或者 Handler。增加的好处是可以和其他领域的类似概念区分，坏处是名字会变长、表述和敲程序都稍微会不方便一些。但是如果都加，类似于 Cralwer 一类约定俗称的表述加上 DataCrawler 就会显得比较奇怪，一般 Processor 也指的就是数据处理。

## 工具库命名规范

以 Python 语言和 Django 框架为例：

### 仓库命名

- **仓库名/README 项目名**：`quanttide-data-python`，`django-quanttide-data`
- **命名原则**：根据过往的习惯和社区习惯，框架名在前面、语言名在后面，以示区分
- **仓库描述/中文名**：量潮数据工程 Python/Django 工具包（Toolkit 或者 SDK）

### 包名

- `quanttide-data`；`django-quanttide-data`
- 去掉语言名做简化；框架名带上防止和语言冲突

### 模块名

- `quanttide_data`；`django_quanttide_data`
- 和包名类似

### 其他工程标准

其他工程标准也遵循类似的命名规范。
