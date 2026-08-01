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
2. 严格依据`xxx-quick-view.md`提供的范例

> 注入上下文  

prompt 2：从数据工程日志（journal）中，整理数据工程意图（intention），按如下步骤顺序严格执行：  
1. 通过下述操作顺序构建上下文
  1. 阅读`quanttide-data`全部文件，重点关注该项目`data/jorunal`和`data/intention`下全部文件
  2. 阅读该项目透视文档:`quanttide-data-quick-view.md`
  3. 阅读`qtcloud-data-main`与`qtdata-main`中的全部文件
  4. 找寻与`data/jorunal`和`data/intention`下全部文件密切相关的仓库`qtcloud-data-main`与`qtdata-main`中的代码文件
  5. 整合凝练上述步骤提取到的关键信息作为下述步骤的上下文
2. 输出名为`qtcloud-data-transfer.md`和`qtdata-transfer.md`于 zed 编辑器打开的项目根目录中，对该文档内容有如下要求：
  1. 严格参考`quanttide-data`现有工作意图文档`data/intention`下的`transfer.md`文件格式
  2. 内容严格参考构建的上下文
  3. 只输出本项目未来的工作意图，不包含过去的工作意图
  4. 紧扣最终目标：描绘量潮为什么想要建设这样一个数据工程，第二大脑
  5. 记住工作意图是“未来的自我记忆”，关系到组织希望成为一个什么样的组织，一般相对稳定，但和组织高度绑定，不一定具备很强的迁移性
  6. 文档的必要位置给出为什么做出如此推测的简要分析，用 > 注释表达