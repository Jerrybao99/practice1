# qtcloud-data 工作意图

## 1. Provider 多租户执行与按次计量

> 一句话：跑一次收一次钱，托管执行是唯一收费核心。

- 背景/动机：商业模型已明确托管执行按次计费，Provider 缺多租户隔离与计量即可商业化。
- 关键证据：
  - 日志：2026-07-10 刻意避开多租户设计（2026-07-10.md:102-103）
  - 代码：`POST /blueprints/{name}/runs`（`src/provider/internal/api/router.go:24`）；`JobRecord` 含 `started_at/finished_at` 计量字段（`src/provider/internal/store/store.go`）
- 参考对标：Modal 按秒计费、Posit Cloud 统计用户付费验证
- 设计实现：
  - **平台**：Provider (Go)
  - **工具**：现有 pipeline runner、job store、sandbox 路径防护
  - 具体实现：`JobRecord` 增加 `tenant_id`/`quota`/`cost`；新增 `AuthHandler` 多租户中间件；run 完成写入 `cost = duration × rate × preemptible_factor`
- 设计排除：不做 DBU 式多档体系（账单复杂度是负资产）；不做单独 token 计费
- 设计原则：
  1. 计价透明到客户可心算
  2. 收入与使用强挂钩
  3. 先单档单价，有量加档位

## 2. 端到端项目交付工作流

> 一句话：5 阶段项目流程串联为 CLI 命令链，实现需求到报告的端到端闭环。

- 背景/动机：日志已定义调研/谈判/实施/验收/复盘五阶段流程，CLI 原子命令齐备，缺上层编排。
- 关键证据：
  - 日志：2026-07-27 定义五阶段流程及对应文档（2026-07-27.md:7-11）
  - 代码：`ProcessJobRecord` 结构体已含 customer、blueprint、pipeline、status、timestamps 等编排字段（`src/cli/src/process.rs`）
- 参考对标：-
- 设计实现：
  - **平台**：CLI (Rust)
  - **工具**：process 命令 job 记录、contract/blueprint YAML 模板、review 质量审计
  - 具体实现：新增 `project init|status|report` 子命令；挂载 clarify→contract→blueprint 为调研+谈判阶段，process 为实施阶段，review 为验收阶段
- 设计排除：不做内置 CRM/合同管理（飞书已有）；不做复杂权限系统
- 设计原则：
  1. 每阶段产出标准文档作为验收依据
  2. 复用现有 CLI 原子命令做上层串联
  3. 商务经理可直接操作

## 3. CLI AI 获客全链路

> 一句话：打通 clarify→design→implement→process→report，免费 CLI 获客，报告交付收费。

- 背景/动机：商业模型明确 CLI 免费获客、服务端收费，AI 生成到报告交付的全链路需打通为获客漏斗。
- 关键证据：
  - 日志：2026-07-10 研发范式已成型，clarify/design/implement 三端成熟（2026-07-10.md:23-24）
  - 代码：`clarify.rs`→`design.rs`→`implement.rs`→`process.rs` 全链路已存在，`render_html()`（`src/cli/src/blueprint_core.rs`）已有报告生成能力
- 参考对标：Modal 免费 CLI + 按次付费 API 模式
- 设计实现：
  - **平台**：CLI (Rust)
  - **工具**：clarify/design/implement/process 命令链、blueprint_core::render_html、review 质量报告
  - 具体实现：新增 `report generate <project>` 命令，聚合执行结果 + 质量审计 + 交付清单输出 HTML 报告；process 完成时自动触发
- 设计排除：不做 Web 端报告编辑器（Studio 展示静态 HTML 即可）；不做自定义模板引擎
- 设计原则：
  1. AI 生成是手段，付费报告是交付物
  2. CLI 全链路尽量一键跑通
  3. 复用现有 HTML 渲染能力
