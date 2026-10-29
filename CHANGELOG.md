# CHANGELOG

所有显著变更都将记录在此文件中。

格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)。

版本遵循语义化版本规范：0.0.x（探索期）→ 0.x.y（验证期）→ x.y.z（正式期）

---

## [Unreleased]

### 变更

- `qtfounder/docs.md`：文档审计路线图自 CLI 仓 `src/cli/docs/dev-guide/docs/roadmap.md` 迁入——CLI 的方向文档全部归本仓；`qtfounder/index.md` 、README 索引与 `myst.yml` 目录同步，README 约定句改为现状（方向归本仓、应用仓只留实现细节）

- 清理已完成与过时信息：`qtfounder/index.md` 删去 CLI v0.1 六条已落地范围项与已完成的兼容规则项、修正过时的版本描述（0.0.9 → 0.0.41），范围只剩退出码收口（实现在 toolkit 仓 `packages/rust/ROADMAP.md`）；`myst.yml` 目录与 README 文档索引从已删除的 `qtgame-war/code/`、`game/` 旧结构换为 v0.x 版本文件；`qtgame-war/index.md` 删去「studio/ROADMAP.md 已收归为薄指针」的失实表述（该文件实为独立清单）
- `qtfounder/index.md`：阈值标定勾选——两组语料实测后块占比无判别力保持关闭、无散文率定 0.35、列表单项字数 100→120；数字与理由的事实源在 CLI 契约注释
- `qtfounder/index.md`：CLI v0.1 补两条冻结收口（退出码语义统一、兼容规则落 CLI 仓 CONTRIBUTING），验收加 Skill 漂移检查——0.1 起命令面按语义化版本冻结，破坏性变更 bump minor
- `qtfounder/roadmap.md` 改名 `qtfounder/index.md`：领域目录统一用 `index.md` 作入口，与 `qtgame-war/` 一致；本仓 README 与 `myst.yml` 目录同步，正文未改
- `language/governance.md` 迁至 `qtfounder/language.md`：术语治理清单随领域归并，`language/` 不再单设目录；正文未改，主仓 AGENTS.md 与本仓 README 索引同步

### 移除

- `work/quanttide-pay.md` 的目录链接：文件已于此前移除，README 索引同步删去

### 新增

- `qtfounder/roadmap.md`：qtfounder 平台路线图，自应用仓 `apps/qtfounder/src/cli/docs/dev-guide/roadmap.md` 迁入（CLI v0.1 收尾、迁移、新增三段）；新增「qtcloud 组织验证」一节——探底噪、真实交付对照、漏率刻度三步，外加三条缺口，先记顺序不实现
- `qtgame-war/game/index.md`：架构落成检查——M1 开工前的六项决定与五条 lint，每项都要能写成代码约束
- `qtgame-war/code/contract.md`：系统契约的落地顺序——格式冻结、桩实现接通、真引擎回填、认知接入、甲板展开五段
- `qtgame-war/game/params.md`：数值参数的落地顺序——区间抽取、三视角交叉、作者裁决、敏感性兜底
- `qtgame-war/code/command.md`：指挥部侧的落地顺序——状态机与两份结构、认知与敌军指挥官、参谋与甲板、交付后的增量
- `qtgame-war/code/acceptance.md`：验收系统的落地顺序——夹具与金标准、命令行骨架、回归与批量、迷雾与 AI 断言、人类协议、值守
- `qtgame-war/code/index.md`：游戏《迷雾中的司令部》的四段顺序——可复算、可复盘、可对弈、可交付；每段写达成标志、顺序依据与后置代价
- `agent/agent-harness.md`：Agent 协作治理方向——先清点存量写入，用数出来的量决定闸门要不要装；之后依次做写入拦截、可离开对话使用的校验单元、外人读得懂的审计报告与成本度量
- `work/qtcloud-pay.md`：支付云方向——先解决支付系统的技术接管，以实训基地代金券业务作首个落地场景验证现有能力，之后再定自动化开发与公司、联盟账本的建设顺序
- 初始化路线图仓库：`README.md`、`index.md`（入口）、`myst.yml`
- 迁入 `work/clarify-intent.md`：澄清意图的方向层（目标、核心问题、已/待决策，含 skill 流程），源自 `data/context/work/roadmap/` 的三篇草稿，整理时命名统一为「澄清意图」、skill 篇并入本篇并砍掉「命名状态」那一步、待决策收敛为存储落点一条
- 命令与字段另立一篇，落 `quanttide-founder-lab` 的 `docs/dev-guide/clarify-intent.md`（不进本仓：方向层归路线图，敲法归开发指南）
- `language/governance.md`：术语治理清单——十六个词的现存量与先判什么，外加出库与改名的三个判断口；方法落在 Skill `language-governance`

### Removed

- `work/quanttide-pay.md`：归口进 `data/profile` 的 `execute/`，本仓不再承载支付工程的执行方向
- `agent/agent-harness.md`：闸门、体检、巡检三件都已落地；仅剩的术语存量归 `language/governance.md`，本仓不再单设 agent 方向

### 变更

- `language/governance.md`：术语存量重数（10-25 全仓 247 个命中、十八个词），清单按「直接换词」与「先判归属」分两表；补语境声明的边界，顺序改为产物先行、账本改名在后、回潮两处顺手清
- `qtgame-war/game/index.md`：数值参数落地表迁出至 `quanttide-context-of-founder` 的 `game/roadmap/index.md`，入口篇只留架构检查
- `qtgame-war/code/gates.md` → `qtgame-war/game/index.md`：架构检查移入游戏文件夹，作入口篇
- `qtgame-war/code/params.md` → `qtgame-war/game/params.md`：数值参数归玩法侧，与实现顺序分开
- `qtgame-war/code/index.md`：补「已定前提」与「分线」两节，五份专文各管一条线，四段顺序仍是唯一门槛
- `qtgame-war/code/index.md`：补 front matter，声明体裁 roadmap、读者 maintainer
- `qtgame-war/code/index.md`：按技术写作规范重写，补全文档范围、阶段表、达成标志与未决事项
- `agent/agent-harness.md`：术语存量第二期「条目」清完，按语境换成一条、记录、清单、子项；下一期「产物」
- `agent/agent-harness.md`：术语存量第一期「判定」清完，53 处换成判断；组件名「判定层」随之改名「判断层」。下一期「条目」
- `agent/agent-harness.md`：闸门只拦用词完成，本节从近景移除；近景只剩术语存量，从「判定」开始改
- `agent/agent-harness.md`：巡检已完成，本节从近景移除；正文改为闸门拦什么与术语存量两件，巡检结论指向 `data/report/agent/corpus-patrol.md`
- `agent/agent-harness.md`：体检报告接上，本节从近景移除；正文只剩巡检，先说清它给闸门与报告提供基线
- `agent/agent-harness.md`：闸门已装在提交那一刻，本节从近景移除；正文改为先做巡检，用存量重判给闸门一个基线，审计报告放最后
- `agent/agent-harness.md`：校验单元与度量两节已完成，按约定从近景移除
- `README.md`、`index.md`：有意精简为标题与许可（`index.md` 仅保留「首页」），正文不再承载定位、目录与格式约定
- **格式约定废弃**：原「章节按序出现，空节省略：目标（含元目标）→ 核心问题 → 已决策 → 待决策；已决策须有行动佐证，待决策须写清卡在哪」不再适用，主题文件改为散文直述。同域仍按旧约定表述的位置（语境仓 README 与 journal-to-roadmap skill）待同步
- `work/clarify-intent.md` 迁出至 `quanttide-report-of-founder` 的 `code/clarify-intent.md`（该仓的 `work/` 随后更名为 `code/`，命令行入口同步为 `code intent` / `code clarify`）：这项工作已从「方向」变成「实现状态与关键设计」，按层归口进报告；本仓只留指针，命令与字段仍在 lab 仓的 dev-guide 与接口参考
