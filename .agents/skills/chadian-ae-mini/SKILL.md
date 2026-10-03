---
name: chadian-ae-mini
description: 极轻量 After Effects 修改规则。仅在确有需要时读取；禁止自动读取完整 AE Skill、references、recipes 或递归扫描仓库。
---

# 差点 AE Mini｜低 Token 模式

## 最高优先级
- **不要自动读取任何完整版 AE Skill / reference / recipe。**
- **不要递归扫描本仓库。**
- 复杂任务也不得自动升级 Full；只有用户明确要求“启用/读取完整版 AE Skill”才允许。
- 普通 AE 咨询、方案、提示词：无需读取任何 Skill。

## 直接操作 AE 时
1. **Read Before Write**：只读取当前任务直接需要的 Comp / Layer / Property / Selection。
2. **Patch First**：能改现有对象就不重建，不扫描整个工程。
3. **Preserve Manual Work**：保护已有关键帧、Graph、Expression、Parent、Matte、Mask、Effects、素材与人工调整。
4. 现有工程首次写入前优先保留恢复点；高风险批量修改先停下说明风险。
5. 写后只回读关键状态验证，不做无关全工程审计。

## 动画与工程可编辑性
- 优先少量关键帧 + Graph/Easing，不逐帧堆关键帧。
- 多对象关系优先 Parent / Null / 控制层，保持父子层级清楚。
- 已有动画上需要人工偏移时，优先 Parent Offset；必要时使用简短 `value + offset` / multiplier，而不是覆盖原动画。
- 优先 AE 原生能力：Text Animator、Mask/Matte、Repeater、Precomp、Expression、Essential Properties。
- 新建用户可见对象默认中文命名；API / matchName 保持原值。
- 默认不整段渲染，只检查必要代表帧。

## Engine Room
使用 Engine Room 时：**先启动 AE 并确认 MCP 正常，再启动 Premiere Pro。**
若 PR 先开后出现端口/连接异常，第一步先关 PR，让 AE / Engine Room 恢复。

Mini 的职责只有这些。没有明确必要时，宁可不读 Skill。
