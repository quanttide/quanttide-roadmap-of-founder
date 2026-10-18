# 澄清意图命令行

只有一条命令 `qtfounder work clarify`，其余是它的开关。方向层见 [clarify-intent.md](./clarify-intent.md)。

## 命令

| 调用 | 干什么 |
|------|--------|
| `qtfounder work clarify` | 记一次澄清：问你「原以为它知道什么 / 它实际做了什么」，给你一段可粘回对话的话 |
| `qtfounder work clarify --done` | 这次收尾：把重复 ≥2 次的差浮成规则候选 |
| `qtfounder work clarify --list` | 看历史记录与已立的规则 |
| `qtfounder work clarify --rule "<文本>"` | 把候选立成一条规则，追加进 `rules.md` |

不带参数就是主流程，开始前、过程中、结束后都走它：当前有未收尾的任务就续记，没有就新开。用户不需要知道阶段名。

HTTP 是唯一真相，CLI 只做透传，五个路径与开关同序：`POST /work/clarify/records`（记一次）、`GET /work/clarify/records`（历史）、`GET /work/clarify/candidates`（候选）、`POST /work/clarify/rules`（立规则）、`POST /work/clarify/done`（收尾）。

## 字段

一条澄清记录四个字段：`task`（这件事要干什么）、`before`（我原以为它知道 / 会做什么）、`actual`（它实际做了什么，也是归类键）、`prompt`（可直接粘回对话的一段话）。

`at` 自动记时间。没有第五个字段——撑起全部功能的就是这四个，不加。

## 用法

```bash
# 人用：进流程，逐问作答
qtfounder work clarify

# 模型用：一次给全，JSON 走 stdin，字段名与输出一致
echo '{"task":"给这段代码写单元测试","before":"它知道项目用 pytest、fixture 在 conftest","actual":"给了五段解释加伪代码","prompt":"刚才你的回答偏在铺陈展开……"}' \
  | qtfounder work clarify --stdin

qtfounder work clarify --done            # 收尾，看有没有够格的候选
qtfounder work clarify --list            # 翻历史
qtfounder work clarify --rule "先结论后细节，除非我明确要展开"
```

`--json` 给机器：字段只增不删，错误也走 JSON、写 stderr。重复提交用 sha256(task + before + actual) 去重，返回已有记录并标 `deduplicated`，退出码 0——模型重试安全。

退出码三档：0 成功（含去重）、2 用法错、4 冲突（记录已收尾还要续记）。

## 落地顺序

1. 先定 HTTP 契约（上面五个路径）；
2. server + SQLite 起一版，跑通「记一次 → 收尾 → 候选 → 立规则」一条链；
3. CLI 做薄客户端，`--json` 直接透传；
4. skill 里写死这一条命令，跑两周，看两个数：prompt 粘回率、规则点头率。

第一版不做：向量检索（先用关键词打分）、多用户、同步、MCP 适配。

## 存储

初版按 server 侧 SQLite 单库设计，一张记录表 + `rules.md`。是否改为第二大脑 `data/context/<集>/intention/` 的 Markdown，见 [clarify-intent.md](./clarify-intent.md) 的「待决策」。
