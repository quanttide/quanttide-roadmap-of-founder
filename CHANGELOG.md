# CHANGELOG

所有显著变更都将记录在此文件中。

格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)。

版本遵循语义化版本规范：0.0.x（探索期）→ 0.x.y（验证期）→ x.y.z（正式期）

---

## [Unreleased]

### 新增

- `agent/agent-harness.md`：Agent 协作治理方向——先清点存量写入，用数出来的量决定闸门要不要装；之后依次做写入拦截、可离开对话使用的校验单元、外人读得懂的审计报告与成本度量
- `work/qtcloud-pay.md`：支付云方向——先解决支付系统的技术接管，以实训基地代金券业务作首个落地场景验证现有能力，之后再定自动化开发与公司、联盟账本的建设顺序
- 初始化路线图仓库：`README.md`、`index.md`（入口）、`myst.yml`
- 迁入 `work/clarify-intent.md`：澄清意图的方向层（目标、核心问题、已/待决策，含 skill 流程），源自 `data/context/work/roadmap/` 的三篇草稿，整理时命名统一为「澄清意图」、skill 篇并入本篇并砍掉「命名状态」那一步、待决策收敛为存储落点一条
- 命令与字段另立一篇，落 `quanttide-founder-lab` 的 `docs/dev-guide/clarify-intent.md`（不进本仓：方向层归路线图，敲法归开发指南）

### 变更

- `agent/agent-harness.md`：闸门只拦用词完成，本节从近景移除；近景只剩术语存量，从「判定」开始改
- `agent/agent-harness.md`：巡检已完成，本节从近景移除；正文改为闸门拦什么与术语存量两件，巡检结论指向 `data/report/agent/corpus-patrol.md`
- `agent/agent-harness.md`：体检报告接上，本节从近景移除；正文只剩巡检，先说清它给闸门与报告提供基线
- `agent/agent-harness.md`：闸门已装在提交那一刻，本节从近景移除；正文改为先做巡检，用存量重判给闸门一个基线，审计报告放最后
- `agent/agent-harness.md`：校验单元与度量两节已完成，按约定从近景移除
- `README.md`、`index.md`：有意精简为标题与许可（`index.md` 仅保留「首页」），正文不再承载定位、目录与格式约定
- **格式约定废弃**：原「章节按序出现，空节省略：目标（含元目标）→ 核心问题 → 已决策 → 待决策；已决策须有行动佐证，待决策须写清卡在哪」不再适用，主题文件改为散文直述。同域仍按旧约定表述的位置（语境仓 README 与 journal-to-roadmap skill）待同步
- `work/clarify-intent.md` 迁出至 `quanttide-report-of-founder` 的 `code/clarify-intent.md`（该仓的 `work/` 随后更名为 `code/`，命令行入口同步为 `code intent` / `code clarify`）：这项工作已从「方向」变成「实现状态与关键设计」，按层归口进报告；本仓只留指针，命令与字段仍在 lab 仓的 dev-guide 与接口参考
