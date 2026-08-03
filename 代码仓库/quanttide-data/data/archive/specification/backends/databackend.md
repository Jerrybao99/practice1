# 数据后端 DataBackend

## 定义

对 DataFile 和 Database 建模，并且定义他们和 DataSchema 的转换关系。

## 分类

统称 DataBackend。分为 DataStorage 和 Database 两类，区别主要是 Database 自带事务处理。

### DataStorage

DataStorage 分为本地、云端，云端分为对象存储、文件存储等，根据云计算标准定。DataStorage 里存储的是 DataFile 数据文件，比如 CSV 格式、xlsx 格式、json 格式等。

## 相关概念

### 中文名

数据后端

### 解释

数据后端是一个系统或软件组件，负责存储、检索和管理应用程序或系统的数据。数据后端通常是一个数据库管理系统（DBMS）或数据存储系统，为应用程序开发人员提供 API 或其他接口来与数据交互。

数据后端通常将数据存储和检索的细节抽象出来，为应用程序开发人员提供更简单、更一致的接口。除了基本的存储和检索功能外，数据后端还可以提供数据索引、搜索能力、数据同步和数据复制等功能。

### 示例

数据后端的例子包括：
- SQL 数据库：如 MySQL、PostgreSQL 和 Oracle
- NoSQL 数据库：如 MongoDB 和 Cassandra
- 云存储服务：如 Amazon S3 和 Google Cloud Storage
