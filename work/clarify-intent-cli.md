# 澄清意图命令行契约

一份随协作不断被修订的意图声明；修订模式沉淀成规则。命令统一前缀 `qtfounder work clarify`，noun-verb 风格。方向层见 [clarify-intent.md](./clarify-intent.md)。

## 架构线

HTTP API 是唯一真相，CLI 是薄客户端。skill / CLI / 未来的 MCP 适配都只是同一层的调用方。

```text
skill / CLI / MCP  →  HTTP API  →  SQLite
```

## 概念模型：一个对象，三种状态

只有一个实体 intent，生命周期是 declared → revised → closed。过程中的每次「不对劲」不是新记录，而是对同一份意图的一次 revision。

```text
intent
├── declared:  任务、假定上下文、成功标准
├── revisions: 每次修订（触发、原以为、改为、可粘回提示词）
└── closed:    复盘，触发规则候选
```

用户只需回答一个问题：「我的意图要不要更新？」不用先分类这是偏差还是意图。

## 数据契约

intent：

```json
{
  "id": "int_01J9...",
  "created_at": "2026-10-18T09:00:00Z",
  "task": "给这段代码写单元测试",
  "assumed_context": "它知道项目用 pytest、fixture 在 conftest",
  "success_criteria": "能跑通、覆盖边界、不引入新依赖",
  "status": "declared",
  "closed_at": null,
  "revisions": []
}
```

revision（内嵌在 intent 里）：

```json
{
  "id": "rev_01J9...",
  "at": "2026-10-18T09:12:03Z",
  "trigger": "它给了五段解释加伪代码",
  "before": "我以为它知道我要能直接跑的代码",
  "after": "先给三行以内可运行代码，解释我追问再要",
  "prompt": "刚才你的回答偏在铺陈展开……"
}
```

rule：

```json
{
  "id": "rule_01J9...",
  "created_at": "2026-10-18T09:40:00Z",
  "text": "先结论后细节，除非我明确要展开",
  "source_revisions": ["rev_01J9...", "rev_01JZ..."]
}
```

字段取舍：trigger 是聚类键（同类偏差靠它归组），before/after 是上下文差的显性化，prompt 是可直接粘回对话的产出——这四个字段撑起全部功能，不多加。

## 命令结构

```text
qtfounder work clarify intent declare      开始前：声明意图
qtfounder work clarify intent revise       过程中：追加一次修订
qtfounder work clarify intent close        结束时：复盘，触发规则候选
qtfounder work clarify intent show <id>    单条详情（可控制 revisions 展开数）
qtfounder work clarify intent list         列出 intent
qtfounder work clarify revision list       跨 intent 的扁平修订视图
qtfounder work clarify rule list           列规则
qtfounder work clarify rule promote        把一组 revision 提升为规则
qtfounder work clarify rule candidates     找重复 ≥2 次的修订类别
qtfounder work clarify schema [cmd]        输出 JSON Schema
qtfounder work clarify config              查看/设置 server 地址
```

存储嵌套，视图扁平：`intent show` 看嵌套结构；`revision list` 给跨 intent 的平铺视图，解决「全局看所有修订」的需求。

## 输入侧：flag + --stdin

```bash
# 人用
qtfounder work clarify intent declare \
  --task "给这段代码写单元测试" \
  --context "项目用 pytest，fixture 在 conftest" \
  --criteria "能跑通、覆盖边界、不引入新依赖"

# 模型用：整条 JSON 走 stdin，字段名与输出一致
echo '{"task":"...","assumed_context":"...","success_criteria":"..."}' \
  | qtfounder work clarify intent declare --stdin
```

revise 同理，`--intent <id>` 指定挂靠对象：

```bash
qtfounder work clarify intent revise --intent int_01J9 --stdin <<'JSON'
{
  "trigger": "它给了五段解释加伪代码",
  "before": "我以为它知道我要能直接跑的代码",
  "after": "先给三行以内可运行代码，解释我追问再要",
  "prompt": "刚才你的回答偏在铺陈展开……"
}
JSON
```

幂等：revise 用 sha256(trigger + before + after) 去重，同一 intent 下重复提交返回已有 revision，标 `"deduplicated": true`，退出码 0。模型重试安全。

## 输出侧：人读默认，--json 给机器

- `--json` 输出是稳定契约，字段只增不删，版本在 schema 里标。
- 错误一律 JSON 写 stderr：

```json
{"error":{"code":"NOT_FOUND","message":"int_01J9 不存在","hint":"用 qtfounder work clarify intent list 查看"}}
```

- 退出码固定：

| 码 | 含义 |
|----|------|
| 0 | 成功（含幂等去重） |
| 2 | 参数/用法错误 |
| 3 | 未找到 |
| 4 | 冲突（如 intent 已 closed 仍 revise） |
| 5 | server 不可达 |

## 核心命令行为

- `intent revise` —— 唯一有副作用的写入路径。校验 `intent.status != closed`，否则返回冲突码 4，提示「先 reopen 或新建 intent」。
- `rule candidates` —— 对 revision 的 trigger 归一化（小写、去标点、去空白）聚类，count ≥ 2 的组返回：

```json
{
  "candidates": [
    {
      "trigger_key": "给了大段铺陈而非可运行代码",
      "count": 3,
      "revision_ids": ["rev_...", "rev_...", "rev_..."],
      "intent_ids": ["int_...", "int_..."]
    }
  ]
}
```

- `rule promote` —— 必须显式给出来源和规则文本，工具不自动生成规则（守住「规则由用户点头才落库」）：

```bash
qtfounder work clarify rule promote \
  --revisions rev_01J9,rev_01JZ \
  --text "先结论后细节，除非我明确要展开"
```

  成功后回写这些 revision 的 rule_id。
- `intent close` —— 关闭动作本身就是复盘仪式：列出该 intent 下所有 revision，按 trigger 聚类，≥2 次浮出候选。不自动关闭，不自动立规则。

## skill 怎么调

skill（[clarify-intent-skill.md](./clarify-intent-skill.md)）只是一段提示词流程，落地调用就三条：

```bash
qtfounder work clarify revision list --similar "$TRIGGER" --json   # 1. 有没有同类修订
qtfounder work clarify intent revise --intent "$ID" --stdin        # 2. 记这次修订
qtfounder work clarify rule candidates --json                      # 3. 够不够格立规则
```

和模型跑 `gh pr view --json` 没有区别——这就是 AI 原生 CLI：结构化入参、稳定 JSON 出参、语义化退出码。

## 自描述：schema

```bash
qtfounder work clarify schema                     # 全部命令的 schema 总表
qtfounder work clarify schema intent.revise       # 单命令入参/出参 JSON Schema
```

这份 schema 就是未来 MCP tool 定义的同一份东西；加 MCP 适配时原样注册，不重写。

## HTTP API

路径与 CLI 的名词路径对齐，统一以 `/work/clarify` 为前缀：

| 方法 | 路径 | 用途 |
|------|------|------|
| POST | `/work/clarify/intents` | 声明意图 |
| POST | `/work/clarify/intents/{id}/revisions` | 追加修订（幂等） |
| GET | `/work/clarify/intents/{id}` | 单条详情 |
| GET | `/work/clarify/revisions?similar=` | 同类修订 |
| GET | `/work/clarify/rules/candidates` | 规则候选 |
| POST | `/work/clarify/rules` | 立规则 |
| GET | `/work/clarify/schema` | JSON Schema |

## MVP 范围

第一版做：上述 11 条命令（含 `config`）、单进程 server + SQLite、`--json` + schema + 结构化错误 + 幂等、intent / revision / rule 三张表。

第一版不做：向量检索（用字符串/关键词打分）、多用户、同步、MCP 适配、自动生成规则文本、被动触发、reopen 语义（先只做「新建」）。

## 落地顺序

1. 先定 API 契约：上表六组接口；
2. server + SQLite 起一版，跑通「declare → revise → candidates → promote → close」整条链；
3. CLI 做薄客户端，`--json` 直接透传 server 响应；
4. skill 里写死那三条命令，跑两周，验证核心假设：用户会不会真的把 prompt 粘回对话。

核心指标只有一个：生成的 prompt 被粘回对话的比例。比例高，工具成立；低，说明「显性化上下文差」这一步没翻译成功，回去改 revise 的引导，而不是加功能。

## 存储

初版按 server 侧 SQLite 单库设计。是否改为第二大脑 `data/context/<集>/intention/` 的 Markdown（白箱、可编辑器看改、随仓库迁移），列为待决策，见 [clarify-intent.md](./clarify-intent.md)。
