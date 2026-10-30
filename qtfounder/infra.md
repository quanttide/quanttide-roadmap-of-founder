---
genre: roadmap
audience: maintainer
---

# infra 路线图

`qtfounder infra`：登记「什么事件、跑什么命令」，事件来了把命令交出去；CLI 自己不调度，被调用就退出。设计方向已由人澄清定案（2026-10-30），本地与云端两段验证都没跑。本节只记验证顺序与验收，不记设计争论。

起因：评估 infra 平移。目标已改（2026-10-30）：这类工具的家只能在 qtcloud-* 独立仓（qtcloud-devops 先例：接口稳定性由外部消费者给，不在产品仓内部），所以先往 qtcloud-infra，再往 qtcloud 集成，不落 qtcloud 主站。结论是命令面形状已收敛、可以平移，卡住的是验证债。qtcloud 的组织验证另见 `index.md`「qtcloud 组织验证」节。

## 设计定案

**世界观（人的目标，2026-10-30）**：一个本地环境里的组件，可以映射到云端的一个集群——定时器本地是 crontab、云端是定时触发器；消息队列本地可部署、也可用云端。CLI 声明「什么触发、跑什么」，触发的执行机制属于环境；换环境换的是驱动方，不是 CLI。infra 的三条技术选择都是从这个目标推出来的。

**灵感与顺序（2026-10-30）**：参照 CloudEvents——Serverless 传递事件的标准。方向是从事件开始建整套逻辑基础设施（计算、存储、网络、触发器），不是只做事件；但进入顺序定为事件第一块——它已攒够两个真实实例（本地 git 钩子与 crontab，云端总线在后），其余部件等各自的第二个真实实现出现再收，不提前设计一整套抽象。载荷若真来，参照 CloudEvents 的信封形状（id、source、type、data 可选）定 fire 的扩展，不自创字段。

以下三条是定案，不是待办，不因验证结果回炉：

- **trigger 是带标签的枚举，event 与 every 形状对等**。云端总线来的时候没有「周期」概念，只有事件；周期只是驱动方的一种投递方式。表结构不改一个字段就能被云端消费
- **解释权全部外置**：事件名不枚举、周期串不解析、无人听的事件退 0 不报错。换驱动方（crontab、git 钩子、云端总线）不动表、不动命令面
- **命令面只有 fire / run / emit**。本地和云端的分界只在「谁调 fire」，fire 里面完全一样；emit 只打印驱动方那一行，不替人接线

立场全文在 CLI 仓 `src/cli/docs/dev-guide/local-and-cloud.md`。

## 待验证：本地闭环

实况（2026-10-30 查）：订阅表全空；`.githooks/pre-commit` 实际走 `qtfounder git hook run pre-commit`，绕过了 infra；user-guide 写的「`.githooks/pre-commit` 里那行 `exec qtfounder infra fire pre-commit`」在仓里没有对应物——文档描述的接线还没发生。测试零覆盖（fire / run / store 都没有）。

- [x] 接线：本仓钩子改走 `infra fire`（2026-10-30，主仓 `ea32ebd`）；钩子里先 `cd` 主仓根再 fire——订阅命令不带 `--root`，靠 cwd 定位，这是 fire 不带载荷在本地的形态
- [x] 补 fire / run / store 测试（2026-10-30）：抽出 `fire_exit`／`emit_lines`／`should_run` 三个纯函数，六条测试钉住退码语义与两种接线形状
- [ ] 注册至少一条真实订阅，跑两周

达成标志（已过一轮，2026-10-30）：造一笔带禁用词的提交，经 `infra fire` 触发订阅表里的 gate，拦下退 1；`infra remove gate` 后同一笔放行，fire 打「没有订阅者」退 0——摘闸门的退路是真的。钩子改动本身那笔提交也经新链路走了一遍。剩两周观察与测试。

观察期待看的：日常提交没有误拦漏拦；另一台机器拉到新钩子但没登记订阅时，闸门会静默放行（fire 的立场，不是故障）——这正是反向退路的日常形态。

顺序理由：「本地和云端同一条入口」是这个设计的核心卖点，本地这一半从未接线；没接线就平移，qtcloud 得到的是从未跑过一轮的骨架。

后置代价：跳过本地闭环直接平移，两端各长一版，日后分不清哪边是对的。

## 待验证：云端总线

qtcloud 的 provider 目前只有 `/healthz`，接线方不存在；「云端总线推事件 → `fire`」这条路一次都没走过。

- [ ] 等第一个真实事件源出现，把事件翻译成 `infra fire <事件名>` 跑通一次（宿主为 qtcloud-infra 的 CLI）

达成标志：云端事件经 fire 触发一条真实订阅，与本地走的是同一条命令面。

顺序理由：本地闭环是它的子集——fire 能被 git 钩子调通，就能被总线调通；反过来不成立。

后置代价：本地没跑通就先接云端，出问题时无法判断是驱动方的锅还是 fire 的锅。

## 未收敛处：fire 不带载荷（本机已验，基本收敛）

`fire` 只有一个事件名，订阅的 `run` 是固定命令串，没有数据注入。今天的需求（提交闸门、夜间 cron）都够用；qtcloud 的云端事件天然带载荷（`release.published` 带版本号、`deploy.finished` 带环境名）。

本机已有一条 Redis Streams 总线，接法与实测记在 CLI 仓 [用户指南：事件总线](../../../apps/qtfounder/src/cli/docs/user-guide/infra/event-bus.md)。`contract-changed` 这条事件推上去时带 `domain` 与 `commit`，订阅端把字段放进环境变量再调 `fire`，订阅的命令里用 `$QTFOUNDER_CONTRACT_DOMAIN` 就读到了，**`fire` 的签名一个字没改**。载荷由驱动方带，跟「触发的执行机制属于环境」是同一条。

什么情况算收敛：环境变量注入够云端用就不动形状；真要显式载荷，参照 CloudEvents 的信封形状（id、source、type、data 可选）定——infra 的灵感本就来自它，载荷也走它的答案，不自创字段；先在 qtfounder 侧定了再平移，避免两边各长一版。

剩的三件不在本节：发布端还没接进 `post-commit`，订阅端还没常驻，用户级契约（`~/.config/qtfounder/<域>/`）在任何仓之外没有信号源。三件都在 CLI 仓 `src/cli/ROADMAP.md` 任务一。

## 平移的验收标准

本地闭环跑通之日即可平移。平移时每个 CLI 按 XDG 各有各的表，订阅不共享——表的归属已定，不是开放问题。
