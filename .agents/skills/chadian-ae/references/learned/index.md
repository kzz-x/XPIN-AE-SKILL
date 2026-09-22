# Learned AE Library｜Index

这是经过用户明确批准后，才允许进入的 AE 长期学习库。

## 使用原则

- 本文件只做轻量注册表，不存放长篇参数和完整案例。
- 用户只说“学习这个 AE 工程”时，不允许直接写入这里；先按 `recipes/learn-from-ae-project.md` 生成候选 Learning Pack，并经过 Promotion Gate。
- 只有已批准内容才能登记。
- 后续任务只在 Learned Library Trigger 命中时读取本 Index。
- 命中后只加载最相关的 1–3 张 learned card，不扫描整个 learned 目录。
- 单案例经验默认进入 learned library；只有反复验证、真正通用的规律才考虑提升到核心 Motion / Engineering reference。

## Scope

- `generic`：跨项目通用
- `channel`：频道 / 品牌视觉系统
- `project`：指定项目
- `asset`：指定模板、材质或动画组件

## Card Types

- `composition`
- `motion`
- `material`
- `visual`
- `engineering`

## Registry

当前暂无已批准条目。

后续每条使用短格式登记，例如：

```md
- id: chadian_hw_ui_card_in_01
  type: motion
  scope: channel
  tags: [hardware, ui, card-in, precise]
  source: <AEP / root comp / case>
  path: references/learned/motion/chadian-hw-ui-card-in-01.md
  trigger: 频道硬件部 UI 卡片入场 / 用户点名该 motion
  summary: 12–16f 克制入场，低 overshoot，保留 Graph Editor 主关键帧。
```
