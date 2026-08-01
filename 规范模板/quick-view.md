# 透视文档指南

## 文档须知

- 提供给软件开发新人**快速、实用、复用、唯一**的项目学习入口
- 教学方向：实用的人机协作技巧 + 投喂给新人必要的文档规范
- 前置知识：
  - zed + opencode 协作基本操作
  - Markdown 基本语法
  - .md 文件排版规范

## 交互示例

prompt 1：生成 `quanttide-data-quick-view.md`于项目根目录中，要求如下：
1. 阅读`quanttide-data`中全部文件，重点关注该项目的所有文档文件
2. 严格依据`规范模板\xxx-quick-view.md`提供的范例

> 注入上下文  
> 仓库名可按需替换，下同  
> 拖拽 `规范模板\xxx-quick-view.md` 到 zed AI 交互界面中  

prompt 2：从数据工程日志（journal）中，整理数据工程意图（intention），按如下步骤顺序严格执行：
1. 日志仓库：`quanttide-data`
2. 代码仓库：`qtcloud-data-main`、`qtdata-main`
3. 阅读日志仓库和代码仓库全部文件
4. 输出名为`qtcloud-data-transfer.md`和`qtdata-transfer.md`于 zed 编辑器打开的项目根目录中，严格参考`规范模板\transfer.md`提供的范例

> 拖拽 `规范模板\transfer.md` 到 zed AI 交互界面中  