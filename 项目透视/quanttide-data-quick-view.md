# quanttide-data 透视

## 项目概述

`quanttide-data` 是量潮第二大脑的数据工程领域的**聚合入口仓库**，通过 Git 子模块架构统一管理数据工程领域的**文档、数据、应用与工具包**。简单说，它是一个"大总管"，**本身不含代码**，只负责把 24 个独立仓库拼装成一个完整的数据工程知识体系。

"第二大脑"是这套仓库体系的总称，数据工程领域只是其中一个"脑区"，意图像人脑一样对外部信息进行收集、整理、检索和复用。

## 知识结构

### 九宫格框架

**docs/程序型记忆九宫格**（约束力层次 × 消费对象）：

- 存"怎么做"：教程、规范、手册、章程等方法性知识

| 约束力 \ 消费对象 | 人类友好 | AI友好 | 规则引擎友好 |
|:------------------|:---------|:-------|:-------------|
| 宪法（最高约束力） | Bylaw 工作章程 | Specification 工程标准 | Toolkit 工具箱 |
| 法律（中等约束力） | Handbook 工作手册 | Gallery 工作案例 | Platform 平台 |
| 法理（低约束力）   | Tutorial 工作教程 | Essay 工作札记 | Example 示例程序 |

**data/陈述型记忆九宫格**（时间维度 × 类型维度）：

- 存"是什么"：日志、报告、档案、历史等事实性数据
- 工作意图是“未来的自我记忆”，关系到组织希望成为一个什么样的组织，一般相对稳定，但和组织高度绑定，不一定具备很强的迁移性

| 时间 \ 类型 | 事件类（"做什么"） | 语义类（"信什么"） | 自我类（"我是谁"） |
|:------------|:-------------------|:-------------------|:-------------------|
| 过去 | Report 工作报告 | Library 工作参考 | History 工作历史 |
| 现在 | Journal 工作日志 | Profile 工作档案 | Brochure 宣传册 |
| 未来 | Roadmap 路线图 | Insight 工作洞察 | Intention 工作意图 |

Context（工作语境）是九宫格的默认入口，是和 AI 的协作中产生的随手记录，当前比较脏，，Archive（工作归档）是过时资产的备份出口，两者不占九宫格位置。

### 顶层目录树

```
quanttide-data/                    ← 父仓库（聚合入口，不含实际代码）
├── apps/                          ← 可部署应用
│   ├── qtcloud-data               ← 数据云 CLI，封装数据需求文档流程
│   └── qtdata                      ← 数据工程工具链：CLI + Provider + Studio
├── data/                          ← 陈述性记忆（事实与内容——"是什么"）
│   ├── context                    ← 工作语境（入口，不占格子）
│   ├── archive                    ← 工作归档（出口，不占格子）
│   ├── report                     ← 工作报告
│   ├── library                    ← 工作参考
│   ├── history                    ← 工作历史
│   ├── journal                    ← 工作日志
│   ├── profile                    ← 工作档案
│   ├── brochure                   ← 宣传册
│   ├── roadmap                    ← 路线图
│   ├── insight                    ← 工作洞察
│   └── intention                  ← 工作意图
├── docs/                          ← 程序性记忆（方法与规则——"怎么做"）
│   ├── bylaw                      ← 工作章程
│   ├── specification              ← 工程标准
│   ├── handbook                   ← 工作手册
│   ├── gallery                    ← 工作案例
│   ├── tutorial                   ← 工作教程
│   └── essay                      ← 工作札记
│   （注：法律+规则引擎友好格位 Platform 平台，当前仓库未注册）
├── examples/                      ← 示例程序
│   ├── company                    ← 商业实体实验室
│   └── default                    ← 默认实验室入口（实验性/原型项目）
├── packages/                      ← 工具箱
│   ├── quanttide-agent-toolkit    ← AI Agent 工具包 (Rust)
│   ├── quanttide-data-toolkit     ← 数据工程 SDK (Dart/Flutter)
│   └── quanttide-toolkit          ← 通用工具包
├── .agents/                       ← AI 技能配置目录
│   └── skills/
│       └── qtcloud-devops/
│           └── SKILL.md           ← DevOps 流程技能定义
├── AGENTS.md                      ← AI Agent 工作指南
├── CONTRIBUTING.md                ← 贡献指南（子模块管理、提交规范）
├── CHANGELOG.md                   ← 版本变更日志
├── STATUS.md                      ← 各子模块当前版本与 commit 快照
├── .gitmodules                    ← 子模块注册表（URL、路径、分支映射）
└── README.md                      ← 项目说明
```

### 关键文档

| 文档 | 角色 | 何时阅读 |
|------|------|----------|
| `README.md` | 项目全貌与模块索引 | 第一次接触时 |
| `CONTRIBUTING.md` | 操作规则与开发约定 | 开始贡献前 |
| `STATUS.md` | 各子模块版本快照 | 需要了解当前进度时 |
| `CHANGELOG.md` | 版本演进历史 | 理解版本变迁时 |
| `.gitmodules` | 子模块注册表 | 查找子项目远程 URL 时 |
| `AGENTS.md` | AI 辅助开发入口 | 使用 AI 编码助手时 |
| `.agents/skills/qtcloud-devops/SKILL.md` | DevOps 操作手册 | 执行构建、测试、发布、审计时 |

### 知识层级关系

```
                              ┌─────────────────────┐
                              │    quanttide-data    │
                              │   （聚合入口仓库）     │
                              └──────────┬──────────┘
                                         │
        ┌────────────────┬───────────────┼───────────────┬────────────────┐
        ▼                ▼               ▼               ▼                ▼
 ┌────────────┐  ┌────────────┐  ┌──────────────┐  ┌──────────┐  ┌──────────────┐
 │   apps/    │  │   data/    │  │    docs/     │  │examples/ │  │  packages/   │
 │ 可部署应用  │  │ 陈述性知识  │  │  程序性知识   │  │ 实验沙盒  │  │  共享开发包   │
 └─────┬──────┘  └─────┬──────┘  └──────┬───────┘  └────┬─────┘  └──────┬───────┘
       │               │               │               │               │
       ▼               ▼               ▼               ▼               ▼
  数据工程工具链   事实与内容归档   方法与规则定义   原型快速验证   可复用 SDK
 (CLI/Studio)   (日志/报告/洞察)  (教程/规范/章程) (default/company) (Rust/Dart)
       │               │               │               │               │
       └───────────────┴───────┬───────┴───────────────┴───────────────┘
                               │
                               ▼
                   ┌───────────────────────┐
                   │    推荐阅读顺序        │
                   │                       │
                   │  1. README.md         │  ← 了解有哪些"零件"
                   │  2. CONTRIBUTING.md   │  ← 学会"操作规则"
                   │  3. STATUS.md         │  ← 掌握当前版本
                   │  4. docs/tutorial     │  ← 从教程开始学习
                   │  5. docs/specification│  ← 理解格式标准
                   │  6. docs/bylaw        │  ← 明确流程约束
                   └───────────────────────┘
```

**知识层级说明：**

- **apps/** — 最上层，直接面向用户的可部署产品（CLI、客户端），是整个工程体系的最终交付物
- **packages/** — 程序型记忆九宫格中 宪法 + 规则引擎友好 格位（Toolkit），提供底层 SDK 能力（AI Agent 调用、数据处理、通用工具）
- **docs/** — 程序型记忆，按约束力层次（宪法/法律/法理）和消费对象（人类/AI/规则引擎友好）3×3 组织；当前已注册 6 个格位，Platform（法律+规则引擎友好）暂缺
- **data/** — 陈述型记忆，按时间维度（过去/现在/未来）和类型维度（事件/语义/自我类）3×3 组织；11 个条目中 context 为入口、archive 为出口，其余 9 个对应九宫格
- **examples/** — 法理 + 规则引擎友好 格位（Example 示例程序），用于快速实验、原型验证、概念证明

**两类文档的核心区别：**

| 对比维度 | `docs/specification`（规范） | `docs/bylaw`（章程） |
|----------|------------------------------|---------------------------|
| 定义什么 | 目录结构、文件格式、字段定义、命名约定 | 流程要求、质量约束、变更规则、跨领域边界 |
| 回答什么 | "长什么样" | "必须做什么" |
| 约束性质 | 格式约定 | 治理规则 |

---

## 核心功能

### 子模块聚合管理

本项目本身不含任何代码或文档内容，其核心功能是通过 `.gitmodules` 将 24 个独立 Git 仓库注册为子模块，实现集中索引、统一版本追踪和批量操作。

**子模块分层策略：**

| 层 | 目录 | 独立性 | 说明 |
|----|------|--------|------|
| 应用层 | `apps/` | 独立发布 | 各自有独立版本号（如 qtdata studio/v0.1.0-alpha.1） |
| 知识层 | `data/` + `docs/` | 独立演进 | 以 main 分支持续滚动更新 |
| 实验层 | `examples/` | 完全独立 | 自由实验，不纳入发布流程 |
| 基础层 | `packages/` | 独立版本 | 有各自语言生态的版本号（如 Rust v0.1.1、Dart v0.3.0-9） |

### DevOps 自动化

通过 `qtcloud-devops` CLI 工具覆盖完整开发生命周期：

```
code → build → test → release
```

- **code**：子模块同步状态检查、TODO/FIXME 密度审计
- **build**：编译器配置、CI 工作流、依赖声明审计
- **test**：测试覆盖率、错误变体覆盖、门禁达标审计
- **release**：发布遵循 SemVer 语义化版本四步流程：
  1. 更新版本号（正式版 `vX.Y.Z`，预发布版加 `-alpha.N`/`-beta.N`/`-rc.N`）
  2. 更新 CHANGELOG（Keep a Changelog 格式）
  3. 打标签（单组件 `vX.Y.Z`，多组件 `scope/vX.Y.Z`）
  4. 发布 GitHub Release（body 含 CHANGELOG 内容）

### 分层提交原则

子模块变更遵循"两层提交"规则，防止直接修改子模块文件导致引用混乱：

```bash
# 第一层：在子模块内提交
cd data/journal
git add . && git commit -m "docs: 添加xxx" && git push

# 第二层：回父仓库更新引用
cd ../..
git add data/journal && git commit -m "update data/journal: xxx" && git push
```

---

## 最小工作流

### 前置环境

- Git ≥ 2.20
- Python ≥ 3.10（用于运行 qtcloud-devops 等工具）
- Rust（构建 qtdata 等应用）
- GitHub 账号（用于子模块操作）

### 命令行

```bash
# ====== 第一步：克隆仓库（含所有子模块） ======
git clone --recurse-submodules https://github.com/quanttide/quanttide-data.git
cd quanttide-data

# 如果克隆时忘了加 --recurse-submodules，补执行：
# git submodule update --init --recursive

# ====== 第二步：快速了解项目 ======
cat README.md              # 项目全貌
cat STATUS.md              # 当前各子模块版本
cat CONTRIBUTING.md        # 操作规则（必读！）

# ====== 第三步：安装 DevOps 工具 ======
python --version           # 确认 Python 可用
pip install qtcloud-devops

# ====== 第四步：环境自检 ======
qtcloud-devops source status    # 检查系统依赖（git、python、rust 等）
qtcloud-devops status           # 查看项目整体状态

# ====== 第五步：日常开发流程 ======
qtcloud-devops code audit       # 代码审计（子模块同步、代码质量）
qtcloud-devops build status     # 查看构建状态
qtcloud-devops test status      # 查看测试状态
qtcloud-devops plan status      # 查看路线图进度
```

### 推荐探索路径

```
1. 先读 README.md         → 知道这个项目有哪些"零件"
2. 再读 CONTRIBUTING.md   → 知道"操作规则"是什么
3. 查看 STATUS.md         → 了解每个零件的当前版本
4. 浏览 .gitmodules       → 找到每个零件的远程仓库地址
5. 挑选一个子模块深入     → 如 docs/tutorial（入门教程）或 data/journal（工作日志）
```

### 常见操作速查

| 场景 | 命令 |
|------|------|
| 添加新子模块 | `git submodule add <url> <path>` |
| 更新全部子模块 | `git submodule update --remote` |
| 更新单个子模块 | `git submodule update --remote apps/qtdata` |
| 移除子模块 | `git submodule deinit -f <path> && git rm <path>` |
| 检查子模块同步状态 | `qtcloud-devops code status` |
| 子模块内修改后提交 | 先在子模块目录内 `commit → push`，再回父仓库 `git add <path> → commit` |
| 发布前预检 | `qtcloud-devops release audit` |
| 执行发布 | `qtcloud-devops release publish` |
| 修复路线图格式 | `qtcloud-devops plan doctor` |