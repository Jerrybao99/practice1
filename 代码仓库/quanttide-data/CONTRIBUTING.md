# 贡献指南

欢迎贡献量潮数据工程(`quanttide-data`)！本文档提供本仓库的贡献指南。

## 仓库定位

`quanttide-data` 是量潮第二大脑的数据工程领域仓库（`domains/quanttide-data`），包含数据工程相关的文档、代码、数据和工具。

## 目录结构

```
quanttide-data/
├── apps/              # 可部署应用
├── data/              # 数据仓库（档案、上下文、日志、报告等）
├── docs/              # 文档（教程、手册、规范、章程等）
├── examples/          # 实验室——实验性/原型项目
│   └── default/       # 实验室入口
├── packages/          # 共享工具包
├── .agents/           # AI 技能配置
├── AGENTS.md          # Agent 工作指南
├── CONTRIBUTING.md    # 本文件
└── README.md          # 项目说明
```

## 开发环境

### 前置依赖

- Git（≥ 2.20）
- Python 3.10+（运行数据工具和脚本）
- Rust（构建 qtdata 等应用）

### 克隆仓库

```bash
git clone --recurse-submodules https://github.com/quanttide/quanttide-data.git
```

如果已克隆但未初始化子模块：

```bash
git submodule update --init --recursive
```

## 子模块管理

本仓库由大量子模块组成，每个子模块是独立仓库。

### 子模块路径约定

| 根路径 | 用途 | 例子 |
|--------|------|------|
| `apps/{name}` | 可部署应用 | `apps/qtdata` |
| `data/{name}` | 数据仓库 | `data/journal` |
| `docs/{name}` | 文档仓库 | `docs/tutorial` |
| `examples/{name}` | 实验室项目 | `examples/default` |
| `packages/{name}` | 共享工具包 | `packages/quanttide-toolkit` |

### 更新子模块

```bash
# 更新单个子模块到最新
git submodule update --remote apps/qtdata

# 更新所有子模块
git submodule update --remote
```

### 子模块提交流程

子模块变更必须遵循**分层提交**原则：

```bash
# 1. 在子模块中修改
cd data/journal
# ... 修改文件 ...
git add .
git commit -m "docs: 添加xxx"
git push

# 2. 回到父仓库更新引用
cd ..
git add data/journal
git commit -m "update data/journal: xxx"
git push
```

### 重要规则

- **禁止**：直接在父仓库修改子模块文件
- **必须**：在子模块仓库独立提交推送，父仓库只更新引用
- **提交即推送**：提交后默认推送远端

## 提交规则

每次 commit 以后自动 push。

## 提交规范

### 提交信息格式

```
<type>: <subject>

<body>
```

### 类型说明

| 类型 | 用途 |
|------|------|
| `feat` | 新功能 |
| `fix` | 修复问题 |
| `docs` | 文档修改 |
| `style` | 格式调整 |
| `refactor` | 重构 |
| `chore` | 其他维护 |
| `update` | 更新子模块引用 |

### 示例

```
docs: 更新数据建模章节

- 添加数据湖内容
- 修正链接错误
```

```
update data/journal: 2026-07-27 日志
```

## 常见任务

### 添加新文档

1. 在对应子模块的目录下创建 `.md` 文件
2. 在子模块中提交推送
3. 在父仓库更新子模块引用

### 新增子模块

```bash
git submodule add <repository-url> <path>
git commit -m "feat: add <name> submodule"
git push
```

### 移除子模块

```bash
git submodule deinit -f <path>
git rm <path>
git commit -m "chore: remove <name> submodule"
git push
```

## 文档与规范

本仓库涉及两类文档：

| 类型 | 位置 | 角色 |
|------|------|------|
| **spec（规范）** | `docs/specification/` | 结构与格式 |
| **bylaw（章程）** | `docs/bylaw/` | 规则与治理 |

- spec 定义目录结构、文件格式、字段定义、命名约定
- bylaw 定义流程要求、质量约束、变更规则、跨领域边界

## AI 辅助

本仓库配置了 AI 工作指南（`AGENTS.md`）和技能（`.agents/skills/`），使用 AI 编码助手时请先阅读 `AGENTS.md`。

## 联系方式

如有疑问，可通过 GitHub Issues 联系维护者。
