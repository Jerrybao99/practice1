# qtdata 项目工程路线图

## M0 项目骨架

### Step 0-1 修复 CLI blueprint 命令 crash

- 涉及文件：`src/cli/src/main.rs`
- 实现需求：
  - 意图2-数据蓝图标准化生成：修复 `blueprint`/`contract list` 命令在缺少 cue 工具时 panic 崩溃，Result 报友好提示而非崩溃（来源于 `default\2026-07-12.md` 日志记录）
  - 设计哲学6-可版本化：CLI 错误处理应符合工程标准，优雅降级
- 关键证据：日志 `default\2026-07-12.md`——缺 cue 工具时 panic 崩溃

- [ ] 操作
	- [ ] 检查 `blueprint` 子命令中调用外部 cue 工具的代码路径
	- [ ] 将 panic/unwrap 替换为 Result 类型，缺工具时输出友好提示信息（如 "cue 工具未安装，请运行 `brew install cue` 安装"）
	- [ ] 同步检查 `contract list` 命令是否存在同样的 cue 依赖问题
- [ ] 测试
	- [ ] 单元测试：模拟 cue 工具不可用的场景，验证 CLI 返回友好错误而非 panic
	- [ ] 单元测试：验证 cue 工具正常时的原有功能不受影响
- [ ] 验收
	- [ ] 在未安装 cue 的环境中运行 `cargo run -- blueprint <file>`，确认输出友好提示而非 panic
	- [ ] 在安装 cue 的环境中运行原有用例，确认功能正常
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "fix(cli): blueprint 命令缺 cue 工具时输出友好提示而非 panic"

### Step 0-2 Provider 单元测试基础

- 涉及文件：`src/provider/test/`（新建测试文件）
- 实现需求：
  - 模板要求：先单元测试，后代码，最后集成测试
  - 为现有 Provider Project/Task CRUD 路由建立单元测试覆盖，确保后续开发有回归安全网
  - 工程标准：Provider 作为坐标系上的实现点，需要可验证的质量基线
- 关键证据：STATUS.md——Provider v0.0.1 CRUD 骨架已实现但无测试

- [ ] 操作
	- [ ] 阅读 `src/provider/app/main.py` 和 `src/provider/app/storage.py` 确认现有 API 接口
	- [ ] 创建 `src/provider/test/test_projects.py`，覆盖 Project CRUD（创建/列表/获取/更新/删除）
	- [ ] 创建 `src/provider/test/test_tasks.py`，覆盖 Task CRUD（创建/列表/获取/更新/删除）
	- [ ] 使用 FastAPI TestClient + pytest 编写，内存字典作为测试后端
- [ ] 测试
	- [ ] 所有 CRUD 操作的正常路径测试
	- [ ] 边界情况：不存在的资源返回 404、无效参数返回 422
- [ ] 验收
	- [ ] `pytest src/provider/test/` 全部通过
	- [ ] 测试覆盖所有现有端点
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "test(provider): 为 Project/Task CRUD 端点添加单元测试"

### Step 0-3 Studio 单元测试基础

- 涉及文件：`src/studio/test/`（新建测试文件）
- 实现需求：
  - 模板要求：先单元测试，后代码，最后集成测试
  - 为 Studio 现有 DashboardScreen（Mock 数据驱动）建立 widget 测试
  - 设计哲学4-不设冗余抽象：测试直接验证现有组件行为
- 关键证据：意图1——`src\studio\lib\screens\` 已有 DashboardScreen 但仅用 Mock 数据

- [ ] 操作
	- [ ] 阅读 `src/studio/lib/screens/` 中现有 DashboardScreen 实现
	- [ ] 创建 `src/studio/test/widget_test.dart`，覆盖 DashboardScreen 渲染（验证四列看板展示）
	- [ ] 创建 widget 测试验证 TaskStatus/DatasetStatus 枚举到 UI 组件的映射
- [ ] 测试
	- [ ] DashboardScreen 四列看板渲染测试
	- [ ] 状态颜色映射测试（pending/inProgress/completed/failed/rejected/cancelled）
- [ ] 验收
	- [ ] `flutter test test/` 全部通过
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "test(studio): 为 DashboardScreen 添加 widget 测试"

## M1 客户项目信息看板

### Step 1-1 Provider 进度端点单元测试

- 涉及文件：`src/provider/test/test_progress.py`（新建）
- 实现需求：
  - 意图1-客户项目信息看板：Provider 新增 `/projects/{id}/progress` 端点（含进度百分比、状态时间轴、异常标注）
  - 设计原则1-只做展示不做写入：端点仅返回 GET 数据，不接受 POST/PUT/DELETE
  - 设计原则2-状态驱动 UI：TaskStatus/DatasetStatus 枚举直接驱动前端组件
- 关键证据：`docs\pmd\index.md`——操作边界为"只做信息展示"；`docs\drd\data.md`——TaskStatus、DatasetStatus 枚举定义

- [ ] 操作
	- [ ] 创建 `src/provider/test/test_progress.py`
	- [ ] 编写测试用例：GET `/projects/{id}/progress` 返回项目进度对象
	- [ ] 验证响应 Schema：`{ project_id, progress_percent, task_timeline: [{task_id, task_title, status, updated_at}], anomaly_flags: [{task_id, reason}] }`
	- [ ] 测试 404（不存在的项目）
	- [ ] 测试空项目（无任务时进度为 0%）
- [ ] 测试
	- [ ] 正常项目返回进度百分比和时间轴
	- [ ] 异常标注正确标记 failed/rejected 状态的任务
	- [ ] 不存在的项目返回 404
- [ ] 验收
	- [ ] 测试先行——此时端点尚未实现，测试全部红灯
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "test(provider): 添加项目进度端点 GET /projects/{id}/progress 测试"

### Step 1-2 Provider 进度端点实现

- 涉及文件：`src/provider/app/main.py`、`src/provider/app/storage.py`
- 实现需求：
  - 意图1-客户项目信息看板：实现进度端点，计算项目整体完成百分比，生成任务状态时间轴，标注异常任务
  - 设计原则2-状态驱动 UI：返回的 status 字段使用 DRD 定义的枚举值
- 关键证据：`docs\drd\data.md`——TaskStatus 枚举（pending/inProgress/completed/failed/rejected/cancelled）、DatasetStatus 枚举（pending/ready/outdated/failed）

- [ ] 操作
	- [ ] 在 `storage.py` 中添加 `get_project_progress(project_id)` 方法
	- [ ] 进度百分比计算：completed 任务数 / 总任务数
	- [ ] 时间轴生成：按任务创建时间排序，记录每次状态变更时间
	- [ ] 异常标注：收集所有 status 为 failed/rejected 的任务
	- [ ] 在 `main.py` 中添加路由 `GET /projects/{project_id}/progress`
- [ ] 测试
	- [ ] 运行 Step 1-1 编写的单元测试，确认全部绿灯
- [ ] 验收
	- [ ] `pytest src/provider/test/test_progress.py` 全部通过
	- [ ] 手动 `curl http://localhost:8000/projects/1/progress` 验证响应格式
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "feat(provider): 实现项目进度端点 GET /projects/{id}/progress"

### Step 1-3 Studio DashboardScreen 对接真实数据

- 涉及文件：`src/studio/lib/screens/dashboard_screen.dart`
- 实现需求：
  - 意图1-客户项目信息看板：Studio `DashboardScreen` 移除 `mock_data.dart` 依赖，对接 Provider API 获取真实进度数据
  - 设计原则3-目录结构是最高优先级：遵循现有 `lib/screens/` 目录结构
  - 设计原则1-边界意识：规范不绑实现，Dashboard 只消费 Provider 数据，不定义数据模型
- 关键证据：意图1——`DashboardScreen` 移除 `mock_data.dart` 依赖，以 TaskStatus/DatasetStatus 枚举驱动 UI 状态色与进度条

- [ ] 操作
	- [ ] 创建 `src/studio/lib/services/provider_client.dart`，封装对 Provider API 的 HTTP 调用
	- [ ] 在 `dashboard_screen.dart` 中移除 Mock 数据导入，改为调用 `provider_client.getProgress(projectId)`
	- [ ] 使用 TaskStatus 枚举映射看板列状态颜色：
		- pending → 灰色、inProgress → 蓝色、completed → 绿色、failed → 红色、rejected → 橙色、cancelled → 灰色线
	- [ ] 使用进度百分比渲染进度条组件
	- [ ] 展示异常标注列表（failed/rejected 任务及其原因）
- [ ] 测试
	- [ ] 更新 widget 测试：Mock Provider API 响应，验证看板正确展示真实数据格式
	- [ ] 测试各状态下的颜色和进度条渲染
- [ ] 验收
	- [ ] 启动 Provider + Studio，Dashboard 展示 Demo 数据（来自 `storage.py` 的预置数据）
	- [ ] 看板四列根据任务状态正确分流
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "feat(studio): DashboardScreen 对接 Provider 真实进度数据，移除 Mock 依赖"

### Step 1-4 数据页面端到端集成测试

- 涉及文件：`tests/usecases/test_data_screen.py`（新建）
- 实现需求：
  - 模板要求：集成测试为每个里程碑收尾
  - 意图1-客户项目信息看板：验证 Provider→Studio 完整数据链路
  - 设计哲学3-三端同步：验证服务端与客户端协同工作
- 关键证据：`docs\ixd\screens\data_screen.md`——数据页面交互设计布局

- [ ] 操作
	- [ ] 创建 `tests/usecases/test_data_screen.py`
	- [ ] 编写 E2E 用例：启动 Provider → 创建项目与任务 → 启动 Studio → 验证 Dashboard 展示正确项目进度
	- [ ] 使用 `tests/utils/` 截图工具在关键步骤截图
	- [ ] 验证异常标注在 UI 上正确显示
- [ ] 测试
	- [ ] 完整链路：Provider CRUD → Progress API → Studio Dashboard 渲染
	- [ ] 状态变更后 Dashboard 实时更新
- [ ] 验收
	- [ ] E2E 测试通过，截图存档至 `assets/images/`
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "test(e2e): 数据页面 Provider→Studio 端到端集成测试"

## M2 数据交付资产追踪

### Step 2-1 Asset 数据模型单元测试

- 涉及文件：`src/provider/test/test_assets.py`（新建）
- 实现需求：
  - 意图3-数据交付资产追踪：新增 Asset 数据模型（id/name/title/type/version/s3_key/status）
  - 设计原则3-状态单向流转：遵循 `docs\add\asset.md` 状态机，不可逆向跳转
  - 设计原则1-模型先行：先定义 Asset 数据结构对齐 DRD，再实现 API
- 关键证据：`docs\add\asset.md`——三层架构（客户门户→资产服务→S3）及状态流转（待处理→处理中→待验收→已验收→已归档）

- [ ] 操作
	- [ ] 创建 `src/provider/test/test_assets.py`
	- [ ] 编写 Asset 数据模型测试：创建 Asset 实例，验证所有字段类型和默认值
	- [ ] 编写状态机测试：验证合法状态流转（pending→inProgress→pendingAcceptance→accepted→archived）
	- [ ] 编写状态机拒绝测试：验证非法跳转（如 accepted 不能回到 pending）抛出异常
	- [ ] 编写 S3 引用测试：Asset 只存 s3_key 字符串，不包含文件内容
- [ ] 测试
	- [ ] Asset 创建时字段正确性
	- [ ] 状态单向流转合法路径
	- [ ] 状态非法跳转被阻止
- [ ] 验收
	- [ ] 测试先行——红灯状态
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "test(provider): 添加 Asset 数据模型与状态机单元测试"

### Step 2-2 Asset 数据模型实现

- 涉及文件：`src/provider/app/models.py`（新建）、`src/provider/app/storage.py`
- 实现需求：
  - 意图3-数据交付资产追踪：实现 Asset 数据模型，参照 `docs\drd\data.md` Dataset 模式
  - 设计原则2-S3 引用而非搬运：Asset 只存 S3 key 引用，不复制文件内容
  - 设计原则3-状态单向流转：资产状态遵循固定状态机
- 关键证据：`docs\add\asset.md`——状态流转定义

- [ ] 操作
	- [ ] 创建 `src/provider/app/models.py`
	- [ ] 定义 `AssetStatus` 枚举：`pending | inProgress | pendingAcceptance | accepted | archived`
	- [ ] 定义 `Asset` 数据类：id/name/title/type/version/s3_key/status/created_at/updated_at
	- [ ] 实现 `Asset.transition_to(new_status)` 方法，内置状态机校验
	- [ ] 在 `storage.py` 中添加 `assets: dict` 字典存储和预置 Demo 数据
- [ ] 测试
	- [ ] 运行 Step 2-1 单元测试，确认全部绿灯
- [ ] 验收
	- [ ] 所有 Asset 模型测试通过
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "feat(provider): 实现 Asset 数据模型与状态机"

### Step 2-3 Asset CRUD 路由单元测试

- 涉及文件：`src/provider/test/test_asset_routes.py`（新建）
- 实现需求：
  - 意图3-数据交付资产追踪：新增 `/projects/{id}/assets` CRUD 路由
  - 设计原则3-状态单向流转：API 状态变更需遵循状态机约束
- 关键证据：`docs\add\asset.md`——需支持资产列表、创建、状态流转

- [ ] 操作
	- [ ] 创建 `src/provider/test/test_asset_routes.py`
	- [ ] 测试 `GET /projects/{id}/assets`——返回项目资产列表
	- [ ] 测试 `POST /projects/{id}/assets`——创建资产记录
	- [ ] 测试 `GET /projects/{id}/assets/{asset_id}`——获取单个资产详情
	- [ ] 测试 `PUT /projects/{id}/assets/{asset_id}/status`——状态流转（合法路径）
	- [ ] 测试 `PUT /projects/{id}/assets/{asset_id}/status`——状态流转（非法路径返回 422）
	- [ ] 测试 404 场景（不存在的项目、不存在的资产）
- [ ] 测试
	- [ ] CRUD 全部正常路径
	- [ ] 状态机约束在 API 层生效
	- [ ] 错误场景返回正确状态码
- [ ] 验收
	- [ ] 测试先行——红灯状态
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "test(provider): 添加 Asset CRUD 路由与状态流转单元测试"

### Step 2-4 Asset CRUD 路由实现

- 涉及文件：`src/provider/app/main.py`、`src/provider/app/storage.py`
- 实现需求：
  - 意图3-数据交付资产追踪：实现 Asset 完整 CRUD 与状态流转 API
  - 设计原则3-状态单向流转：状态变更时调用 `Asset.transition_to()` 校验
  - 设计原则2-S3 引用：创建资产时接受 s3_key 字符串，不处理文件上传
- 关键证据：`docs\add\asset.md`——三层架构中 Provider 作为资产服务层

- [ ] 操作
	- [ ] 在 `main.py` 中添加路由：
		- `GET /projects/{project_id}/assets`——资产列表（支持 status 过滤参数）
		- `POST /projects/{project_id}/assets`——创建资产
		- `GET /projects/{project_id}/assets/{asset_id}`——资产详情
		- `PUT /projects/{project_id}/assets/{asset_id}/status`——状态流转（body: `{"status": "xxx"}`）
	- [ ] 在 `storage.py` 中添加 assets 的 CRUD 方法
	- [ ] 状态流转端点调用 `Asset.transition_to()` 进行校验
- [ ] 测试
	- [ ] 运行 Step 2-3 单元测试，确认全部绿灯
- [ ] 验收
	- [ ] `pytest src/provider/test/test_asset_routes.py` 全部通过
	- [ ] 手动 `curl` 验证完整 CRUD 流程
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "feat(provider): 实现 Asset CRUD 路由与状态流转 API"

### Step 2-5 Studio 资产页面组件

- 涉及文件：`src/studio/lib/screens/asset_screen.dart`（新建）、`src/studio/lib/services/provider_client.dart`（扩展）
- 实现需求：
  - 意图3-数据交付资产追踪：Studio 新增交付记录列表组件对接 Asset API
  - 设计原则3-状态单向流转：资产状态在 Studio 仅展示，变更由 Provider API 完成
- 关键证据：`docs\add\asset.md`——交付物列表与状态展示需求

- [ ] 操作
	- [ ] 在 `provider_client.dart` 扩展 Asset API 调用方法
	- [ ] 创建 `asset_screen.dart`——交付物列表页面
	- [ ] 列表项展示：资产名称、类型标签、版本号、状态标签（颜色映射）
	- [ ] 详情卡片：展示 s3_key、创建时间、更新时间、状态时间轴
	- [ ] 在 main.dart 中添加资产页面路由
- [ ] 测试
	- [ ] 创建 `src/studio/test/asset_screen_test.dart`
	- [ ] widget 测试：Mock Provider API 响应，验证资产列表渲染
	- [ ] widget 测试：验证各状态颜色正确展示
- [ ] 验收
	- [ ] 启动 Provider + Studio，资产页面展示 Demo 数据
	- [ ] 状态标签颜色正确：pending=灰、inProgress=蓝、pendingAcceptance=黄、accepted=绿、archived=灰线
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "feat(studio): 添加资产页面交付物列表组件"

### Step 2-6 资产页面端到端集成测试

- 涉及文件：`tests/usecases/test_asset_screen.py`（新建）
- 实现需求：
  - 模板要求：集成测试收尾
  - 意图3-数据交付资产追踪：验证 Asset 完整链路
- 关键证据：`docs\add\asset.md`——交付物状态流转全流程

- [ ] 操作
	- [ ] 创建 `tests/usecases/test_asset_screen.py`
	- [ ] E2E 用例：启动 Provider → 创建项目 → 创建资产 → 变更资产状态 → 启动 Studio → 验证资产页面展示
	- [ ] 使用 `tests/utils/` 截图
- [ ] 测试
	- [ ] 资产创建到 Studio 展示全链路
	- [ ] 状态变更后 Studio 刷新展示
- [ ] 验收
	- [ ] E2E 测试通过
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "test(e2e): 资产页面 Provider→Studio 端到端集成测试"

## M3 数据蓝图标准化生成

### Step 3-1 Blueprint 输出增强单元测试

- 涉及文件：`src/cli/src/`（测试文件）
- 实现需求：
  - 意图2-数据蓝图标准化生成：在 `blueprint` 输出增加"商务可读摘要"字段
  - 设计原则4-输出分双模式：工程模式（CUE/JSON）与商务模式（摘要文本）
  - Blueprint 洞察：Blueprint = Catalog + Contract，三格式由同一事实源派生
- 关键证据：ghtorrent-retrospective.md——Blueprint 三格式模式（.md/.cue/.html），.cue 是唯一事实源

- [ ] 操作
	- [ ] 创建 `src/cli/tests/test_blueprint_output.rs`（或现有测试文件扩展）
	- [ ] 测试蓝图输出包含 `summary` 字段（非技术语言的技术指标翻译）
	- [ ] 测试蓝图输出包含完整 CUE 格式（工程模式）
	- [ ] 测试 Markdown 输入可正确提取 Catalog 字段和 Contract 约束
	- [ ] 测试 LLM 调用失败时优雅降级（返回错误提示，非 panic）
- [ ] 测试
	- [ ] 商务摘要字段存在且为非空中文文本
	- [ ] CUE 输出格式正确
	- [ ] 错误场景不 panic
- [ ] 验收
	- [ ] 测试先行——红灯状态
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "test(cli): 添加 blueprint 商务摘要输出与 Catalog-Contract 映射测试"

### Step 3-2 Blueprint 输出增强实现

- 涉及文件：`src/cli/src/main.rs`
- 实现需求：
  - 意图2-数据蓝图标准化生成：实现 Catalog 字段到 Contract 模板的自动映射
  - 设计原则4-输出分双模式：工程输出（CUE）+ 商务输出（摘要 Markdown）
  - 设计原则1-输入容忍模糊：接受非技术语言 Markdown，由 LLM 补全结构化字段
- 关键证据：blueprint-insight.md——Catalog 回答"当前有什么数据"，Contract 回答"需要什么数据"

- [ ] 操作
	- [ ] 在 `blueprint` 命令输出结构中新增 `summary` 字段
	- [ ] 实现 Catalog→Contract 映射逻辑：
		- 从 Markdown 提取数据目录描述 → 生成 Catalog 结构
		- 从 Markdown 提取需求约束 → 生成 Contract 约束
		- Catalog 中缺失字段由 LLM 推断补全
	- [ ] 商务摘要生成：将 CUE 中的技术指标（行数、列名、类型约束）翻译为中文业务描述
	- [ ] 输出分两文件：`blueprint.cue`（工程模式）+ `blueprint-summary.md`（商务模式）
- [ ] 测试
	- [ ] 运行 Step 3-1 单元测试，确认全部绿灯
- [ ] 验收
	- [ ] `cargo run -- blueprint examples/default/price-indexer.md`
	- [ ] 验证 `blueprint.cue` 包含完整结构定义
	- [ ] 验证 `blueprint-summary.md` 包含非技术人员可读的中文摘要
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "feat(cli): blueprint 输出增加商务可读摘要与 Catalog-Contract 自动映射"

### Step 3-3 Blueprint 三格式生成集成

- 涉及文件：`src/cli/src/main.rs`、`src/cli/src/blueprint.rs`（新建或扩展）
- 实现需求：
  - Blueprint 洞察：三格式 Blueprint（.md/.cue/.html）由同一事实源派生
  - ghtorrent-retrospective.md——".cue 是唯一的事实源，.md 是对 .cue 的人类可读展开，.html 是从 .cue 渲染的视图"
- 关键证据：ghtorrent-retrospective.md——"如果三格式由同一事实源派生，写 Blueprint 就可以简化为写 .cue，其余自动生成"

- [ ] 操作
	- [ ] 实现 `cue → markdown` 渲染：从 `.cue` 生成人类可读需求文档
	- [ ] 实现 `cue → html` 渲染：从 `.cue` 生成可视化对比页面（表格+流程图）
	- [ ] 添加 `blueprint export` 子命令：`blueprint export --format md|html`
	- [ ] 单事实源保证：`.md` 和 `.html` 均由 `.cue` 自动生成，不独立维护
- [ ] 测试
	- [ ] 单元测试：验证 `.cue` → `.md` 转换完整性
	- [ ] 单元测试：验证 `.cue` → `.html` 生成有效 HTML 结构
- [ ] 验收
	- [ ] `cargo run -- blueprint export --format md` 输出可读 Markdown
	- [ ] `cargo run -- blueprint export --format html` 输出有效 HTML
	- [ ] 修改 `.cue` 后重新导出，`.md` 和 `.html` 同步更新
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "feat(cli): blueprint 支持三格式导出（cue→md/html 自动生成）"

### Step 3-4 Blueprint 端到端集成测试

- 涉及文件：`tests/usecases/test_blueprint.py`（新建）
- 实现需求：
  - 模板要求：集成测试收尾
  - 意图2-数据蓝图标准化生成：验证 CLI blueprint 完整流程
- 关键证据：ghtorrent-retrospective.md——纯净复现与收敛迭代验证方法

- [ ] 操作
	- [ ] 创建 `tests/usecases/test_blueprint.py`
	- [ ] E2E 用例：准备测试 Markdown 输入 → 运行 CLI blueprint 命令 → 验证 CUE 输出正确 → 验证 summary 输出可读 → 验证三格式导出一致
	- [ ] 用例覆盖：正常输入、模糊输入（非技术语言）、缺字段输入
- [ ] 测试
	- [ ] 完整 blueprint 命令链路
	- [ ] 三格式一致性验证
	- [ ] 模糊输入容忍性验证
	- [ ] 缺 cue 工具时优雅降级验证
- [ ] 验收
	- [ ] E2E 测试通过
- [ ] 提交
	- [ ] 生成 commit 建议，形如 git commit -m "test(e2e): blueprint 命令端到端集成测试"
