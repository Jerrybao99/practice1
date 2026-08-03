## 1. 数据传输统一接口标准化

- 背景/动机：数据云的第一公里和最后一公里 — 把零散的客户数据收发收敛为统一的 send/receive 命令。
- 关键证据：
  - 日志 `2026-07-28.md:7-13`：明确了飞书→GitHub→S3 的三步数字化链路，send/receive 是该链路的两端原子动作。
  - 代码 `src/cli/src/providers/mod.rs:5-20`：`StorageProvider` trait 定义了 `send`/`receive`/`receive_path` 统一接口，新平台接入仅需实现此 trait。
- 设计实现：
  - **平台**：Dropbox、百度网盘、Google Drive、OneDrive、S3、SFTP 六平台
  - **工具**：Rust CLI `qtcloud-data`（`src/cli/src/transfer.rs`）
  - 具体实现：在 `src/cli/src/providers/mod.rs:37-47` 的 `from_name` 工厂中注册新 Provider struct，按 `StorageProvider` trait 协议接入，`detect` 函数补 URL 识别规则即可。
- 设计原则：
  1. 先支持一个平台能用，不加抽象层；第二个平台接入时再抽象
  2. send 成功后自动登记交付链接到 `delivery-links.json`，失败只 warning 不反转
  3. 凭证走环境变量，不硬编码在命令行参数中

## 2. 端到端交付流程编排自动化

- 背景/动机：将 receive→pipeline→send 串联为一条可复现的生产线，消除人工拼接环节。
- 关键证据：
  - 日志 `2026-07-28.md:59-66`："先把数据转成机器可读格式，再写平台"渐进式标准化思路，对应 process 编排的接收-处理-交付三阶段。
  - 代码 `src/cli/src/process.rs:82-288`：`run` 方法串联 shell 调用 `transfer receive`、本地 pipeline 逐步执行、`transfer send` 交付，失败时写入 `failed` 状态并记录完整日志。
- 设计实现：
  - **平台**：本地 DataOps CLI 编排，Provider 远程执行对齐中
  - **工具**：Rust CLI `qtcloud-data`（`src/cli/src/process.rs` + `src/cli/src/pipeline.rs`）
  - 具体实现：process.rs 已按 `--blueprint` 解析 pipeline（通过 `cue export` 提取 pipeline 字段），结果写入 `jobs.json` + 日志 + `registry.json`；下一版本在 process 入口增加 manifest 输入契约（`TODO.md:12-13`），让单个 process 支持多输入数据流。
- 设计原则：
  1. 编排失败不丢中间产物，保留已完成步骤的输入输出供排查
  2. source_url 自动脱敏去 query 和 fragment，只留 scheme://host/path
  3. 交付成功后自动登记 registry，provider=process，保持可追溯

## 3. 交付过程的记录追溯与目录管理

- 背景/动机：将交付活动的追踪能力嵌入命令行工具，支撑从"蓝图"到"交付清单"的验收闭环。
- 关键证据：
  - 日志 `2026-07-27.md:66-77`：交付记录列表、质量报告可视化、重新交付入口 — 均依赖底层工具提供有序可查的交付记录。
  - 代码 `src/cli/src/transfer.rs:104-115` + `src/cli/src/process.rs:24-39`：`DeliveryLinkRecord` 与 `ProcessJobRecord` 结构体定义了 id/provider/file/link/status/time 等字段，构成交付追踪的最小数据模型。
- 设计实现：
  - **平台**：本地 JSON registry（无外部依赖）
  - **工具**：Rust CLI `qtcloud-data`（`src/cli/src/transfer.rs` delivery-links + `src/cli/src/process.rs` jobs + `src/cli/src/catalog.rs` registry）
  - 具体实现：现有三处登记点已打通 — `transfer send` 写 `delivery-links.json`，`process` 写 `jobs.json`+日志+`registry.json`；下一版本在 `src/cli/src/catalog.rs` 增加 `list/show` 子命令（`catalog list --status delivered`），让 CLI 可查询登记记录，对齐 Studio 的交付记录页面（`src/studio/lib/screens/contracts.dart`）。
- 设计原则：
  1. JSON registry 格式统一为 `BTreeMap<id, record>`，便于追加和查询
  2. 登记写入失败不阻断主流程，仅输出 warning
  3. 目录结构遵循 `.quanttide/data` 规范，DATA_ROOT/CATALOG_DIR 可覆盖
