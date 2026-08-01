# 数据传输

## 未来意图

1. **数据交付的一站式收发标准**：将"发送+接收"的对偶模型确立为量潮与客户之间唯一的数据交付协议。客户通过分享链接提交原始数据，量潮通过分享链接交付处理结果，两端对称、语义对等，从根本上消除"数据用什么方式传"的沟通成本。

> 日志反复提及"网盘是最简单的一种交付方式"（2026-07-10），且 transfer 代码已实现 send/receive 的双向对称。process 命令更将此编排为 receive → pipeline → send 的标准三步，说明收发不是独立功能，而是交付闭环的起止端点。

2. **多平台传输的渐进抽象**：在 Dropbox 验证单一平台可行性后，逐步接入百度网盘、Google Drive、OneDrive、S3、SFTP，最终收敛为统一的 `StorageProvider` 接口，根据客户设备和网络环境自动路由最优通道。

> 代码已预留 6 个 provider 骨架（dropbox 已完整实现，其余 5 个为占位），说明多平台不是远期愿景而是近期工程计划。日志中"等第二个平台接入时再抽象接口"与代码中 `from_name()` 工厂模式的设计取舍一致。

3. **传输记录即数据血缘的起点**：每次 send/receive 操作自动登记到 `delivery-links.json`，与 process 层级的 `jobs.json` 和 catalog 的 `registry.json` 串联，形成从原始数据入口到最终交付物的完整可审计链。

> transfer.rs 中 `DeliveryLinkRecord` 已记录 provider、file_path、remote_path、link、status、sent_at；process.rs 中 `ProcessJobRecord` 进一步关联 customer_id、blueprint、pipeline、log_path。两层记录叠加即可追溯"谁的数据→经过什么处理→何时交付给谁"。

## 拟定工作范围

- **CLI 端**：完善 `transfer send/receive` 的剩余 5 个 provider 实现（百度网盘、Google Drive、OneDrive、S3、SFTP），对齐 Dropbox 已有的认证约定和错误处理模式。
- **Studio 端**：将 CLI 的 transfer 能力暴露为可视化操作界面，支持拖拽上传、链接一键复制、传输历史查看。
- **Provider 端**：扩展 Go 后端的 pipeline 与 specstore 模块，支持在服务端触发传输任务，替换当前 CLI 内嵌 shell 调用的编排方式。

## 未来实现

- **平台**：优先补齐百度网盘和 S3 两个 provider，覆盖国内主流网盘和对象存储两大类别，形成"网盘类 + 对象存储类"的双线能力。
- **工具**：复用 `qtcloud-data` CLI（Rust）已有的 provider trait 体系，在 `src/cli/src/providers/` 下逐个实现，每个新增 provider 同时补充对应的集成测试。Studio 端复用 Flutter 已有的 `TransferScreen` 和 `ApiClient`，扩展 provider 选择器和传输状态展示。
- **环境与认证**：统一通过环境变量注入 access token（沿用已有约定），Provider 端增加 token 管理接口以支持多租户场景下的凭证安全存储。

## 设计原则

1. **网盘优先，直连补充**：以网盘共享链接为默认传输方式，因为网盘厂商已在后台处理好分片、断点续传、权限控制。S3/SFTP 等直连模式仅作为无网盘环境下的备选方案。

2. **手动为主，自动为辅**：客户通过分享链接提交数据（手动模式）是交互主线，自动拉取仅面向已建立信任关系的长期客户。手动模式天然携带"客户已授权"的语义，降低合规风险。

3. **原子操作，组合编排**：transfer 是原子操作（单个文件的一次收发），process 是编排层（串联多个原子操作）。不对 transfer 层添加流程逻辑，保持其单一职责。

4. **先发后记，记不阻发**：传输操作成功后再写入交付记录，记录写入失败只输出 warning 不阻塞交付结果（见 transfer.rs 和 process.rs 的错误处理策略），保证交付优先于记录完整性。

5. **一个平台，一个实现**：每个 provider 独立实现，不加额外抽象层。等至少两个同类型平台（如两个网盘）实现完毕后再提取公共接口，避免过早抽象的维护成本。
