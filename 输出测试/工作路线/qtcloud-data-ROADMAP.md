# qtcloud-data 项目工程路线图

> 追溯来源：工作意图 `qtcloud-data-intention.md`（数据传输统一接口、端到端交付流程编排、交付记录追溯与目录管理）、工作洞察 `blueprint-insight.md`/`design-philosophy.md`/`engineering-standards.md`/`ghtorrent-retrospective.md`/`why-insight.md`、代码仓库 `qtcloud-data-main`。

## M0 项目骨架

> 已基本完成。CLI v0.2.0 已发布 crates.io，Provider v0.0.2 提供 HTTP API，Studio v0.1.0-alpha 可运行。

### Step 0-1 仓库初始化与模块划分

- 涉及文件：`.gitignore`、`.gitmessage`、`README.md`、`CONTRIBUTING.md`、`LICENSE`、`src/cli/`、`src/provider/`、`src/studio/`

- [ ] 操作
	- [ ] 创建 monorepo，划分 CLI（Rust）、Provider（Go）、Studio（Flutter）三模块
	- [ ] 编写项目级 README、CONTRIBUTING、LICENSE
	- [ ] 配置 .gitignore（Rust target/、Go vendor、Flutter build/）
	- [ ] 引入 `.agents/skills/qtcloud-devops/SKILL.md` 定义 DevOps 流程
- [ ] 测试
  - [ ] `cargo check` 通过（CLI）
  - [ ] `go vet ./...` 通过（Provider）
  - [ ] `flutter analyze` 通过（Studio）
- [ ] 验收
	- [ ] 三模块均可编译无报错
	- [ ] 贡献指南明确 Conventional Commits 规范
- [ ] 提交
  - [ ] `git commit -m "chore(repo): 初始化 qtcloud-data monorepo 项目骨架"`

### Step 0-2 CI/CD 流水线搭建

- 涉及文件：`.github/workflows/test-cli.yml`、`.github/workflows/release-cli.yml`

- [ ] 操作
	- [ ] 编写 CLI 测试 CI（cargo test + cargo clippy）
	- [ ] 编写 CLI 发布 CD（build binary + publish crates.io）
	- [ ] 预留 Provider 和 Studio 的 CI 配置文件占位
- [ ] 测试
  - [ ] Push 后 CI 自动触发并成功
- [ ] 验收
	- [ ] PR 合并前必须通过 CI
- [ ] 提交
  - [ ] `git commit -m "ci(github): 搭建 CLI 测试与发布流水线"`

## M1 数据传输统一接口标准化

> 追溯意图 #1：将零散的客户数据收发收敛为统一的 send/receive 命令。设计原则：先支持一个平台能用，不加抽象层；第二个平台接入时再抽象；send 成功后自动登记交付链接；凭证走环境变量。

### Step 1-1 StorageProvider trait 接口定义与单元测试

- 涉及文件：`src/cli/src/providers/mod.rs`

- [ ] 操作
	- [ ] 定义 `StorageProvider` trait：`name()`、`send()`、`receive()`、`receive_path()`
	- [ ] 编写 trait 方法的单元测试（mock provider）
	- [ ] 实现 `from_name` 工厂函数（Match `name` → 返回 `Box<dyn StorageProvider>`）
	- [ ] 实现 URL 检测函数 `detect(url: &str) -> Option<&'static str>`，根据 URL 前缀识别平台
- [ ] 测试
  - [ ] 单元测试：mock provider 实现全部 trait 方法
  - [ ] 单元测试：`from_name("dropbox")` 返回 DropboxProvider
  - [ ] 单元测试：`from_name("unknown")` 返回 None
  - [ ] 单元测试：`detect("https://www.dropbox.com/...")` 返回 "dropbox"
  - [ ] 单元测试：`detect("https://example.com/file")` 返回 None
- [ ] 验收
	- [ ] `cargo test -p qtcloud-data-cli --lib providers` 全部通过
	- [ ] 新增平台只需实现 trait + 注册 `from_name` + 扩展 `detect`
- [ ] 提交
  - [ ] `git commit -m "test(transfer): 定义 StorageProvider trait 并完成单元测试"`

### Step 1-2 Dropbox Provider 实现与集成测试

- 涉及文件：`src/cli/src/providers/dropbox.rs`、`src/cli/src/providers/mod.rs`

- [ ] 操作
	- [ ] 实现 `DropboxProvider` struct，通过 `DROPBOX_TOKEN` 环境变量获取凭证
	- [ ] 实现 `send(local_path, remote_path) -> Result<String>`：上传文件 → 创建共享链接 → 返回链接
	- [ ] 实现 `receive(url, local_path) -> Result<()>`：从共享链接下载文件到本地
	- [ ] 实现 `receive_path(remote, local) -> Result<()>`：按路径下载（单文件/文件夹递归）
	- [ ] 在 `from_name` 和 `detect` 中注册 Dropbox
- [ ] 测试
  - [ ] 单元测试：send 成功后返回有效 URL
  - [ ] 单元测试：receive 成功后文件存在于 local_path
  - [ ] 集成测试：`transfer send <file> dropbox` 端到端上传并获取链接
  - [ ] 集成测试：`transfer receive <dropbox-url>` 端到端下载并验证文件
- [ ] 验收
	- [ ] 成功上传文件到 Dropbox 并获取可访问的分享链接
	- [ ] 成功从 Dropbox 链接下载文件且内容一致
	- [ ] 凭证走环境变量，不硬编码在命令参数中
- [ ] 提交
  - [ ] `git commit -m "feat(transfer): 实现 Dropbox Provider 并与 CLI 集成"`

### Step 1-3 多平台 Provider 扩展（S3、百度网盘、Google Drive、OneDrive、SFTP）

- 涉及文件：`src/cli/src/providers/s3.rs`、`src/cli/src/providers/baidu_drive.rs`、`src/cli/src/providers/google_drive.rs`、`src/cli/src/providers/onedrive.rs`、`src/cli/src/providers/sftp.rs`、`src/cli/src/providers/mod.rs`

- [ ] 操作
	- [ ] 实现 `S3Provider`（`AWS_ACCESS_KEY_ID` + `AWS_SECRET_ACCESS_KEY` + `AWS_REGION`）
	- [ ] 实现 `BaiduDriveProvider`（`BAIDU_NETDISK_TOKEN`）
	- [ ] 实现 `GoogleDriveProvider`（`GOOGLE_DRIVE_TOKEN`）
	- [ ] 实现 `OneDriveProvider`（`ONEDRIVE_TOKEN`）
	- [ ] 实现 `SftpProvider`（`SFTP_HOST` + `SFTP_USER` + `SFTP_KEY`）
	- [ ] 在 `from_name` 和 `detect` 中注册所有新增平台
- [ ] 测试
  - [ ] 每个 Provider 的单元测试（mock HTTP/SFTP client）
  - [ ] 每个 Provider 的集成测试（需真实凭证，CI 中 skip）
- [ ] 验收
	- [ ] 六平台 Provider 全部注册到 `from_name` 工厂
	- [ ] 六平台 URL 识别规则全部注册到 `detect`
	- [ ] 各 Provider 凭证统一走环境变量
- [ ] 提交
  - [ ] `git commit -m "feat(transfer): 实现 S3、百度网盘、Google Drive、OneDrive、SFTP 五平台 Provider"`

### Step 1-4 交付链接自动登记

- 涉及文件：`src/cli/src/transfer.rs`

- [ ] 操作
	- [ ] `transfer send` 成功后自动将 `DeliveryLinkRecord` 追加写入 `delivery-links.json`
	- [ ] `delivery-links.json` 存储于 `DATA_ROOT/CATALOG_DIR/` 目录
	- [ ] 写入失败仅输出 warning，不阻断主流程
- [ ] 测试
  - [ ] 单元测试：send 成功后 `delivery-links.json` 新增一条记录
  - [ ] 单元测试：send 成功但写入失败时仅输出 warning，仍返回 Ok
  - [ ] 单元测试：多次 send 后记录正确追加，不覆盖已有记录
- [ ] 验收
	- [ ] `delivery-links.json` 格式为 `BTreeMap<id, DeliveryLinkRecord>`
	- [ ] 登记失败不反转 send 操作
- [ ] 提交
  - [ ] `git commit -m "feat(transfer): send 成功后自动登记交付链接到 delivery-links.json"`

## M2 端到端交付流程编排自动化

> 追溯意图 #2：将 receive→pipeline→send 串联为一条可复现的生产线。设计原则：编排失败不丢中间产物；source_url 自动脱敏；交付成功后自动登记 registry。

### Step 2-1 Pipeline 解析与状态机单元测试

- 涉及文件：`src/cli/src/pipeline.rs`、`src/cli/src/blueprint_core.rs`

- [ ] 操作
	- [ ] 从 `--blueprint` 指定的 CUE 文件解析 pipeline 字段（steps + states）
	- [ ] 支持两种格式：线性 steps 和状态机 states（含 `start_at`/`next`/`end`）
	- [ ] 按拓扑顺序解析状态机依赖，检测循环依赖
- [ ] 测试
  - [ ] 单元测试：解析线性 steps pipeline → 返回正确的步骤列表
  - [ ] 单元测试：解析状态机 pipeline → 返回正确的拓扑排序步骤
  - [ ] 单元测试：循环依赖检测 → 返回错误
  - [ ] 单元测试：未知 resource 类型 → 返回错误
- [ ] 验收
	- [ ] `pipeline list` 列出所有可用 pipeline
	- [ ] `pipeline show <name>` 显示步骤详情和依赖图
- [ ] 提交
  - [ ] `git commit -m "test(pipeline): 实现 Pipeline 解析器与状态机单元测试"`

### Step 2-2 Process 编排引擎实现与集成测试

- 涉及文件：`src/cli/src/process.rs`

- [ ] 操作
	- [ ] 实现 `process` 命令：`process <customer_id> <source_url> [--blueprint] [--pipeline]`
	- [ ] 步骤1 `receive`：调用 `transfer receive` 下载源数据到 `DATA_ROOT/raw/<customer_id>/`
	- [ ] 步骤2 `pipeline`：按解析后的 pipeline 逐步执行（`builtin:copy`、`python:<script>`、`bash:<script>`）
	- [ ] 步骤3 `send`：调用 `transfer send` 交付处理结果
	- [ ] 每步完成后记录日志到 `DATA_ROOT/logs/<customer_id>/<job_id>/`
	- [ ] 失败时保留已完成步骤的中间产物，状态标记为 `failed`
	- [ ] `source_url` 自动脱敏：去 query 和 fragment，只留 `scheme://host/path`
- [ ] 测试
  - [ ] 单元测试：source_url 脱敏 → `https://example.com/path?key=val#frag` → `https://example.com/path`
  - [ ] 单元测试：process 成功流程 → 验证 jobs.json 写入 `delivered` 状态
  - [ ] 单元测试：pipeline 某步失败 → 验证中间产物保留、jobs.json 状态为 `failed`
  - [ ] 集成测试：完整 process 流程 → receive → pipeline(2步) → send → 验证输出文件存在 + registry 登记
- [ ] 验收
	- [ ] `process` 命令接收 2 个必选参数 + 2 个可选参数
	- [ ] 交付成功后自动登记 `registry.json`，provider=process
	- [ ] 失败不丢中间产物，保留已完成步骤的输入输出
- [ ] 提交
  - [ ] `git commit -m "feat(process): 实现端到端编排引擎 receive→pipeline→send"`

### Step 2-3 Manifest 输入契约支持

- 涉及文件：`src/cli/src/process.rs`、`src/cli/src/spec.rs`

- [ ] 操作
	- [ ] 在 process 入口增加 `--manifest` 参数，支持输入 YAML 定义的多数据流
	- [ ] manifest 格式：包含 `inputs[]` 数组，每项含 `name`、`source_url`、`blueprint`
	- [ ] 单个 process 支持多输入数据流并发处理
	- [ ] 与 `spec wrap` 的 Specification 格式对齐（`api_version: "qtcloud.quanttide.com/v1alpha1"`）
- [ ] 测试
  - [ ] 单元测试：manifest 解析 → 返回正确的 inputs 列表
  - [ ] 单元测试：manifest 格式校验 → 缺少必填字段返回错误
  - [ ] 集成测试：process --manifest 多输入 → 每个输入独立完成 receive→pipeline→send
- [ ] 验收
	- [ ] manifest 与 spec 的 Specification envelope 格式完全对齐
	- [ ] 多输入数据流并发执行不互相干扰
- [ ] 提交
  - [ ] `git commit -m "feat(process): 支持 manifest 多输入数据流契约"`

## M3 交付过程的记录追溯与目录管理

> 追溯意图 #3：将交付活动的追踪能力嵌入命令行工具。设计原则：JSON registry 统一为 `BTreeMap<id, record>`；登记写入失败不阻断主流程；目录结构遵循 `.quanttide/data` 规范。

### Step 3-1 Catalog 管理命令与单元测试

- 涉及文件：`src/cli/src/catalog.rs`

- [ ] 操作
	- [ ] 实现 `catalog list`：列出所有数据卷，从 `registry.json` 读取
	- [ ] 实现 `catalog show <name>`：显示单个数据卷详情
	- [ ] 实现 `catalog add <path> [--name] [--provider] [--source]`：手动注册数据卷
	- [ ] 实现 `catalog rm <name>`：移除数据卷记录
	- [ ] 实现 `catalog list --status delivered`：按状态过滤
	- [ ] `Volume` 结构体字段：name、path、size、received_at、provider、source、status
- [ ] 测试
  - [ ] 单元测试：catalog add → registry.json 新增 Volume 记录
  - [ ] 单元测试：catalog list → 返回所有 Volume
  - [ ] 单元测试：catalog list --status delivered → 仅返回 status=delivered 的记录
  - [ ] 单元测试：catalog rm → registry.json 移除对应记录
  - [ ] 单元测试：catalog add 写入失败 → 仅输出 warning
- [ ] 验收
	- [ ] `registry.json` 格式为 `BTreeMap<id, Volume>`，便于追加和查询
	- [ ] 登记写入失败不阻断主流程，仅输出 warning
	- [ ] 目录结构遵循 `DATA_ROOT/CATALOG_DIR` 可覆盖
- [ ] 提交
  - [ ] `git commit -m "test(catalog): 实现 catalog 增删查命令与单元测试"`

### Step 3-2 Process 与 Catalog 联动（自动化登记）

- 涉及文件：`src/cli/src/process.rs`、`src/cli/src/catalog.rs`、`src/cli/src/transfer.rs`

- [ ] 操作
	- [ ] `transfer send` 成功后自动调用 `catalog add` 逻辑登记交付链接
	- [ ] `process` 成功后自动登记输出结果到 registry
	- [ ] `process` 的 `ProcessJobRecord` 状态变更同步更新 registry 中对应 Volume 的 status
- [ ] 测试
  - [ ] 集成测试：process 完整执行后 → registry 包含输入 + 输出两条 Volume 记录
  - [ ] 集成测试：process 失败后 → registry 中 Volume status 正确反映 failed
  - [ ] 集成测试：transfer send 后 → delivery-links.json + registry.json 均有记录
- [ ] 验收
	- [ ] 三大登记点（transfer send / process / catalog add）数据模型统一
	- [ ] provider=process 的记录可追溯完整交付链路
- [ ] 提交
  - [ ] `git commit -m "feat(catalog): process 与 transfer 执行自动登记 registry"`

## M4 Provider 服务对齐与 API 集成

> 追溯意图：Provider 读取 CLI 生成的 Specification YAML，提供 HTTP API 给 Studio 消费，实现三端（CLI/Provider/Studio）协同工作。

### Step 4-1 Provider 蓝图 CRUD API 与单元测试

- 涉及文件：`src/provider/internal/api/handler.go`、`src/provider/internal/specstore/specstore.go`

- [ ] 操作
	- [ ] `GET /blueprints`：列出 SPEC_DIR 下所有 Blueprint 摘要
	- [ ] `GET /blueprints/{name}`：返回单个 Blueprint 详情（含 pipeline states/steps）
	- [ ] `POST /blueprints/{name}/runs`：接收 `customer_id` + `input_path` + `work_dir` 执行 pipeline
	- [ ] Pipeline 执行器支持 `builtin:copy`、`python:<script>`、`bash:<script>` 三种 resource
- [ ] 测试
  - [ ] 单元测试：specstore 读取 YAML → 正确解析 Bleuprint 结构
  - [ ] 单元测试：handler GET /blueprints → 返回 JSON 数组
  - [ ] 单元测试：handler GET /blueprints/{name} → 返回完整 Blueprint
  - [ ] 单元测试：handler POST /blueprints/{name}/runs → 执行 pipeline 并返回 job id
  - [ ] 单元测试：pipeline 执行 builtin:copy → 文件从 input 复制到 output
- [ ] 验收
	- [ ] Provider 读取的 YAML 与 CLI `spec wrap` 生成的格式完全一致
	- [ ] Pipeline 执行结果写入 JobRecord store
- [ ] 提交
  - [ ] `git commit -m "test(provider): 实现蓝图 CRUD API 与 Pipeline 执行器单元测试"`

### Step 4-2 Provider 传输 API 实现

- 涉及文件：`src/provider/internal/api/handler.go`、`src/provider/internal/provider/dropbox.go`、`src/provider/internal/provider/s3.go`

- [ ] 操作
	- [ ] `POST /transfer/send`：接收 `provider` + `local_path` + `remote_path`，返回分享链接
	- [ ] `POST /transfer/receive`：接收 `provider` + `url` + `local_path`，下载文件
	- [ ] 实现 Dropbox Provider（Go 版，对齐 Rust CLI 的 StorageProvider trait）
	- [ ] 实现 S3 Provider（Go 版）
- [ ] 测试
  - [ ] 单元测试：Dropbox send mock → 返回模拟 URL
  - [ ] 单元测试：Dropbox receive mock → 模拟下载
  - [ ] 集成测试：Provider 启动后 curl POST /transfer/send → 返回链接
- [ ] 验收
	- [ ] Go Provider 接口签名与 Rust StorageProvider trait 对齐
	- [ ] Provider 传输 API 与 CLI transfer 命令行为一致
- [ ] 提交
  - [ ] `git commit -m "feat(provider): 实现传输 API 与 Dropbox/S3 Provider"`

### Step 4-3 Provider 任务可观测性

- 涉及文件：`src/provider/internal/store/store.go`、`src/provider/internal/api/handler.go`

- [ ] 操作
	- [ ] `GET /process/jobs`：返回所有任务记录列表，支持 `?status=xxx` 过滤
	- [ ] `GET /process/jobs/{id}`：返回单个任务详情（含 step-level 日志）
	- [ ] 失败任务记录 stdout/stderr 摘要到 JobRecord
	- [ ] 保留每个 step 的输入/输出路径用于排查
- [ ] 测试
  - [ ] 单元测试：store 写入 JobRecord → 读取验证
  - [ ] 单元测试：handler GET /process/jobs → 返回 JSON 数组
  - [ ] 单元测试：handler GET /process/jobs/{id} → 包含 Steps 详情
  - [ ] 单元测试：pipeline 执行失败 → JobRecord 含 error 字段
- [ ] 验收
	- [ ] 失败任务的 stdout/stderr 完整保留，可定位失败步骤
	- [ ] JobRecord 与 CLI `ProcessJobRecord` 字段对齐
- [ ] 提交
  - [ ] `git commit -m "feat(provider): 实现任务记录查询与失败可观测性"`

### Step 4-4 Provider 集成测试与契约对齐

- 涉及文件：`src/provider/internal/api/handler.go`、`src/provider/internal/pipeline/pipeline.go`、`src/provider/internal/specstore/specstore.go`

- [ ] 操作
	- [ ] 验证 Provider-run 产物（JobRecord）与 CLI catalog（Volume）字段契约一致
	- [ ] 验证 Provider Pipeline 状态机与 CLI pipeline 解析器输出一致
	- [ ] 编写端到端集成测试：CLI spec wrap → Provider 读取 YAML → 执行 pipeline → 查询 job
- [ ] 测试
  - [ ] 集成测试：CLI 生成 YAML → Provider GET /blueprints 可读取
  - [ ] 集成测试：Provider POST /blueprints/{name}/runs → 执行结果持久化
  - [ ] 集成测试：多次执行后 job list 正确累积
- [ ] 验收
	- [ ] CLI 与 Provider 的 Blueprint/Pipeline/Job 数据模型完全对齐
	- [ ] Spec v0.0.3 升级后 CLI 和 Provider 同步升级
- [ ] 提交
  - [ ] `git commit -m "test(provider): 端到端集成测试与 CLI-Provider 契约对齐"`

## M5 Studio 前端集成与 AI 智能体

> 追溯意图 + 洞察：Blueprint 三格式（.md/.cue/.html）由 CUE 单一事实源派生；AI Agent 在 Catalog/Pipeline/Contract 框架约束下工作；把技术语言翻译成业务语言赋能非技术岗位。

### Step 5-1 Studio 蓝图页面与 Provider API 对接

- 涉及文件：`src/studio/lib/screens/blueprints.dart`、`src/studio/lib/screens/blueprint_detail.dart`、`src/studio/lib/api/client.dart`

- [ ] 操作
	- [ ] 蓝图列表页对接 `GET /blueprints` API（替换硬编码 mock 数据）
	- [ ] 蓝图详情页对接 `GET /blueprints/{name}` API
	- [ ] 详情页按 SRS 布局：提案 → 契约 → 处理步骤
	- [ ] 支持从 .cue 渲染的 .html 内嵌预览
- [ ] 测试
  - [ ] 单元测试：api/client.dart 封装所有 Provider API 端点
  - [ ] Widget 测试：蓝图列表页渲染 API 返回数据
  - [ ] Widget 测试：蓝图详情页显示 SRS 三段式布局
- [ ] 验收
	- [ ] 蓝图列表数据来自 Provider 实时查询，非硬编码
	- [ ] 详情页包含 pipeline steps 可视化展示
- [ ] 提交
  - [ ] `git commit -m "feat(studio): 蓝图列表与详情页对接 Provider API"`

### Step 5-2 Studio 数据传输页面与交付记录页面

- 涉及文件：`src/studio/lib/screens/transfer.dart`、`src/studio/lib/screens/jobs.dart`、`src/studio/lib/screens/contracts.dart`

- [ ] 操作
	- [ ] 传输页对接 `POST /transfer/send` 和 `POST /transfer/receive`
	- [ ] 任务记录页对接 `GET /process/jobs` 和 `GET /process/jobs/{id}`
	- [ ] 契约页对接 `GET /blueprints` 中的 contract 字段
- [ ] 测试
  - [ ] Widget 测试：传输页发送文件 → 显示返回的分享链接
  - [ ] Widget 测试：任务记录页显示任务列表及其状态
  - [ ] Widget 测试：契约页展示 contract 字段定义
- [ ] 验收
	- [ ] Studio 上可完成完整的 send/receive 操作
	- [ ] 任务记录页与 CLI `catalog list` 数据一致
- [ ] 提交
  - [ ] `git commit -m "feat(studio): 数据传输页、任务记录页、契约页对接 API"`

### Step 5-3 AI 智能体集成（clarify / design / implement / review 命令）

- 涉及文件：`src/cli/src/clarify.rs`、`src/cli/src/design.rs`、`src/cli/src/implement.rs`、`src/cli/src/review.rs`

- [ ] 操作
	- [ ] `clarify from-chat`：客户对话 → LLM → 数据需求文档（DRD .md）
	- [ ] `design contract`：DRD → LLM → 数据契约
	- [ ] `design blueprint`：契约 + 目录 → LLM → 数据蓝图（即 CUE #Blueprint）
	- [ ] `design formalize`：Blueprint → Specification envelope YAML
	- [ ] `design preview`：Blueprint → .html 可视化预览
	- [ ] `implement`：Blueprint YAML → LLM → Python 处理代码
	- [ ] `review`：Specification → LLM → 完整性审计报告
- [ ] 测试
  - [ ] 单元测试：clarify 输入聊天记录 → 输出有效 DRD markdown
  - [ ] 单元测试：design contract → 输出含 field 定义的有效 YAML
  - [ ] 单元测试：design preview → 生成有效 HTML
  - [ ] 单元测试：review → 输出审计报告含缺失字段警告
  - [ ] 集成测试：clarify → design → implement → review 完整链路
- [ ] 验收
	- [ ] AI 命令在 Catalog/Pipeline/Contract 框架约束下工作
	- [ ] Blueprint .md/.cue/.html 三格式可由 .cue 单一事实源派生
	- [ ] 商务人员可通过对话生成蓝图（赋能非技术岗）
- [ ] 提交
  - [ ] `git commit -m "test(cli): AI 智能体 clarify/design/implement/review 命令与单元测试"`

### Step 5-4 端到端验收测试

- 涉及文件：`tests/e2e/`、`tests/fixtures/`

- [ ] 操作
	- [ ] 编写端到端验收用例：客户聊天 → DRD → 契约 → 蓝图 → Spec → Python代码 → 审计
	- [ ] 编写端到端交付用例：receive → pipeline(多步) → send → registry 登记
	- [ ] 编写跨组件用例：CLI spec wrap → Provider 读取执行 → Studio 查询展示
	- [ ] 编写脱敏流程用例（参考 GHTorrent 复盘）
	- [ ] 编写版本迭代用例：Blueprint V2→V3 diff 对比
- [ ] 测试
  - [ ] E2E 测试：完整"设计→执行→交付→追溯"数据链路
  - [ ] E2E 测试：Blueprint 三格式一致性验证
  - [ ] E2E 测试：version diff 正确显示两个版本差异
- [ ] 验收
	- [ ] 全部 E2E 用例通过
	- [ ] CLI / Provider / Studio 三端数据完全对齐
- [ ] 提交
  - [ ] `git commit -m "test(e2e): 端到端验收测试覆盖设计-执行-交付-追溯全链路"`

## M6 生产就绪与生态发布

### Step 6-1 CLI 二进制分发

- 涉及文件：`.github/workflows/release-cli.yml`、`src/cli/Cargo.toml`

- [ ] 操作
	- [ ] 构建 Linux (x86_64)、macOS (x86_64 + ARM64)、Windows (x86_64) 三平台二进制
	- [ ] GitHub Release 自动上传构建产物
	- [ ] crates.io 发布 `cargo install qtcloud-data` 可用
- [ ] 测试
  - [ ] 各平台二进制下载后可独立执行 `qtcloud-data --help`
- [ ] 验收
	- [ ] GitHub Release 包含三平台二进制下载
	- [ ] `cargo install qtcloud-data` 安装成功
- [ ] 提交
  - [ ] `git commit -m "ci(release): 多平台二进制构建与 crates.io 发布"`

### Step 6-2 新人接入文档与开发者体验

- 涉及文件：`docs/`、`README.md`

- [ ] 操作
	- [ ] 编写新人 15 分钟快速上手指南
	- [ ] 编写 CLI 每个子命令的使用文档和示例
	- [ ] 编写 Provider API 接口文档
	- [ ] 编写 Studio 开发者本地运行指南
- [ ] 验收
	- [ ] 新人按文档可从零运行完整项目
	- [ ] CLI 全部子命令有使用文档
- [ ] 提交
  - [ ] `git commit -m "docs(guide): 新人接入文档与完整 CLI 命令参考"`

### Step 6-3 运营监控与持续交付

- 涉及文件：`src/cli/src/monitor.rs`（新增）、`src/provider/internal/monitor/`（新增）

- [ ] 操作
	- [ ] CLI 增加 `monitor` 命令：查看最近 job 执行状态
	- [ ] Provider 增加健康检查 `/health` 端点
	- [ ] 接入 qtcloud-devops 流程的 deploy/operate/monitor 阶段
- [ ] 测试
  - [ ] 单元测试：monitor 命令读取 jobs.json 并输出状态摘要
  - [ ] 单元测试：GET /health → 200 OK
- [ ] 验收
	- [ ] `qtcloud-data monitor` 输出最近 10 条 job 状态
	- [ ] Provider 健康检查集成到 CI 部署后验证
- [ ] 提交
  - [ ] `git commit -m "feat(monitor): CLI monitor 命令与 Provider 健康检查端点"`
