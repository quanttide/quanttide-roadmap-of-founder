# CHANGELOG

所有显著变更都将记录在此文件中。

格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)。

版本遵循语义化版本规范：0.0.x（探索期）→ 0.x.y（验证期）→ x.y.z（正式期）

---

## [Unreleased]

### 新增

- 初始化路线图仓库：`README.md`、`index.md`（入口）、`myst.yml`
- 迁入 `work/`：澄清意图两篇——`clarify-intent.md`（目标、核心问题、已/待决策，含 skill 流程）、`clarify-intent-cli.md`（名词 `intent` 与动词 `clarify` 两个入口、字段）。源自 `data/context/work/roadmap/` 的三篇草稿，整理时：命令行前缀统一为 `qtfounder work clarify`，命名统一为「澄清意图」，三篇并为两篇、十一组命令并为「名词看、动词写」两个入口、对象三层并为 intent + 澄清记录两层（规则就是 `rules.md` 里的行）、HTTP 表并入命令表、skill 篇并入主体并砍掉「命名状态」那一步、待决策收敛为存储落点一条；总计 14.7KB → 7.0KB
