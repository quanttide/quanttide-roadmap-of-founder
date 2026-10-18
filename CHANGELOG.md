# CHANGELOG

所有显著变更都将记录在此文件中。

格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)。

版本遵循语义化版本规范：0.0.x（探索期）→ 0.x.y（验证期）→ x.y.z（正式期）

---

## [Unreleased]

### 新增

- 初始化路线图仓库：`README.md`、`index.md`（入口）、`myst.yml`
- 迁入 `work/`：澄清意图两篇——`clarify-intent.md`（目标、核心问题、已/待决策，含 skill 流程）、`clarify-intent-cli.md`（六条命令与字段）。源自 `data/context/work/roadmap/` 的三篇草稿，整理时：命令行前缀统一为 `qtfounder work clarify`，命名统一为「澄清意图」，命令 11→6（`config` / `schema` / `rule list` / `intent show` / `revision list` 出局）、对象 3→2（rule 不再是实体，就是 `rules.md` 里的行）、HTTP 表并入命令表、skill 篇并入主体并砍掉「命名状态」那一步、待决策收敛为存储落点一条；总计 14.7KB → 6.6KB
