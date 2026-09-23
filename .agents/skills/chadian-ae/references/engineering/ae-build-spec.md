# 工程｜AE 执行合同（AE BUILD SPEC）

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
AE 执行合同｜AE BUILD SPEC

目标｜TARGET
- 工程 / 合成 / 帧率 / 时长 / 画幅

锁定项｜LOCKED
- 用户已确认、不得擅自改的设计、参考、关键姿态（Pose）、时序（Timing）、事实内容

事实依据｜SOURCE OF TRUTH
- 参考 / 预演（Previs）/ 当前 AEP / 频道规范 / 已批准学习卡

工程结构｜STRUCTURE
- 根合成 / 预合成 / 重要图层 / 控制层

素材｜ASSETS
- 真实素材 / 占位素材 / 可替换素材槽 / 依赖

关系｜RELATIONSHIPS
- 父子 / 跟随 / 吸附 / 目标 / 连线 / 动态边界

动画阶段｜MOTION PHASES
- 帧或时间范围 + 中文语义阶段

主动画｜PRIMARY MOTION
- 真实关键帧 / 曲线 / 镜头 / 路径

次级动画｜SECONDARY MOTION
- 表达式 / 循环 / 漂浮（wiggle）/ 弹性（spring）/ 跟随收尾

材质｜MATERIAL
- 原生效果 / 图层样式 / 调整层 / 插件 / 混合模式

控制项｜CONTROLS
- 只暴露高频参数，用户可见名称中文优先

人工精修｜HUMAN POLISH
- 明确保留给人工调整的点

验证｜VERIFY
- 状态回读 + 代表性视觉时间点

撤销策略｜UNDO STRATEGY
- BUILD 默认单次 / 少量执行；内部约 3–6 个逻辑 UndoGroup
- 禁止最外层总 UndoGroup
- 小 PATCH 保留 Undo
- 大型 BUILD 只有在恢复点与正式保存都验证成功后才允许 Purge Undo Cache

禁止项｜DO NOT
- 明确禁止的做法 / 不允许的重建
```

只有当前任务需要的字段才填写，不为了模板完整而制造废话。

## 3｜声明式，不等于强制重建

BUILD SPEC 的作用是“声明目标结构”，不是每次把整个 Comp replace。

执行时仍遵循：
- Existing Project → Patch First；
- 新建模块 → 可按 Spec 构建；
- 人工修改后 → 只 Patch 对应 section；
- 不因为 Spec 存在就覆盖用户后来手调的内容。

## 4｜AI / 人工边界（Managed / Manual Boundary）

每个复杂 BUILD 应尽量区分：
- `AI_MANAGED`：结构、批量层、可再生模块；
- `HUMAN_TUNED`：Hero Graph、Path、Camera polish、Typography 等；
- `SHARED`：AI 可读取并局部修改，但不能自动重建。

可使用 Comment / AI_ID / ROLE / 文档记录表达边界。

## 5｜动画阶段合同（Motion Phase Contract）

复杂镜头优先把自然语言阶段转成明确时间：

```text
P01  000–018f  主体出现
P02  016–034f  UI stagger
P03  034–052f  状态切换
P04  052–090f  停留 / 说明｜HOLD / EXPLAIN
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

## 8｜撤销封存计划

复杂 BUILD 在执行前就明确：
- 本任务是否属于大型 BUILD，需要完成后封存 Undo；
- 修改前恢复点如何建立与验证；
- BUILD 内部准备拆成哪 3–6 个逻辑 UndoGroup；
- 是否可以在单次 JSX 内完成，避免无意义 MCP 往返；
- 哪些验证通过后才能保存并 Purge；
- 哪些情况必须保留 Undo。

默认顺序：
`Backup → Build → Verify → Save → Re-check Backup → Purge Undo`

小 PATCH 不进入该流程。

## 9｜项目侧保存

当 BUILD SPEC 对后续持续修改明显有价值时，可保存到项目侧文档，例如：
- `_ae/specs/<shot>.build.md`
- 或现有项目约定目录。

不要强制所有临时任务落文件；只有可复用、长期 Patch 的镜头值得保存。

## 10｜最终目标

BUILD SPEC 应让一个新的执行 Agent 在不重新发散创意的情况下回答：

- 我要做什么？
- 什么绝对不能改？
- 哪些对象是什么关系？
- Motion 如何实现？
- 哪些参数以后最常改？
- 哪些地方留给人？
- 怎么证明我没有做错？
