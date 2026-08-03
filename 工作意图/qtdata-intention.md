## 1. 客户项目信息看板

- 背景/动机：客户当前依赖群聊获取项目进展，信息分散无追溯，需通过产品化信息窗口实现透明化。
- 关键证据：
  - 日志：`default\2026-07-27.md` 旅程4"查看执行进度"——客户频繁询问进度，需看板展示状态百分比与异常标注。
  - 代码：`docs\pmd\index.md` 定义核心矛盾为"信息不透明"，方案为"信息窗口"同步业务信息；`src\studio\lib\screens\` 已有 DashboardScreen 但仅用 Mock 数据。
- 设计实现：
  - **平台**：Provider (FastAPI) + Studio (Flutter)
  - **工具**：`src\provider\app\main.py`（Project/Task CRUD）、`src\provider\app\storage.py`（内存字典 + Demo 数据）
  - 具体实现：Provider 新增 `/projects/{id}/progress` 端点（含进度百分比、状态时间轴、异常标注）；Studio `DashboardScreen` 移除 `mock_data.dart` 依赖，以 TaskStatus/DatasetStatus 枚举驱动 UI 状态色与进度条，对齐 `docs\ixd\screens\data_screen.md` 布局。
- 设计原则：
  1. 只做展示不做写入——与 `docs\pmd\index.md` 操作边界一致。
  2. 状态驱动 UI——`docs\drd\data.md` 中 TaskStatus/DatasetStatus 枚举直接映射前端组件样式。
  3. 渐进对接——先通 Provider→Studio 数据链路，再迭代交互细节。

## 2. 数据蓝图标准化生成

- 背景/动机：商务经理需能使用数据蓝图与客户沟通需求规格，而非依赖工程师每次从零编写。
- 关键证据：
  - 日志：`default\2026-07-10.md`——蓝图定义为"施工图纸"，非技术岗应能参与生成；Catalog 与 Contract 需匹配对应。
  - 代码：`src\cli\src\main.rs` 已实现 `blueprint` 子命令（Markdown→CUE），但 `default\2026-07-12.md` 记录缺 cue 工具时 panic 崩溃。
- 设计实现：
  - **平台**：CLI (Rust)
  - **工具**：`src\cli\` 中 `quanttide-agent` crate（调 DeepSeek API）
  - 具体实现：修复 `blueprint`/`contract list` 命令 cue 缺失导致 panic（Result 报友好提示）；在 `blueprint` 输出增加"商务可读摘要"字段（非技术语言翻译技术指标）；实现 Catalog 字段到 Contract 模板的自动映射。
- 设计原则：
  1. 输入容忍模糊——接受非技术语言 Markdown，由 LLM 补全结构化字段。
  2. 输出分双模式——工程模式（CUE/JSON）与商务模式（摘要文本）。
  3. 优雅降级——缺工具时提示安装而非崩溃。

## 3. 数据交付资产追踪

- 背景/动机：交付物散落于群聊文件与网盘链接中，无版本追溯与质量报告，客户验收缺乏凭据。
- 关键证据：
  - 日志：`default\2026-07-28.md`——数字化第二步为"GitHub 私有仓库 → S3 对象存储"，需资产治理与版本一致性。
  - 代码：`docs\add\asset.md` 定义三层架构（客户门户→资产服务→S3）及状态流转（待处理→处理中→待验收→已验收→已归档），Provider 目前仅有 Project/Task 模型，无 Asset 模型。
- 设计实现：
  - **平台**：Provider (FastAPI)
  - **工具**：`src\provider\app\storage.py`（内存字典模式）、`src\provider\app\main.py`（FastAPI 路由）
  - 具体实现：新增 `Asset` 数据模型（id/name/title/type/version/s3_key/status），参照 `docs\drd\data.md` Dataset 模式；新增 `/projects/{id}/assets` CRUD 路由与状态流转逻辑；Studio 新增交付记录列表组件对接 Asset API。
- 设计原则：
  1. 模型先行——先定义 Asset 数据结构对齐 DRD，再实现 API。
  2. S3 引用而非搬运——Asset 只存 S3 key 引用，不复制文件内容。
  3. 状态单向流转——遵循 `docs\add\asset.md` 状态机，不可逆向跳转。
