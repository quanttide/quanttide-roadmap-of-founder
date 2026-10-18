# CHANGELOG

所有显著变更都将记录在此文件中。

格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)。

版本遵循语义化版本规范：0.0.x（探索期）→ 0.x.y（验证期）→ x.y.z（正式期）

---

## [Unreleased]

### 新增

- 初始化路线图仓库：`README.md`、`index.md`（入口）、`myst.yml`
- 迁入 `work/clarify-intent.md`：澄清意图的方向层（目标、核心问题、已/待决策，含 skill 流程），源自 `data/context/work/roadmap/` 的三篇草稿，整理时命名统一为「澄清意图」、skill 篇并入本篇并砍掉「命名状态」那一步、待决策收敛为存储落点一条
- 命令与字段另立一篇，落 `quanttide-founder-lab` 的 `docs/dev-guide/clarify-intent.md`（不进本仓：方向层归路线图，敲法归开发指南）

### 变更

- `work/clarify-intent.md` 迁出至 `quanttide-report-of-founder` 的 `code/clarify-intent.md`（该仓的 `work/` 随后更名为 `code/`，命令行入口同步为 `code intent` / `code clarify`）：这项工作已从「方向」变成「实现状态与关键设计」，按层归口进报告；本仓只留指针，命令与字段仍在 lab 仓的 dev-guide 与接口参考
