# 项目透视模板

- [ ] 不超过 300 行
- [ ] 专业、便于软件开发新人理解的语句
- [ ] 重点的文本内容加粗显示
- [ ] 本文所有复选框内容不出现在输出中，仅为 AI 生成的约束条件

## 项目概述

- [ ] 一句话解释本项目是什么，在做什么

## 知识结构

### 顶层目录树

- [ ] 列出重点目录和重点文件，随附简要解释，示例如下

```
quanttide-data/                ← 父仓库（聚合入口）
├── apps/                      ← 可部署应用
│   ├── qtcloud-data           ← 数据云 CLI
│   └── qtdata                  ← 数据工程工具链
├── data/                      ← 陈述性记忆（事实/内容）
│   ├── archive                ← 历史归档
│   ├── brochure               ← 项目宣传册
│   ├── context                ← 行业竞争调研
│   ├── history                ← 项目发展历史
│   ├── insight                ← 行业洞察
│   ├── intention              ← 产品意图与规划
│   ├── journal                ← 工作日志
│   ├── library                ← 解决方案库
│   ├── profile                ← 数据工程档案
│   ├── report                 ← 分析报告
│   └── roadmap                ← 路线图
├── docs/                      ← 程序性记忆（方法/规则）
│   ├── bylaw                  ← 工程章程（流程约束）
│   ├── essay                  ← 行业随笔
│   ├── gallery                ← 工程案例集
│   ├── handbook               ← 操作手册
│   ├── specification          ← 工程标准（格式规范）
│   └── tutorial               ← 入门教程
├── examples/                  ← 实验室（原型项目）
│   ├── company                ← 商业实体实验室
│   └── default                ← 默认实验室入口
├── packages/                  ← 共享工具包
│   ├── quanttide-agent-toolkit ← AI Agent 工具包 (Rust)
│   ├── quanttide-data-toolkit  ← 数据工程 SDK (Dart/Flutter)
│   └── quanttide-toolkit       ← 通用工具包
├── .agents/                   ← AI 技能配置
├── AGENTS.md                  ← AI 工作指南
├── CONTRIBUTING.md            ← 贡献指南
├── CHANGELOG.md               ← 版本日志
├── STATUS.md                  ← 子模块状态快照
└── README.md                  ← 项目说明
```

### 关键文档

- [ ] 列出本项目必须要阅读的关键文档 
- [ ] 以 md 列表的形式呈现，表头为文档、角色、何时阅读三个字段，简要表达，示例如下

| 文档 | 角色 | 何时阅读 |
|------|------|----------|
| `README.md` | 项目全貌 | 第一次接触时 |
| `CONTRIBUTING.md` | 操作规则 | 开始贡献前 |
| `STATUS.md` | 版本快照 | 了解当前状态时 |
| `CHANGELOG.md` | 演进历史 | 理解版本变迁时 |
| `.gitmodules` | 子模块注册表 | 查找子项目 URL 时 |
| `AGENTS.md` | AI 工作流 | 使用 AI 辅助开发时 |
| `.agents/skills/qtcloud-devops/SKILL.md` | DevOps 手册 | 执行发布/审计时 |

### 知识层级关系

- [ ] 绘制知识依赖层级图，清晰表达知识层级关系，示例如下

```
                                ┌──────────────────────────┐
                                │        qtdata-main        │
                                │   （数据服务交付平台仓库）  │
                                └─────────────┬────────────┘
                                              │
        ┌─────────────┬───────────────┬───────┴───────┬──────────────┐
        ▼             ▼               ▼               ▼              ▼
 ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────┐
 │    src/    │ │   docs/    │ │   tests/   │ │ examples/  │ │  scripts/  │
 │ 可执行组件 │ │ 程序性知识  │ │  测试体系  │ │  原型沙盒  │ │  运维脚本  │
 └─────┬──────┘ └─────┬──────┘ └─────┬──────┘ └─────┬──────┘ └────────────┘
       │              │              │              │
       │              │              │              │
       ▼              ▼              ▼              ▼
  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────────┐
  │ CLI     │   │ 产品定义 │   │ 基础设施 │   │ HTML 交互    │
  │ (Rust)  │   │ 文档     │   │ + 工具层 │   │ 原型验证     │
  │         │   │         │   │         │   │             │
  ├─────────┤   ├─────────┤   ├─────────┤   │ cloud2→4     │
  │ Provider│   │ pmd(问题)│   │ conftest │   │ →qtcloud    │
  │ (FastAPI│   │ prd(剧本)│   │ BasePage │   │ (+ PRD/设计) │
  │ )        │   │ brd(场景)│   │ 截图/录屏│   │             │
  ├─────────┤   │ add(资产)│   │         │   └─────────────┘
  │ Studio  │   │ drd(数据)│   └─────────┘
  │ (Flutter)│   │ ixd(交互)│
  └────┬────┘   └────┬────┘
       │             │
       │             ▼
       │   ┌─────────────────────────────────────┐
       │   │    知识层级说明                      │
       │   │                                     │
       │   │  docs/pmd → 定义"当前问题是什么"     │
       │   │  docs/prd → 定义"完整业务流程"       │
       │   │  docs/brd → 定义"具体业务场景"       │
       │   │  docs/drd → 定义"数据长什么样"       │
       │   │  docs/add → 定义"系统怎么搭"         │
       │   │  docs/ixd → 定义"页面怎么画"         │
       │   │  docs/dev → 定义"前后端怎么连"       │
       │   └─────────────────────────────────────┘
       │
       ▼
┌──────────────────────────────────────────────────────┐
│                    阅读顺序建议                       │
│                                                      │
│  1. README.md           ← 看清仓库里有什么            │
│  2. STATUS.md           ← 了解当前进度与差距          │
│  3. docs/index.md       ← 理解产品为什么存在          │
│  4. docs/pmd/index.md   ← 理解当前业务痛点            │
│  5. docs/prd/index.md   ← 理解完整交付流程            │
│  6. docs/drd/data.md    ← 理解核心数据模型            │
│  7. src/provider/docs/usage.md ← 启动服务端试试       │
│  8. tests/README.md     ← 了解测试怎么跑              │
│  9. AGENTS.md           ← 与 AI 协作前必读            │
└──────────────────────────────────────────────────────┘
```

**知识层级说明：**

- **docs/** — 程序性记忆，定义产品"做什么"：pmd 定义问题、prd 定义流程、brd 定义场景、drd 定义数据模型、add 定义系统架构、ixd 定义交互界面、dev 定义集成方案。从抽象到具体逐层展开。
- **src/** — 三个独立组件，分别实现不同切面：CLI 面向工程师（Markdown → 契约文件），Provider 面向数据存储（CRUD API），Studio 面向客户（可视化看板）。三者通过 REST API 解耦。
- **tests/** — 分层测试体系：基础层（进程启停）→ 工具层（窗口操作/截图/录屏）→ 用例层（业务序列）。下层不依赖上层，可独立替换。
- **examples/** — 不受文档章程约束的沙盒，用于快速验证想法。HTML 原型从 cloud2 → cloud4 → qtcloud 逐步演化，最终交给 Studio 代码实现。

## 核心功能

- [ ] 侧重回答核心功能是如何实现的，格式示例如下

```markdown

### xxx

```

## 最小工作流

- [ ] 示例如下

### 前置环境

- Git ≥ 2.20
- Python ≥ 3.10
- GitHub 账号并已配置 SSH

### 命令行

```bash
# ====== 第一步：克隆仓库 ======
git clone --recurse-submodules https://github.com/quanttide/quanttide-data.git
cd quanttide-data
# 如果克隆时忘了 --recurse-submodules，补执行：
# git submodule update --init --recursive

# ====== 第二步：查看项目状态 ======
# 阅读项目总览
cat README.md
# 了解当前各子模块版本
cat STATUS.md
# 查看贡献规则（重要！）
cat CONTRIBUTING.md

# ====== 第三步：安装 DevOps 工具 ======
# 确保 Python 可用
python --version
# 安装 qtcloud-devops 命令行工具
pip install qtcloud-devops

# ====== 第四步：环境自检 ======
qtcloud-devops source status    # 检查系统依赖是否齐全
qtcloud-devops status          # 查看项目整体状态

# ====== 第五步：尝试日常开发流程 ======
# 1. 代码审计（检查子模块同步、代码质量）
qtcloud-devops code audit

# 2. 查看构建和测试状态
qtcloud-devops build status
qtcloud-devops test status

# 3. 查看路线图进度
qtcloud-devops plan status
```

### 推荐探索路径

```
1. 先读 README.md        → 知道这个项目有哪些"零件"
2. 再读 CONTRIBUTING.md  → 知道"操作规则"是什么
3. 查看 STATUS.md        → 了解每个零件的当前版本
4. 浏览 .gitmodules      → 找到每个零件的远程仓库地址
5. 挑选一个子模块深入     → 如 docs/tutorial（入门教程）或 data/journal（工作日志）
```

### 常见操作速查

| 场景 | 命令 |
|------|------|
| 添加新子模块 | `git submodule add <url> <path>` |
| 更新全部子模块 | `git submodule update --remote` |
| 更新单个子模块 | `git submodule update --remote apps/qtdata` |
| 移除子模块 | `git submodule deinit -f <path> && git rm <path>` |
| 检查子模块是否同步 | `qtcloud-devops code status` |
| 查看发布预检结果 | `qtcloud-devops release audit` |
| 发布新版本 | `qtcloud-devops release publish` |