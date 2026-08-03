# 工程标准的定位与意义

## 背景

2026-07-10，量潮数据工程完成了配置目录结构规范（`specification` 仓库 v0.0.1），将数据管道、数据蓝图、数据契约、数据目录的概念和目录结构正式定义下来。

## 之前的状态

概念散落在代码里，靠人脑同步：

- CLI 里硬编码了目录路径，Studio 不知道
- 新加 pipeline 靠看已有例子猜格式
- catalog、contract 等词在不同人嘴里意思不一样

## 这套标准的意义

### 1. 统一语言

数据蓝图 = 数据契约 + 数据管道。术语写在规范里，CLI、Provider、Studio、客户用同一套坐标系沟通。

### 2. 定义边界

规范定义"有什么"和"长什么样"，不定义"怎么用"。CLI 可以读 `.quanttide/data/pipeline/`，Provider 可以读同一个目录提供 REST API，Studio 也可以读。实现解耦的前提是规范先定好。

### 3. 把经验变成标准

隐形的约定（目录结构、命名习惯）变成显式文档，写入 AGENTS.md 后 agent 和新人都可以直接遵守。

### 4. 可版本化

单独成仓、独立版本号。规范变更可 review、可追溯。CLI v0.0.5 对应 spec v0.0.1，spec 升 v0.1.0 说明有 breaking change，各消费者对应调整。

## 一句话总结

> 定标准不是为了管人，是为了让不同组件、不同团队、不同工具可以各自独立演进的同时，还能协同工作。

标准定义了量潮数据工程的"坐标系"——所有实现（CLI、Provider、Studio）都是坐标系上的点，规范是坐标轴本身。

## 参考

- [specification 仓库](https://github.com/quanttide/quanttide-specification-of-data-engineering)
- [specification/index.md](https://github.com/quanttide/quanttide-specification-of-data-engineering/blob/main/index.md)
