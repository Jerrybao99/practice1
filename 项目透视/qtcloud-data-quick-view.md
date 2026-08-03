# qtcloud-data-quick-view

## 项目概述

量潮数据云是一个**数据工程全生命周期工具链**，将数据交付中从"理解客户需求"到"清洗处理"再到"交付结果"的碎片环节，通过 CLI + API + 前端三端协同串成可重复的标准化流水线。

## 知识结构

### 顶层目录树

```
qtcloud-data/                     ← 项目根目录
├── src/
│   ├── cli/                      ← Rust CLI 工具（数据工程命令行）
│   │   ├── src/
│   │   │   ├── main.rs           ← CLI 入口，13 个子命令路由
│   │   │   ├── lib.rs            ← 模块注册表
│   │   │   ├── blueprint_core.rs ← 纯逻辑层：prompt 模板、解析器、渲染器
│   │   │   ├── blueprint.rs      ← 蓝图查看命令（list/show）
│   │   │   ├── clarify.rs        ← 从聊天记录生成需求文档（DRD）
│   │   │   ├── design.rs         ← 从 DRD 生成契约和蓝图（LLM 辅助）
│   │   │   ├── spec.rs           ← Specification YAML 包装/校验（wrap/validate）
│   │   │   ├── implement.rs      ← 从蓝图生成 Python 代码实现
│   │   │   ├── review.rs         ← LLM 审计蓝图完整性
│   │   │   ├── process.rs        ← 编排完整流程（接收→处理→交付）
│   │   │   ├── transfer.rs       ← 文件收发（send/receive），6 个存储平台
│   │   │   ├── catalog.rs        ← 本地数据目录和文件登记
│   │   │   ├── doctor.rs         ← 本机 DataOps 环境诊断
│   │   │   ├── contract.rs       ← 契约定义查看
│   │   │   ├── pipeline.rs       ← 管道定义查看
│   │   │   ├── version.rs        ← 蓝图版本管理（git-based）
│   │   │   └── providers/        ← 存储提供商插件（Dropbox/百度/S3/SFTP 等）
│   │   ├── tests/                ← 集成测试和 CLI 黑盒测试
│   │   ├── docs/                 ← CLI 文档
│   │   │   ├── dev/              ← 开发者文档（架构/规范/发布）
│   │   │   ├── user/             ← 用户文档（安装/命令用法）
│   │   │   └── examples/         ← LLM 使用示例
│   │   ├── Cargo.toml            ← Rust 依赖声明
│   │   ├── CHANGELOG.md          ← CLI 版本演进历史
│   │   ├── ROADMAP.md            ← CLI 未来版本计划
│   │   └── TODO.md               ← CLI 按模块拆解的任务清单
│   │
│   ├── provider/                 ← Go 后端服务（HTTP API + Pipeline 引擎）
│   │   ├── cmd/                  ← 服务入口 main.go
│   │   ├── internal/
│   │   │   ├── api/              ← HTTP 路由和 handler
│   │   │   ├── pipeline/         ← Pipeline 执行引擎（状态机 + 线性步骤）
│   │   │   ├── provider/         ← 存储提供商接口和注册
│   │   │   ├── store/            ← job 持久化存储
│   │   │   └── specstore/        ← Blueprint/Specification YAML 加载和解析
│   │   ├── go.mod                ← Go 模块声明
│   │   ├── CHANGELOG.md          ← Provider 版本变更
│   │   ├── ROADMAP.md            ← Provider 版本计划
│   │   └── TODO.md               ← Provider 任务清单
│   │
│   └── studio/                   ← Flutter 前端控制台（Web/桌面/移动）
│       ├── lib/
│       │   ├── main.dart         ← 应用入口
│       │   ├── router.dart       ← 路由配置（6 个页面）
│       │   ├── theme.dart        ← 主题色和间距
│       │   ├── api/client.dart   ← Provider HTTP API 客户端
│       │   ├── screens/          ← 页面实现（仪表盘/传输/蓝图/管道/契约/任务）
│       │   └── widgets/          ← 布局组件（侧边栏）
│       ├── docs/                 ← 蓝图详情页设计方案
│       ├── pubspec.yaml          ← Flutter 依赖声明
│       └── ROADMAP.md            ← Studio v0.1.0 设计和发布计划
│
├── .agents/skills/               ← AI 技能配置
│   └── qtcloud-devops/SKILL.md   ← DevOps CLI 完整工作流手册
├── .github/workflows/            ← CI/CD 流水线（test-cli.yml / release-cli.yml）
├── .gitignore                    ← Git 忽略规则
├── .gitmessage                   ← Git 提交消息模板（Conventional Commits）
├── CONTRIBUTING.md               ← 贡献指南
├── LICENSE                       ← Apache 2.0
└── README.md                     ← 项目总览
```

### 关键文档

| 文档 | 角色 | 何时阅读 |
|------|------|----------|
| `README.md` | 项目全貌 | 第一次接触时 |
| `CONTRIBUTING.md` | 操作规则 | 开始贡献前 |
| `src/cli/README.md` | CLI 完整手册 | 需要了解 CLI 能力时 |
| `src/provider/README.md` | Provider 完整手册 | 需要了解后端 API 时 |
| `src/studio/ROADMAP.md` | Studio 设计和发布计划 | 需要了解前端开发方向时 |
| `src/cli/CHANGELOG.md` | CLI 版本演进历史 | 理解 CLI 能力变迁时 |
| `src/provider/CHANGELOG.md` | Provider 版本变更 | 理解 Provider 能力变迁时 |
| `src/cli/ROADMAP.md` | CLI 未来计划 | 了解待开发功能时 |
| `src/cli/docs/dev/specification.md` | Specification 契约定义 | 需要理解 CLI/Provider 数据格式时 |
| `.agents/skills/qtcloud-devops/SKILL.md` | DevOps 工作流手册 | 执行发布/审计/规划时 |
| `.gitmessage` | 提交消息模板 | 提交代码前 |

### 知识层级关系

```
                              ┌─────────────────────┐
                              │    qtcloud-data      │
                              │   （量潮数据云仓库）    │
                              └──────────┬──────────┘
                                         │
            ┌────────────────────────────┼────────────────────────────┐
            ▼                            ▼                            ▼
   ┌─────────────────┐        ┌─────────────────┐        ┌─────────────────┐
   │    src/cli/      │        │  src/provider/   │        │   src/studio/    │
   │   Rust CLI 工具   │        │   Go 后端服务     │        │  Flutter 前端    │
   │  (本地命令行)      │────────│  (HTTP API)      │────────│  (Web/桌面控制台) │
   └────────┬────────┘        └────────┬────────┘        └────────┬────────┘
            │                          │                          │
            ▼                          ▼                          ▼
   数据工程师在终端操作         CLI ↔ Provider 通过        产品经理/客户在浏览器
   设计→实现→执行→交付        Specification YAML 对齐      浏览蓝图和任务状态
            │                          │                          │
            │              ┌───────────┴───────────┐              │
            │              ▼                       ▼              │
            │     ┌──────────────┐        ┌──────────────┐       │
            │     │specstore/    │        │  pipeline/    │       │
            │     │ 加载和解析    │        │  执行引擎      │       │
            │     │ Blueprint    │───────▶│  状态机+步骤   │       │
            │     │ YAML         │        │               │       │
            │     └──────────────┘        └──────────────┘       │
            │                          │                          │
            └──────────────────────────┼──────────────────────────┘
                                       │
                                       ▼
                           ┌───────────────────────┐
                           │    阅读顺序建议        │
                           │                       │
                           │  1. README.md         │  ← 了解三大组件
                           │  2. src/cli/README.md  │  ← 掌握 CLI 全貌
                           │  3. CONTRIBUTING.md   │  ← 学会提交规则
                           │  4. src/provider/README.md│← 理解后端
                           │  5. src/studio/ROADMAP.md│← 了解前端设计方向
                           │  6. .agents/skills/     │  ← 掌握 DevOps 流程
                           └───────────────────────┘
```

**知识层级说明：**

- **src/cli/** — 面向数据工程师的本地命令行工具，覆盖从需求到交付的全流程，是项目中最成熟的部分（当前 v0.2.0）
- **src/provider/** — 面向服务的 HTTP API 后端，将 CLI 生成的 Blueprint YAML 作为输入，提供可调用的 Pipeline 执行能力
- **src/studio/** — 面向非技术人员的前端控制台，将 Provider 的数据以业务语言可视化展示，当前是原型阶段
- **CLI ↔ Provider 桥梁** — 两者通过 Specification YAML（`api_version/kind/spec` 信封格式）对齐数据契约，确保 CLI 设计的结果可以被 Provider 正确执行

## 核心功能

本项目实现了一条**从客户需求到数据交付的完整自动化流水线**：

1. **需求澄清**（`clarify`）：读取客户聊天记录 → LLM 提取业务需求 → 输出数据需求文档（DRD）
2. **规格设计**（`design`）：读取 DRD → LLM 生成 Contract（输入/输出契约）+ Blueprint（处理步骤）→ 输出结构化 YAML + Markdown + HTML
3. **规格固化**（`spec`）：将 Blueprint YAML 包装为 Kubernetes 风格的 Specification envelope，供 CLI 和 Provider 统一消费
4. **代码生成**（`implement`）：读取 Blueprint YAML → LLM 按步骤生成 Python 函数 → 组装为完整可执行脚本
5. **审计检查**（`review`）：LLM 审查 Specification 的完整性，输出结构化问题清单（严重/警告/建议三级）
6. **编排执行**（`process`）：接收原始数据 → 执行 Pipeline → 交付结果，自动记录 job 和 catalog
7. **环境诊断**（`doctor`）：检查本机 DataOps 环境，包括工具链、目录结构、传输凭证
8. **版本管理**（`version`）：基于 git 的 Blueprint 版本历史、查看、差异对比

**核心设计原则**：LLM 只输出 Markdown 表格（非结构化），CLI 代码确定性解析表格生成 YAML/代码。这避免了 LLM 直接生成 CUE/YAML 代码的不可控问题。

## 最小工作流

### 前置环境

- Rust ≥ 1.80（CLI 编译）
- Go ≥ 1.26（Provider 运行）
- Flutter ≥ 3.0（Studio 运行，可选）
- `cue` CLI 工具（部分子命令依赖）
- 各云存储平台的 Access Token（按需配置）

### 命令行

```bash
# ====== 第一步：克隆仓库 ======
git clone https://github.com/quanttide/qtcloud-data.git
cd qtcloud-data

# ====== 第二步：构建 CLI ======
cd src/cli
cargo build
cd ../..

# ====== 第三步：环境自检 ======
src/cli/target/debug/qtcloud-data doctor --no-fail
src/cli/target/debug/qtcloud-data doctor --fix-dirs    # 创建数据目录

# ====== 第四步：体验核心流程 ======
# 1. 从聊天记录生成需求文档
echo "客户是做电商的，需要清洗用户订单数据，去除重复记录" > /tmp/chat.txt
src/cli/target/debug/qtcloud-data clarify from-chat /tmp/chat.txt

# 2. 从需求文档生成契约和蓝图
src/cli/target/debug/qtcloud-data design contract .quanttide/data/drd/chat.md
src/cli/target/debug/qtcloud-data design blueprint .quanttide/data/drd/chat.md

# 3. 校验生成的规格
src/cli/target/debug/qtcloud-data spec validate .quanttide/data/spec/chat-blueprint.yaml

# 4. 从蓝图生成 Python 代码
src/cli/target/debug/qtcloud-data implement .quanttide/data/spec/chat-blueprint.yaml --lang python

# ====== 第五步：启动 Provider（可选） ======
cd src/provider
go run ./cmd/qtcloud-provider

# 另一终端测试 API
curl http://localhost:8080/version
curl http://localhost:8080/blueprints
```

### 推荐探索路径

```
1. 先读 README.md                  → 知道三大组件是干什么的
2. 再读 src/cli/README.md          → 掌握 CLI 的 13 个命令
3. 查看 src/cli/CHANGELOG.md       → 理解版本从哪里来、到哪里去
4. 阅读 src/provider/README.md     → 理解 Pipeline 状态机模型
5. 阅读 src/studio/ROADMAP.md      → 了解前端的 SRS 设计理念
6. 查看 .agents/skills/qtcloud-devops/SKILL.md → 掌握发布和审计流程
```

### 常见操作速查

| 场景 | 命令 |
|------|------|
| 构建 CLI | `cd src/cli && cargo build` |
| 运行 CLI 测试 | `cd src/cli && cargo test` |
| 运行 Provider | `cd src/provider && go run ./cmd/qtcloud-provider` |
| 运行 Provider 测试 | `cd src/provider && go test ./...` |
| 运行 Studio | `cd src/studio && flutter run` |
| 查看蓝图列表 | `qtcloud-data blueprint list` |
| 查看环境状态 | `qtcloud-data doctor --no-fail` |
| 发送文件到 Dropbox | `qtcloud-data transfer send ./file.pdf --output link.txt` |
| 执行端到端处理 | `qtcloud-data process CUSTOMER_ID "https://source-url" --blueprint csv-standard` |
| 代码审计（DevOps） | `qtcloud-devops code audit` |
