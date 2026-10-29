---
genre: roadmap
audience: maintainer
---

# qtfounder 路线图

量潮创始人平台（qtfounder）。状态报告在 qtfounder 仓根的 `STATUS.md`（不进文档站），studio 的应用内步骤见该仓 `src/studio/ROADMAP.md`。

## CLI v0.1

当前 `cli/v0.0.41`。检查器（`code`/`language`/`docs`）与认知侧（`think`）已在 0.0.x 落地，阈值已标定，范围只剩退出码收口与验收打 tag。

### 范围

- [ ] 退出码收口：全仓统一四码——0 无命中、1 有命中、2 用法或环境错、3 目标缺席（现 `docs` 为 0/1/2、`product` 为 2/3 两套并存）；语义定义与实现在 toolkit 仓 `packages/rust/ROADMAP.md` 的 core/exit 一节，CLI 只消费

取法与反样本目录见[文档审计路线图](docs.md)「阶段三」。

### 不做

- 自动改写：改写需要判断，属于 AI 的活，工具只出报告
- 多格式：Markdown 没跑通之前不做 docx 与 HTML
- `auto` 推断 Profile：黑盒不可调试，手动 `--rules` 一行参数的事

### 验收

拿真实文档跑一遍，报告贴回 AI，它能按位置改完，复跑达标；`cargo test` 全绿；打 tag 前跑 `qtfounder agent skill install check`，确认 Skill 与命令面的漂移报得出来——Skill 正文写死命令用法，属兼容承诺的一部分；打 `cli/v0.1.0`。

## 迁移

把当前的 studio 实现移动到 qtcloud 并且清空现有实现，以方便重新定义一个更自由的版本。

### 资产

- [ ] 先封装到 toolkit 仓库，然后迁移到 qtcloud-asset
- [ ] 评估是否以及如何基于 OSS 的资产管理功能合并

### 写作

- [ ] 封装到 toolkit 仓库，然后对比写作云

## qtcloud 组织验证

验证 CLI 的机械层能替 qtcloud 收走多少人工判断、误报漏报各多少。先量再接：契约没标定就装闸门，噪音会把闸门变成摆设。本节只记顺序与验收，不急着实现。

### 三步

- [ ] 探底噪：qtcloud 全量跑 `audit` 与 `docs format`，数出存量命中分布；据此写 qtcloud 专用契约，排除目录先排掉，密度阈值先只观测不拦截
- [ ] 真实交付对照：下一次 studio 功能，agent 全流程产出 spec、mockup 与走查记录，人照常审；数三个数——工具拦下的、人拦下而工具与 agent 漏的、误报让人白读的
- [ ] 漏率刻度：`audit --patrol` 扩到 qtcloud，抽历史提交用今天的契约重判，量出当年漏过多少；此后定期复跑，作「保持可靠」的标尺

为什么这么排：没有底噪分布，契约的排除目录与阈值就是拍脑袋；没有对照实验，「可靠」只是感觉不是数字；没有 patrol 基线，后续复跑没有参照。

后置代价：跳过前两步直接装闸门，误报会把人推回全量人工读，闸门反而成了负担。

### 缺口

先记着，不在这次范围：

- `design review` 只认 HTML 的 CSS，扫不到 Flutter 的 `TextStyle`；要覆盖 studio 需扩 Dart 解析
- 十条走查原则没有落盘：agent 走查完结论留在对话里，人抽查无从核对；补法是走查记录随交付物一起交
- `code` 判的是下一个维护者读不读得懂，不判正确性；不能拿它当测试套件的替代品

## 新增

使用 quanttide-founder 的结构迁移到私有第二大脑 iGuo，统一管理公私两个结构。探索未来如何管理组织层面的公开和私有结构。

### 资产结构

- [ ] 公开和私有第二大脑都有 fiction 和 memory，且前者是成品，后者是素材；搭建一个稳定且显式的结构有利于同构维护

### 创意写作

- [ ] fiction 都来源于 memory，且私有的结构还没有完全建立起来；需要一个精细的结构来加工它
