# Engineering｜AE BUILD SPEC

> 复杂 AE 任务在“设计方案”和“实际 MCP / JSX 执行”之间的结构化施工合同。它不是最终工程的数据库，也不是强制 JSON；目标是让 Codex 在执行前已经知道：什么锁定、什么可变、工程怎么搭、动作怎么实现、如何验收。

核心：

**Design intent → explicit build contract → deterministic execution.**

## 1｜何时需要

建议使用：
- 从零完整镜头；
- 多模块 / 多合成；
- M2–M4 Motion；
- 用户要求“给 Codex 完整执行提示词”；
- Previs 已通过，准备工程化；
- 模板迁移；
- 复杂素材槽 / 3D / Camera / 插件；
- 重复执行 / 后续持续 Patch 的项目。

不需要：
- M0 小 Patch；
- 只改一个属性；
- 已有模板中非常明确的一次局部替换。

## 2｜最小字段

```text
AE BUILD SPEC

TARGET
- project / comp / fps / duration / aspect

LOCKED
- 用户已确认、不得擅自改的设计、参考、Pose、Timing、事实内容

SOURCE OF TRUTH
- reference / previs / current AEP / house-style / learned card

STRUCTURE
- root comp
- precomps
- important layers
- control layers

ASSETS
- real assets
- placeholders
- replaceable slots
- dependencies

RELATIONSHIPS
- parent / follow / attach / target / connector / dynamic bounds

MOTION PHASES
- frame/time ranges + semantic phase

PRIMARY MOTION
- real keyframes / graph / camera / path

SECONDARY MOTION
- expression / loop / wiggle / spring / follow-through

MATERIAL
- native effects / layer style / adjustment / plugin / blend

CONTROLS
- only high-frequency controls

HUMAN POLISH
- deliberately retained manual adjustment points

VERIFY
- state checks + representative visual times

DO NOT
- explicit anti-goals / forbidden rebuilds
```

只有当前任务需要的字段才填写，不为了模板完整而制造废话。

## 3｜声明式，不等于强制重建

BUILD SPEC 的作用是“声明目标结构”，不是每次把整个 Comp replace。

执行时仍遵循：
- Existing Project → Patch First；
- 新建模块 → 可按 Spec 构建；
- 人工修改后 → 只 Patch 对应 section；
- 不因为 Spec 存在就覆盖用户后来手调的内容。

## 4｜Managed / Manual Boundary

每个复杂 BUILD 应尽量区分：
- `AI_MANAGED`：结构、批量层、可再生模块；
- `HUMAN_TUNED`：Hero Graph、Path、Camera polish、Typography 等；
- `SHARED`：AI 可读取并局部修改，但不能自动重建。

可使用 Comment / AI_ID / ROLE / 文档记录表达边界。

## 5｜Motion Phase Contract

复杂镜头优先把自然语言阶段转成明确时间：

```text
P01  000–018f  主体出现
P02  016–034f  UI stagger
P03  034–052f  状态切换
P04  052–090f  HOLD / explain
```

允许 overlap，不要求阶段首尾严格串行。

Marker 是执行阶段的优先 Timing Contract；BUILD SPEC 记录语义，Marker 落到 AE。

## 6｜实现映射

BUILD SPEC 必须与 `ae-implementation-spec.md` 一致：

- 主 Timing / Hero Motion → Keyframe + Graph；
- 持续 / 程序化行为 → Expression；
- 多对象逻辑 → Relationship；
- 统一风格 → Layer Style / Native Effect / Adjustment / Plugin；
- 高频参数 → CTRL；
- 重复模块 → Precomp / Reuse；
- 复杂共享时序 → Master Progress / Marker / Time Remap。

## 7｜验证计划在执行前写

复杂 BUILD 不到最后才想“怎么看对不对”。

至少先定义：
- 哪些 property 必须 read-back；
- 哪些结构必须 diff；
- 哪些时间点适合 contact sheet；
- 哪些 Motion 必须用户 AE 前台连续播放判断；
- 哪些属于人工 Polish，不以自动 QA 判定“完成”。

## 8｜项目侧保存

当 BUILD SPEC 对后续持续修改明显有价值时，可保存到项目侧文档，例如：
- `_ae/specs/<shot>.build.md`
- 或现有项目约定目录。

不要强制所有临时任务落文件；只有可复用、长期 Patch 的镜头值得保存。

## 9｜最终目标

BUILD SPEC 应让一个新的执行 Agent 在不重新发散创意的情况下回答：

- 我要做什么？
- 什么绝对不能改？
- 哪些对象是什么关系？
- Motion 如何实现？
- 哪些参数以后最常改？
- 哪些地方留给人？
- 怎么证明我没有做错？
