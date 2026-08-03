## 1. 从命令行到平台：蓝图结构化建模与可视化

- 背景/动机：蓝图是连接商务与技术的核心交付物，当前仅CLI支持Markdown→JSON转换，平台侧缺乏结构化存储与可视化入口。
- 关键证据：
  - 日志：2026-07-10 指出蓝图应为非技术岗可操作的"施工图纸"，"让商务可以大量地生成这种计划资料"
  - 代码：`src/studio/lib/models/project.dart` 已定义 Blueprint/BlueprintStep/BlueprintException 结构体，但 `src/provider/app/main.py` 无对应 CRUD 端点
- 设计实现：
  - **平台**：Provider + Studio
  - **工具**：复用 `src/cli/src/main.rs` Blueprint 子命令的 Markdown→CUE 解析逻辑，复用 `src/studio/lib/screens/project_detail_screen.dart` 已有蓝图展示区域
  - 具体实现：在 `src/provider/app/main.py` 新增 Blueprint 路由（创建/更新/查询），存储步骤与异常处理策略；Studio 蓝图区域从 mock 数据切换为 API 调用
- 设计原则：
  1. 非技术可读：蓝图输出支持商务友好语言模式
  2. 结构化为约束：蓝图字段即 AI Agent 执行边界
  3. 版本可追溯：每次修改自动保存版本快照

## 2. 交付记录与质量报告在线化

- 背景/动机：当前交付物以聊天群口头通知为主，缺乏结构化记录与质量可视化，客户难以追溯和验收。
- 关键证据：
  - 日志：2026-07-27 旅程5 指出"客户问往期交付内容很难快速查到；客户质疑数据质量拿不出凭证"
  - 代码：`src/provider/app/storage.py` 中 Task 模型仅有状态字段，无交付物文件关联、质量指标与验收状态
- 设计实现：
  - **平台**：Provider + Studio
  - **工具**：复用 `examples/prototype/cloud2.html` 交付记录列表 UI 设计，复用 `src/provider/app/main.py` 现有 TaskRouter 模式
  - 具体实现：在 `src/provider/app/main.py` 新增 Delivery 模型（时间戳、文件名、格式、质量报告链接、验收状态枚举），Studio `lib/screens/project_detail_screen.dart` 交付目标区域接入 API
- 设计原则：
  1. 质量可见：契约指标达成情况以进度条直观展示
  2. 状态可溯：每次交付的时间戳与验收状态完整保留
  3. 非技术可操作：商务经理可发起重新交付而不依赖开发介入

## 3. 项目生命周期状态机数字化

- 背景/动机：5阶段项目流程在代码中仅有枚举定义，未实现状态流转约束与阶段门控，项目进度依赖人工同步。
- 关键证据：
  - 日志：2026-07-27 定义项目制服务五阶段（调研→谈判→实施→验收→复盘），每阶段有对应验收文档
  - 代码：`src/studio/lib/models/project.dart` 定义 ProjectPhase 枚举，`src/studio/lib/screens/dashboard_screen.dart` 渲染静态 mock 数据，`src/provider/app/storage.py` 的 demo project 无 phase 字段
- 设计实现：
  - **平台**：Provider + Studio
  - **工具**：复用 `src/studio/lib/models/project.dart` ProjectPhase 枚举，复用 `src/studio/lib/components/project_card.dart` 状态颜色映射
  - 具体实现：在 `src/provider/app/main.py` 为 Project 模型扩展 current_phase 与 phase_status 字段，新增阶段切换端点（含前置条件校验）；`src/studio/lib/screens/dashboard_screen.dart` 从 Provider API 拉取项目列表替代 mock 数据
- 设计原则：
  1. 状态驱动：项目卡片状态由后端数据决定，非前端硬编码
  2. 阶段门控：切换到下一阶段需满足前置条件（如上一阶段文档已归档）
  3. 全员可见：仪表盘展示所有项目当前阶段与进度，商务和技术共享同一视图
