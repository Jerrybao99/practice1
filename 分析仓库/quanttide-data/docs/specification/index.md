# 量潮数据工程标准（QuantTide Data Engineering Specification）

> 版本：v0.1.0-rc.1 | 日期：2026-07-24
> 本文档定义量潮数据工程的四层框架及其组件规范。

---

## 1. 引言

本文档是量潮数据工程标准的正式规范。它定义了数据工程项目的四个抽象层次以及每层的组件格式、字段约束和交互规则。

本文档中的关键词 "MUST"、"MUST NOT"、"SHOULD"、"SHOULD NOT"、"MAY" 遵循 [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt) 的解释。

### 1.1 四层框架

量潮数据工程标准将数据项目分解为四个层次，从业务需求到技术执行逐层递进：

```
Requirement（需求层）
    │ 动词: clarify  输入: 客户上下文  输出: DRD
    │ 受众: 客户、业务方  语言: 自然语言
    ↓
Specification（规格层）
    │ 动词: design  输入: DRD  输出: Contract + Blueprint
    │ 受众: 工程师  语言: YAML（结构化）
    │
    ├── Contract  回答: 数据长什么样（输入/输出结构）
    └── Blueprint  回答: 数据怎么处理（工作流步骤）
    ↓
Implementation（实现层）
    │ 动词: implement  输入: Specification  输出: Catalog + Pipeline
    │ 受众: 运行时
    │
    ├── Catalog  回答: 有哪些数据（运行时文件注册）
    └── Pipeline  回答: 怎么执行（可执行处理流程）
    ↓
Task（任务层）
    │ 动词: execute  输入: Pipeline + Catalog
    │
    ├── Feature（产出）
    └── Observation（观测）
```

### 1.2 完整流程链

```
Context → clarify → Requirements (DRD)
    → design → Specification (Contract + Blueprint)
    → implement → Catalog + Pipeline
    → execute → Task
    → report → transfer → Delivery
```

### 1.3 文档约定

| 约定 | 含义 |
|------|------|
| `MUST` | 符合规范的实现必须满足的要求 |
| `MUST NOT` | 符合规范的实现绝对不能做的事 |
| `SHOULD` | 推荐满足的要求，在特定上下文中可以忽略 |
| `SHOULD NOT` | 不推荐的做法，但允许有理由的例外 |
| `MAY` | 可选的能力，实现者自行决定 |

---

## 2. 配置目录

本规范遵循"约定优先于配置"原则。用户无需手动设置即可使用，但所有路径 MUST 可通过环境变量覆盖。

### 2.1 目录结构

符合本规范的实现 MUST 使用以下目录布局：

```
.quanttide/data/
├── requirement/         # 数据需求文档（Markdown）
├── specification/       # 数据规格书
│   ├── contract/        #   数据契约（YAML）
│   └── blueprint/       #   处理蓝图（YAML）
├── catalog/             # 数据目录（registry.json）
└── pipeline/            # 数据管道（YAML + 可执行代码）
```

### 2.2 命名规范

所有目录名和配置字段名 MUST 遵循 Unix 风格：

- **单数** — 目录名用单数。示例: `contract/`（正确），`contracts/`（错误）
- **小写** — 全小写字母。示例: `blueprint/`（正确），`BluePrint/`（错误）
- **连字符** — 单词间用 `-` 分隔。示例: `requirement/`（正确），`requirements/`（错误）
- **无缩写** — 除非是通用缩写。示例: `catalog/`（正确），`catlg/`（错误）

YAML 文件中的字段名 MUST 遵循相同规范：

```yaml
# 正确
name: csv-standard
steps:
  - command: python3

# 错误
name: csvStandard
Steps:
  - Command: python3
```

### 2.3 路径解析

路径解析 MUST 按以下优先级进行：

1. 同名环境变量（如 `PIPELINE_DIR`）
2. 环境变量未设置时使用 2.1 节定义的默认路径
3. 默认路径相对于当前工作目录解析

### 2.4 环境变量

| 环境变量 | 默认值 | 说明 |
|----------|--------|------|
| `REQUIREMENT_DIR` | `.quanttide/data/requirement` | DRD 目录 |
| `SPECIFICATION_DIR` | `.quanttide/data/specification` | 规格书根目录 |
| `CONTRACT_DIR` | `.quanttide/data/specification/contract` | Contract 目录 |
| `BLUEPRINT_DIR` | `.quanttide/data/specification/blueprint` | Blueprint 目录 |
| `CATALOG_DIR` | `.quanttide/data/catalog` | Catalog 目录 |
| `PIPELINE_DIR` | `.quanttide/data/pipeline` | Pipeline 目录 |

---

## 3. 术语表

| 术语 | 英文 | 层次 | 定义 |
|------|------|------|------|
| 数据需求文档 | DRD (Data Requirements Document) | Requirement | 面向客户的业务需求描述。使用自然语言，回答"要交付什么"。通过 `clarify` 动作从客户上下文生成。 |
| 数据规格书 | Specification | Specification | Contract 和 Blueprint 的统称。面向技术的设计文档集合。通过 `design` 动作从 DRD 生成。 |
| 数据契约 | Contract | Specification | 数据输入/输出结构的正式定义。描述每个字段的类型、约束和业务含义。 |
| 处理蓝图 | Blueprint | Specification | 数据处理工作流的有序步骤定义。描述从输入到输出的转换路径。 |
| 数据目录 | Catalog | Implementation | 运行时文件注册表。记录 Volume 的接收状态、处理进度和交付信息。 |
| 数据管道 | Pipeline | Implementation | 可执行的数据处理流程定义。将 Blueprint 步骤映射为具体运行时配置和命令。 |

---

## 4. 组件规范索引

| 组件 | 规范文档 | 层次 |
|------|---------|------|
| DRD | [requirement.md](requirement.md) | Requirement |
| Contract | [contract.md](contract.md) | Specification |
| Blueprint | [blueprint.md](blueprint.md) | Specification |
| Catalog | [catalog.md](catalog.md) | Implementation |
| Pipeline | [pipeline.md](pipeline.md) | Implementation |

---

## 5. 版本兼容性

### 5.1 语义化版本

本规范使用 [Semantic Versioning 2.0.0](https://semver.org/lang/zh-CN/)：

- **MAJOR**（主版本号）：不兼容的规范变更（如移除层次、重命名核心概念）
- **MINOR**（次版本号）：向后兼容的新增（如新增可选字段、新增组件）
- **PATCH**（修订号）：向后兼容的修正（如措辞澄清、示例修正）

### 5.2 迁移策略

v0.0.x → v0.1.x 的迁移指南见 [CHANGELOG.md](CHANGELOG.md)。
