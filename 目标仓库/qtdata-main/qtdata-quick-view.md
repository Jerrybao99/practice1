# qtdata-quick-view

## 项目概述

量潮数据是一个**面向客户的数据服务交付平台**，核心解决客户"说不清需求、看不懂过程、信不过结果"的沟通翻译问题——把数据处理流程可视化给客户看，让客户从模糊的业务感觉走到确认的数据交付。

## 知识结构

### 顶层目录树

```
qtdata-main/                       ← 仓库根目录
├── src/                           ← 三大可执行组件
│   ├── cli/                       ← Rust 命令行工具，读 Markdown → LLM → 生成 CUE/JSON 契约文件
│   │   ├── src/main.rs            ← 入口：blueprint/scope/quotation/delivery 四个子命令
│   │   ├── Cargo.toml             ← Rust 依赖声明（clap + quanttide-agent）
│   │   ├── ROADMAP.md             ← CLI 四阶段开发计划
│   │   └── README.md              ← CLI 设计依据（工作空间 → 蓝图 → 范围 → 报价 → 交付）
│   ├── provider/                  ← Python FastAPI 服务端
│   │   ├── app/main.py            ← FastAPI 应用入口，挂载 Project/Task CRUD 路由
│   │   ├── app/storage.py         ← 内存字典存储 + 3 个 Demo 项目 + 15 个 Demo 任务
│   │   ├── pyproject.toml         ← Python 依赖（fastapi + fastapi-quanttide-project + uvicorn）
│   │   ├── docs/usage.md          ← 使用文档：如何启动、API 端点表、如何跑测试
│   │   ├── test/test_main.py      ← 集成测试（用 TestClient 测所有 CRUD + 健康检查）
│   │   └── ROADMAP.md             ← 数据处理 API + 资产管理 API 路线图
│   └── studio/                    ← Flutter 桌面/Web 客户端
│       ├── lib/main.dart          ← MaterialApp 入口，首页为 DashboardScreen
│       ├── lib/mock_data.dart     ← 单项目 Mock 数据（USPTO 商标匹配项目）
│       ├── lib/models/project.dart ← 数据模型：Project/Phase/DeliveryTarget/Blueprint
│       ├── lib/screens/           ← 两个页面：仪表盘 + 项目详情
│       ├── lib/components/        ← 可复用组件：进度条 + 项目卡片
│       ├── pubspec.yaml           ← Flutter 依赖（仅 SDK + cupertino_icons，无状态管理库）
│       ├── ROADMAP.md             ← 计划拆出 qtdata-data / qtdata-asset 两个 Dart 包
│       └── doc/data_screen.md     ← DataScreen 页面交互设计（PipelinePanel + DatasetPanel）
├── tests/                         ← 端到端测试框架
│   ├── conftest.py                ← 会话级 fixtures：Provider/Flutter 进程启停
│   ├── utils/                     ← 工具层
│   │   ├── base_page.py           ← BasePage：xdotool 窗口操作封装（click/type/screenshot/OCR 断言）
│   │   ├── screenshot.py          ← 全屏/窗口截图（mss + xdotool 定位）
│   │   ├── recorder.py            ← 屏幕录制（ffmpeg x11grab）
│   │   ├── test_base_page.py      ← BasePage 单元测试（Mock 驱动）
│   │   ├── test_screenshot.py     ← 截图函数单元测试
│   │   └── test_recorder.py       ← 录屏功能单元测试
│   ├── usecases/                  ← 用例层（业务序列，待实现）
│   ├── screens/ROADMAP.md         ← 页面层计划：ProjectScreen/DataScreen/AssetScreen Page Object
│   └── README.md                  ← 测试体系约定、分层设计、技术栈说明
├── docs/                          ← 工作文档（程序性记忆：定义"做什么"和"怎么做"）
│   ├── index.md                   ← **产品定位**：引导式数据服务的核心矛盾与四阶段产品形态
│   ├── pmd/index.md               ← **项目管理文档**：当前问题（群聊信息分散）→ 解决方案（信息窗口）
│   ├── prd/index.md               ← **用户故事地图**：5 阶段 12 步骤的需求到交付全流程
│   ├── brd/                       ← 业务需求文档
│   │   ├── index.md               ← 六大场景索引
│   │   └── scenario/              ← 五个业务场景：采集/获客/报价/业务线/交付
│   ├── add/asset.md               ← **资产模块架构**：客户门户 → 资产服务 → S3 三层结构
│   ├── ixd/                       ← 交互设计文档
│   │   ├── index.md               ← 客户门户功能概述
│   │   ├── screens/data_screen.md ← DataScreen 布局（Pipeline 横向流程 + Dataset 卡片网格）
│   │   └── views/                 ← 视图规格（PipelineView / TaskCard）
│   ├── drd/data.md                ← **数据模型文档**：工厂产量 Pipeline/Task/Dataset Schema + 状态枚举
│   └── dev/index.md               ← 前后端对接方案与启动命令
├── examples/                      ← 原型与示例（沙盒验证）
│   ├── prototype/                 ← HTML 交互原型（cloud2/3/4.html → qtcloud.html 逐步演化）
│   └── default/                   ← 默认示例 + 模块 PRD/技术设计文档
├── scripts/run-studio-linux.sh    ← Linux 下构建并启动 Flutter Studio 的一键脚本
├── AGENTS.md                      ← AI 工作纪律与沟通原则
├── STATUS.md                      ← 项目状态报告：版本历史、组件进度、战略差距分析
├── CHANGELOG.md                   ← 版本日志（v0.0.1/v0.0.2）
├── pyproject.toml                 ← 根级 Python 项目配置（E2E 测试依赖）
├── README.md                      ← 仓库结构总览
└── LICENSE                        ← Apache 2.0
```

### 关键文档

| 文档 | 角色 | 何时阅读 |
|------|------|----------|
| `README.md` | 仓库地图 | 第一次接触时 |
| `STATUS.md` | 项目体检报告 | 了解当前版本、进度、差距时 |
| `AGENTS.md` | AI 操作手册 | 使用 AI 辅助开发前 |
| `docs/index.md` | 产品灵魂 | 理解"这个产品到底解决什么问题"时 |
| `docs/pmd/index.md` | 问题定义 | 理解当前业务痛点和操作边界时 |
| `docs/prd/index.md` | 全流程剧本 | 理解客户与平台如何协同完成交付时 |
| `docs/drd/data.md` | 数据字典 | 理解 Pipeline/Task/Dataset 三模型关系时 |
| `docs/add/asset.md` | 资产架构 | 理解交付物如何存储、版本化、流转时 |
| `docs/ixd/screens/data_screen.md` | 页面蓝图 | 理解前端页面布局逻辑时 |
| `docs/dev/index.md` | 对接指南 | 需要启动前后端联合调试时 |
| `docs/brd/scenario/data_collection.md` | 用户故事 | 理解客户真实场景（从几百G压缩包到清洗数据）时 |
| `docs/brd/scenario/delivery.md` | 交付规范 | 理解交付文档体系（数据集/处理器/工程文档）时 |
| `tests/README.md` | 测试约定 | 写测试用例或阅读测试代码前 |
| `src/provider/docs/usage.md` | 服务端速查 | 启动 Provider 或查看 API 时 |
| `src/cli/README.md` | CLI 速查 | 理解 CLI 设计理念和命令结构时 |
| `src/studio/ROADMAP.md` | 客户端计划 | 了解 Flutter 端下一步要做什么时 |

### 知识层级关系

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

### 数据处理流程可视化（Pipeline + Dataset）

项目核心是一个五步数据处理流水线（导入 → 清洗 → 合并 → 计算 → 报表），每一步对应一个 Task 和一对输入/输出 Dataset：

```
外部文件 → raw-output-data → cleanse → cleaned-output-data → merge
→ merged-output-records → compute → output-analysis-results → report
```

Task 有 6 种状态（pending / inProgress / completed / failed / rejected / cancelled），Dataset 有 4 种状态（pending / ready / outdated / failed）。前端通过状态色驱动 UI 展示。

### 三大组件协作方式

```
┌───────────────────────────────────────────────────────────────┐
│ 工程师写 Markdown 需求文档                                      │
│       │                                                        │
│       ▼                                                        │
│  CLI (Rust) ── LLM 解析 ──→ 输出 CUE/JSON 契约文件              │
│                                 │                              │
│                                 ▼                              │
│  Provider (FastAPI) ←── REST API ──→ Studio (Flutter)           │
│  Project/Task CRUD                    仪表盘 / 项目详情          │
│  内存字典存储                          进度条 / 数据蓝图         │
│  Demo: 3 项目 15 任务                 交付物 / 时间线            │
└───────────────────────────────────────────────────────────────┘
```

- **CLI**：读 Markdown → 调 DeepSeek API → 输出 `blueprint.cue` / `scope.json` / `quotation.json`
- **Provider**：暴露 `/health`、`/projects` CRUD、`/tasks` CRUD，底层是内存字典
- **Studio**：Flutter Material 3 客户端，目前使用本地 Mock 数据（一个 USPTO 商标匹配项目），计划对接 Provider API

## 最小工作流

### 前置环境

- Python ≥ 3.12（Provider 和测试依赖）
- Rust 工具链（CLI 编译）
- Flutter SDK ≥ 3.18（Studio 运行）
- 平台工具：xdotool / ffmpeg（E2E 测试，仅 Linux）

### 命令行

```bash
# ====== 第一步：克隆仓库 ======
git clone https://github.com/quanttide/qtdata.git
cd qtdata

# ====== 第二步：启动 Provider（终端 1）=======
cd src/provider
uv sync                                    # 安装 Python 依赖
uv run uvicorn app.main:app --reload --port 8000
# 访问 http://127.0.0.1:8000/docs 查看交互式 API

# ====== 第三步：启动 Studio（终端 2）=======
cd src/studio
flutter pub get                            # 安装 Dart 依赖
flutter run -d linux                       # Linux 桌面运行
# 或 flutter run -d chrome                 # Web 运行

# ====== 第四步：运行 Provider 单元测试 ======
cd src/provider
uv run pytest test/ -v                     # 运行服务端测试（9 个用例）

# ====== 第五步：编译并运行 CLI ======
cd src/cli
cargo build                                # 编译 Rust CLI
cargo run -- blueprint -i input.md -o blueprint.cue   # 试运行蓝图命令

# ====== 第六步：运行 E2E 测试（需 Linux 桌面环境）======
cd tests
uv run pytest utils/ -v                    # 先跑工具层单元测试
# uv run pytest conftest.py               # 需要真实 Flutter 窗口环境
```

### 推荐探索路径

```
1. 读 docs/index.md           → 理解产品为什么存在
2. 读 docs/pmd/index.md       → 理解当前要解决的真实业务痛点
3. 读 docs/prd/index.md       → 理解完整交付流程（5 阶段 12 步骤）
4. 启动 Provider + 浏览 /docs → 亲手调一下 Project/Task API
5. 启动 Studio                → 看看前端长什么样（仪表盘 + 项目详情）
6. 跑 test/                   → 了解测试怎么分层、怎么写
7. 读 STATUS.md               → 看看还有多少活没干
```

### 常见操作速查

| 场景 | 命令 |
|------|------|
| Provider 启动 | `cd src/provider && uv run uvicorn app.main:app --reload` |
| Provider 测试 | `cd src/provider && uv run pytest test/ -v` |
| Studio 构建 Linux | `cd src/studio && flutter build linux` |
| Studio 一键运行 | `bash scripts/run-studio-linux.sh` |
| CLI 编译 | `cd src/cli && cargo build` |
| CLI 运行蓝图命令 | `cargo run -- blueprint -i <md文件> -o <输出.cue>` |
| E2E 工具层测试 | `cd tests && uv run pytest utils/ -v` |
| 安装所有 Python 依赖 | `cd tests && uv sync` |
| 查看 API 文档 | 浏览器打开 `http://127.0.0.1:8000/docs` |
