# 澄清意图命令行

统一前缀 `qtfounder work clarify`，noun-verb 风格。方向层见 [clarify-intent.md](./clarify-intent.md)。

## 命令

| 命令 | 干什么 |
|------|--------|
| `qtfounder work clarify declare` | 开始前：声明意图（任务、假定上下文、成功标准） |
| `qtfounder work clarify revise` | 过程中：追加一次修订 |
| `qtfounder work clarify close` | 结束时：复盘，把重复 ≥2 次的差浮成候选 |
| `qtfounder work clarify list [--id <id>]` | 列 intent；带 `--id` 出单条详情 |
| `qtfounder work clarify candidates [--similar <触发>]` | 找重复 ≥2 次的修订类别 |
| `qtfounder work clarify promote` | 把一组 revision 立成一条规则（追加进 `rules.md`） |

HTTP 路径与命令同名同序，CLI 直接透传，不另设一套契约：`POST /work/clarify/intents`、`POST /work/clarify/intents/{id}/revisions`、`GET /work/clarify/intents`、`GET /work/clarify/candidates`、`POST /work/clarify/rules`。

## 字段

intent：`task`（要干什么）、`assumed_context`（假定它知道什么）、`success_criteria`（什么算干成）、`status`（declared / revised / closed）。

revision：`trigger`（这次哪里不对劲，也是聚类键）、`before`（原以为）、`after`（改为）、`prompt`（可直接粘回对话的一段话）。这四个字段撑起全部功能，不加。

`id` 自动生成；`closed_at` 只有 close 之后才有值。

## 用法

```bash
# 人用
qtfounder work clarify declare \
  --task "给这段代码写单元测试" \
  --context "项目用 pytest，fixture 在 conftest" \
  --criteria "能跑通、覆盖边界、不引入新依赖"

# 模型用：整条 JSON 走 stdin，字段名与输出一致
echo '{"task":"...","assumed_context":"...","success_criteria":"..."}' \
  | qtfounder work clarify declare --stdin

qtfounder work clarify revise --intent int_01J9 --stdin <<'JSON'
{"trigger":"它给了五段解释加伪代码","before":"我以为它知道我要能直接跑的代码","after":"先给三行以内可运行代码","prompt":"刚才你的回答偏在铺陈展开……"}
JSON

qtfounder work clarify promote \
  --revisions rev_01J9,rev_01JZ \
  --text "先结论后细节，除非我明确要展开"
```

`candidates` 返回 count ≥ 2 的组，每组给 `trigger_key`、`count`、`revision_ids`。

`--json` 给机器：字段只增不删，错误也走 JSON、写 stderr。`revise` 用 sha256(trigger + before + after) 去重，重复提交返回已有 revision 并标 `deduplicated`，退出码 0——模型重试安全。

退出码三档：0 成功（含去重）、2 用法错、4 状态冲突（比如 intent 已 closed 还要 revise）。

## 落地顺序

1. 先定 HTTP 契约（上面五个路径）；
2. server + SQLite 起一版，跑通 declare → revise → candidates → promote → close 一条链；
3. CLI 做薄客户端，`--json` 直接透传；
4. skill 里写死 revise 那一条命令，跑两周，看两个数：prompt 粘回率、规则点头率。

第一版不做：向量检索（先用关键词打分）、多用户、同步、MCP 适配、reopen（只做新建）。

## 存储

初版按 server 侧 SQLite 单库设计。是否改为第二大脑 `data/context/<集>/intention/` 的 Markdown，见 [clarify-intent.md](./clarify-intent.md) 的「待决策」。
