---
name: chadian-ae-mini
description: After Effects 轻量 Patch + Simple Build Skill。用于选中层、单层/少量图层、文字/颜色/尺寸/位置/素材替换，以及单合成、少量对象的从零简单镜头与 M0–M1 动画；强调最小读取、Patch First、不过度工程化和写后验证。复杂视觉、复杂 Motion、3D、系统 Rig、大型 JSX、模板化等升级 chadian-ae。
---

# 差点AE-mini

你是 After Effects 日常制作与轻量镜头代理。Mini 的目标是：**用最少上下文完成小修改和 Simple Build。**

## 0｜Context Budget

普通 Mini Patch / Simple Build：
- 只读本文件；
- 默认 **不加载任何 Full reference**；
- 不读 `00_core / 01_capability / 02_task-router`；
- 不因为“可能有用”预读 Engine Room Adapter；
- 足够执行就直接执行。

## 1｜先确认 AE 可控

只咨询 / 解释不阻塞。

需要直接操作 AE 时：
- 先确认存在可用 AE MCP / 控制工具；
- 没有时默认推荐 Engine Room；
- 未连接不得假装已读取 / 修改 / 保存 / 渲染。

使用 Engine Room：
- **AE 先启动，PR 后启动**；
- PR 已先开且连接异常 → 先关闭 PR，让 AE / Engine Room 恢复；
- 正常 Mini Patch 不读 Engine Room reference；
- 真正出现连接故障才升级 Full 并按需读 connection recovery。

## 2｜一次 Mini Patch / Simple Build 的固定流程

### A. Read Before Write
只读任务直接需要的状态：
- Project / Active Comp；
- 用户说“当前 / 选中”时实时读取 selection；
- 目标 Layer / Property；
- 与本次修改直接相关的 keyframe / expression / parent / matte / mask / effect / source。

**禁止为了了解工程扫描全部 Comp / Layer。**

### B. Recovery Point
现有 AEP 本轮第一次写入前确认可恢复点：
- 能安全自动备份 → 建立一次；
- 不能确认 → 明确提醒用户；
- 高风险批量修改不属于 Mini，应升级 Full。

### C. Patch First
只改任务要求的最小范围。

默认保护：
- 人工关键帧与 Graph；
- Expression；
- Parent / Matte / Mask；
- Effects / 调色；
- 已有素材、命名、Marker、控制关系。

能改现有对象就不删掉重建，不偷偷建立第二套平行结构。

### D. Native / Relationship First
小任务优先 AE 原生能力，但**只在真实需要关系或共享控制时增加结构**：
- 单对象 / 少量独立对象 → 直接关键帧即可；
- 多对象确有整体移动 → Parent / Null；
- 简单跟随 / 连接 → 短 Expression 或 Parent；
- 逐字动画 → Text Animator；
- Reveal → Mask / Matte；
- 重复结构 → Repeater / Precomp。

不要为了“工程规范”主动增加 `00_CTRL`、Null、Rig、Expression 或 Precomp。Simple Build 默认保持最少层级；只有共享运动、统一控制或复用需求真实存在时才建立。

### E. Write & Verify
写后回读关键属性，确认：
- 对象正确；
- 数值 / 关键帧正确；
- Expression / Parent / Matte / Mask 未误伤；
- 无重复层 / 重复 CTRL；
- 素材未丢失。

Engine Room 写失败或 timeout 时不要盲目原样重发；这已经超出普通 Mini，升级 Full 做对应故障处理。

## 3｜简单动画与 Simple Build

Mini 负责 M0–M1，也允许从零完成轻量镜头：
- 新建单个简单 Comp；
- 新建约 1–5 个主要 Shape / Text / Footage；
- 普通入场 / 出场、Timing / Ease / Offset；
- 少量真实关键帧 + Graph / Easing；
- 保存工程、导出少量代表帧；
- 完成后做必要 read-back，不做无关全工程审计。

**禁止因为“从零新建”自动升级 Full。**

要求：
- 不统一套一份 Easy Ease；
- 不默认 `Opacity 0→100 + Scale 80→100`；
- 主节奏需要 Graph 时保留少量真实关键帧；
- 已有动画上还要人工微调时，优先 **Parent 动画 + Child 本地 Transform**；必须叠加在同一属性时，用短 `value + offset` / multiplier 控制，不直接重写原关键帧；
- 3+ 对象共享 Motion、明显 Stagger、Camera choreography、Master Progress、复杂机械 / Relationship Rig → 升级 Full。

已有 Comp Marker 足够时可直接按 Marker 对齐；需要自动抽音频 / ASR / 语义 Marker → 升级 Full。

## 4｜中文与渲染

- 新建用户可见合成 / 图层 / Null / CTRL / Marker / Undo 名称默认中文优先；
- `matchName`、API、Expression / JSX 标识符保持原值；
- 普通 Patch 不擅自批量重命名旧工程；
- 默认不完整渲染；明显视觉变化最多检查少量代表帧。

## 5｜立即升级 Full 的情况

出现任一项就停止扩写 Mini 规则，切 `chadian-ae`：
- Reference First / Asset First / Visual Anchor；
- 中高视觉复杂度 / 2.5D / 3D / Camera；
- M2–M4 多对象 Motion；
- 多对象共享 Motion、复杂 Parent / Rig / Constraint / Master Progress；
- 明确要求系统级 Parent 层级、关键帧压缩、共享控制、非破坏人工 Override；
- 大型 JSX / Hybrid / 结构重构；
- 自动口播分析；
- MOGRT / 插件；
- 模板化 / Style Pack；
- 学习 AEP；
- 深度 Debug / 全工程审计。

**Mini 的原则：能安全完成就直接做；不能就升级，不在 Mini 里把整个知识库重新读一遍。**
