# CHANGELOG

## [0.1.0-rc.1] - 2026-07-24

### Changed

- 全部组件文档升级为正式规范格式（RFC 2119 关键词：MUST/SHOULD/MAY）
- `index.md`：新增引言、文档约定、版本兼容性章节。CUE 示例替换为 YAML
- `requirement.md`：增加正式字段定义表、验证规则、标准模板和完整示例
- `contract.md`：增加正式类型系统（7种类型 + enum 约束）、字段级约束/承诺定义、验证规则和完整 YAML 示例。CUE 替换为 YAML
- `blueprint.md`：增加正式字段定义表、step 级 description/expectation 规范、验证规则和完整 YAML 示例。CUE 替换为 YAML
- `pipeline.md`：增加 runtime 子结构定义、step 级 image/env 扩展、调度方式枚举、验证规则和完整 YAML 示例。CUE 替换为 YAML

### Migration from v0.1.0

- CUE 格式已弃用，统一使用 YAML
- 所有文档增加了 RFC 2119 关键词，字段约束从"描述"升级为"MUST/SHOULD/MAY"

## [0.1.0] - 2026-07-23

### Changed

- **Breaking**: 四层框架重构——需求层(Requirement) → 规格层(Specification) → 实现层(Implementation) → 任务层(Task)
- 新增 `requirement.md`：数据需求文档（DRD）规范定义
- 重定义 `blueprint.md`：从"完整处理方案"改为规格层的"处理蓝图"（工作流步骤），与 Contract 并列
- 重定义 `contract.md`：明确为 Specification 组成部分，增加输入/输出契约区分和 CUE 结构示例
- 重定义 `pipeline.md`：从规格层移至实现层，增加运行时配置和容器化字段
- 更新 `index.md`：新四层框架总览 + 术语表 + 更新目录结构和环境变量
- 更新 `myst.yml`：TOC 增加 requirement.md

### Migration

旧目录结构 → 新目录结构:
- `blueprint/`（旧 Blueprint 定义）→ `specification/`（规格书，含 contract + blueprint）
- `contract/` → `specification/contract/`
- `pipeline/`（旧处理步骤）→ `specification/blueprint/`（处理蓝图）
- 新增 `.quanttide/data/pipeline/` 为实现层数据管道

## [0.0.1] - 2026-07-10

### Added

- 首页：配置目录结构、Unix 风格命名规范、路径解析规则、环境变量说明
- 数据管道定义（pipeline.md）：格式、结构示例、字段说明
- 数据蓝图定义（blueprint.md）：格式、结构示例、字段说明
- 数据契约定义（contract.md）：格式说明
- 数据目录定义（catalog.md）：Volume 结构示例、字段说明
- myst.yml：MyST 项目配置
