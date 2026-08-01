# 开发日志

## 计划

- [ ] 任务 3 比对`practice1.md`和 Github 原文设计`quick-view.md`
- [x] 建立`README.md`
- [ ] 吃透 2 项目透视
- [ ] 参考资料消化
- [ ] 提炼`README.md`规范
- [ ] 合并开发流

### PR

- [ ] 标准化检查清单流程构建

1. **格式检查**：Conventional Commit？文件路径规范？
2. **内容检查**：issue 是否有根因分析？PR 是否引用关联 issue？
3. **质量检查**：文档语言是否通顺？结构是否合理？
4. **一票否决项**：什么情况下 PR 直接打回？

- 提交方式：在仓库 `.github/` 下新增 `PULL_REQUEST_TEMPLATE.md` 或 `review-checklist.md`，并提交 PR。

#### 任务 1

- [ ] 知识的诅咒解决方案与瓶颈：虽然通过固定工作流和模板能够尽可能约束 AI 的输出向新人直接可以上手参阅的需求靠拢，但输出的上限依然在于新人是否擅长提问、迭代、注入上下文的灵活性、会遗忘、抓核心，实测适度人工干预改写后的效果更贴合使用场景，并可以反哺工作流和模板的规范
- [ ] 两个教学方向：高频实用人机协作习惯的构建、投喂必要的可复用的上下文/文档规范

#### 附加题

- [ ] 2 档交付标准定义
- **标准档（最低可接受）**：满足什么条件即算任务完成？
- **优秀档（有亮点）**：满足什么条件超出预期？
- 每档写 2-4 条可验证的验收条件（如"至少提 1 个 issue + 1 个 PR"、"整理了至少 5 条意图"）
- 提交方式：在 `practice1.md` 末尾增加"交付标准"章节并提交 PR

## 总结

- 写项目一定要连仓库作备份，误删除关键文件的惨痛教训
- 对项目的快速理解力有待加强，解决逻辑是通过可复用 prompt 流和模板约束 AI 输出，通过人工检验和 AI 比对建议以对齐输出和需求，真实效果有待 PR 反馈

## 参考资料

- 项目仓库：https://github.com/quanttide/quanttide-data
- 量潮第二大脑资产图式章程：https://github.com/quanttide/quanttide-bylaw-of-asset-management/blob/main/schema/second-brain.md
- 量潮发布管理章程：https://github.com/quanttide/quanttide-bylaw-of-devops/blob/main/lifecycle/release.md