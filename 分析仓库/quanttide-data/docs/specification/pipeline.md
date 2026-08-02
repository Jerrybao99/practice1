# Pipeline — 数据管道

> 层次：Implementation | 动词：implement | 受众：运行时 | 格式：YAML + 可执行代码

---

## 1. 定义

数据管道（Pipeline）是实现层的可执行数据处理流程定义。它将 Blueprint 中定义的步骤映射为具体的运行时命令、环境配置和调度规则。

Pipeline 是 Implementation 的组成部分，与 Catalog 并列：
- Pipeline 定义**怎么执行**（运行时配置 + 命令映射）
- Catalog 记录**执行了什么**（运行时文件注册）

一个 Pipeline MUST 关联一个 Blueprint。Pipeline 是 Blueprint 的运行时实现。

---

## 2. 格式与位置

### 2.1 格式

Pipeline 定义 MUST 使用 YAML 格式。

可执行部分（step 中的 `command`）MAY 引用外部代码：
- Python 模块路径（如 `python3 -m processors.validate`）
- Shell 命令
- 容器镜像引用（通过 `image` 字段）

### 2.2 文件位置

Pipeline 定义文件 MUST 存放在 `.quanttide/data/pipeline/` 目录下。

可执行代码 MAY 存放在同目录或子目录中。

---

## 3. 结构定义

### 3.1 顶层字段

| 字段 | 类型 | 约束 | 说明 |
|------|------|------|------|
| `name` | string | MUST 存在，MUST 唯一 | 管道名称 |
| `blueprint` | string | MUST 存在 | 关联的 Blueprint 名称。管道是该 Blueprint 的运行时实现 |
| `runtime` | object | SHOULD 存在 | 运行时配置（引擎、版本） |
| `schedule` | string | MAY 存在 | 调度方式。合法值：`manual`、`cron`、`event-driven`。默认 `manual` |
| `steps` | list | MUST 存在，MUST NOT 为空 | 执行步骤列表。应与关联 Blueprint 的 steps 一一对应 |

### 3.2 runtime 字段

| 字段 | 类型 | 约束 | 说明 |
|------|------|------|------|
| `engine` | string | MUST 存在 | 执行引擎。如 `python3`、`node`、`bash` |
| `version` | string | SHOULD 存在 | 运行时版本。如 `3.11` |

### 3.3 Step 定义（steps 中的每个条目）

| 字段 | 类型 | 约束 | 说明 |
|------|------|------|------|
| `name` | string | MUST 存在 | 步骤名称。SHOULD 与对应 Blueprint step 的 name 一致 |
| `command` | string | MUST 存在 | 可执行命令或模块入口 |
| `image` | string | MAY 存在 | 容器镜像引用（容器化部署时使用） |
| `env` | map | MAY 存在 | 步骤级环境变量覆盖 |

---

## 4. 验证规则

符合本规范的 Pipeline MUST 满足以下规则：

1. `name` 字段 MUST 非空且唯一
2. `blueprint` 字段 MUST 引用一个已存在的 Blueprint 名称
3. `steps` MUST 为非空列表
4. 每个 step 的 `name` 和 `command` MUST 非空
5. `schedule` 如果指定，MUST 为 `manual`、`cron` 或 `event-driven` 之一
6. `runtime.engine` MUST 为合法的执行引擎标识符

---

## 5. 结构

```yaml
name: <pipeline-name>
blueprint: <关联的 Blueprint 名称>
runtime:
  engine: <python3|node|bash|...>
  version: <版本号>
schedule: <manual|cron|event-driven>  # 可选，默认 manual

steps:
  - name: <对应 Blueprint step 名称>
    command: <可执行命令>
    image: <容器镜像>               # 可选
    env:                            # 可选
      KEY: value
```

---

## 6. 示例

```yaml
name: ghtorrent-user-activity-pipeline
blueprint: ghtorrent-user-activity
runtime:
  engine: python3
  version: "3.11"
schedule: manual

steps:
  - name: 环境搭建与数据下载
    command: python3 -m processors.download --source ${SOURCE_URL}
    image: registry.quanttide.com/data/downloader:v1.0.0

  - name: 数据解压与提取
    command: python3 -m processors.extract --input ${DUMP_PATH}

  - name: 用户匹配与数据筛选
    command: python3 -m processors.filter --users ${USER_LIST} --data ${EXTRACTED_DIR}

  - name: 多维度日聚合计数
    command: python3 -m processors.aggregate --data ${FILTERED_DIR} --output ${AGGREGATED_DIR}

  - name: 面板合并
    command: python3 -m processors.merge --input ${AGGREGATED_DIR}

  - name: 数据验证与交付
    command: python3 -m processors.validate --data ${MERGED_CSV}
```
