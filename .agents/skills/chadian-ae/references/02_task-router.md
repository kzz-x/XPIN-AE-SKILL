# 02｜Task Router

先路由，再加载。不要“任务复杂 = 读全部”。

## A｜EXISTING_PROJECT_PATCH
典型：
- 改选中图层
- 调文字 / 颜色 / 尺寸 / 少量关键帧
- 替换素材
- 修改一个窗口 / 图标 / 模块

加载：
- `workflows/modify-existing.md`
- `workflows/mcp-direct-control.md`（若 MCP）
- 与目标属性有关的 1 个 Engineering / Capability 模块

普通局部动画仍可留在 Mini；不要因为出现关键帧就自动升级完整版或加载 Motion System。

## B｜NEW_PROJECT
典型：
- 从零搭一个镜头
- 创建完整可编辑工程

加载：
- `workflows/new-project.md`
- `engineering/project-architecture.md`
- `engineering/controls-and-tokens.md`
- `engineering/animation-and-timing.md`
- 任务触发的素材 / 3D / Effects 模块
- `quality/validation.md`

若 Motion Complexity 达 M2–M4，再按下方 Motion Router 增量加载。

## C｜JSX_BUILD
加载：
- `workflows/jsx-generation.md`
- 相关工程模块
- `engineering/expressions-and-compatibility.md`

若 JSX 包含复杂共享动画，不要只因为“脚本能批量打关键帧”就复制动画；按 Motion Router 加载控制架构。

## D｜DIRECT_MCP
加载：
- `workflows/mcp-direct-control.md`
- 若是修改现有工程，再加 `modify-existing.md`

## E｜HYBRID
加载：
- `workflows/hybrid.md`
- `mcp-direct-control.md`
- `jsx-generation.md`
- 只加载本任务涉及的工程模块

## F｜COMPLEX_3D
加载：
- `capabilities/3d-camera-models.md`
- `engineering/assets-and-replacement.md`
- `engineering/project-architecture.md`
- `engineering/animation-and-timing.md`
- `quality/visual-quality.md`
- 若从零做，再加 `new-project.md`

若包含明显 Camera choreography / 多对象动画，至少按 M3 处理 Motion。

## G｜MOGRT_TEMPLATE
加载：
- `capabilities/mogrt-essential-properties.md`
- `engineering/controls-and-tokens.md`
- `engineering/project-architecture.md`
- `engineering/assets-and-replacement.md`

模板若暴露复杂动画控制，再加载 `motion/motion-control-architecture.md`。

## H｜REVIEW_DEBUG
加载：
- `workflows/review-debug.md`
- `quality/validation.md`
- `quality/render-policy.md`
- 再按错误类型加载 1 个对应模块

若问题明确属于动画质量 / 节奏 / Camera / 关键帧架构，直接加载 `quality/animation-qa.md` 和必要的 Motion 模块。

---

# Motion Router

Motion Complexity 只用于判断是否加载 Motion System，不替代任务类型路由。

## M0｜无动画
文字、颜色、素材、布局、静帧、纯参数修改。

→ 不加载 Motion 模块。

## M1｜局部简单动画
单层或少量图层；简单关键帧微调；没有复杂共享节奏 / Camera / 动画系统。

→ 默认 Mini 或 `engineering/animation-and-timing.md` 足够。
→ 不加载完整 Motion System。

## M2｜编排型动画
多个对象需要明显先后、Stagger、不同对象运动性格，或 Motion Quality 本身是任务重点。

→ 若只是少量直接可控的错帧修改，Mini 优先。
→ 若需要设计运动逻辑，加载：
- `motion/motion-principles.md`
- `motion/motion-profiles.md`

→ 若出现 3+ 图层共享同类运动，再加：
- `motion/motion-control-architecture.md`

## M3｜系统型复杂动画
多对象编排、Camera、Parent Rig、Master Progress、共享表达式、重复模块 retime、明显分段动作。

→ 使用完整版并加载：
- `motion/motion-principles.md`
- `motion/motion-profiles.md`
- `motion/motion-control-architecture.md`
- `quality/animation-qa.md`

## M4｜大型 / 高风险 Motion System
多合成联动、复杂 Camera + 3D、机械系统、Hybrid / 大型 JSX、模板化 Motion Architecture、深度动画重构。

→ 在 M3 基础上按任务继续加载对应 3D / Expression / Workflow / Capability 模块。
→ 完成前必须做 `quality/animation-qa.md`。

## Motion 升级信号

出现任一项时提高 Motion Complexity：
- 3 个以上对象共享同类动画；
- 多对象需要分层 Stagger / Overlap；
- Camera 与主体需要协调；
- 机械 / UI / 文字 / 数据混合且应有不同运动逻辑；
- 用户反馈“太像统一 Easy Ease / 太模板 / 没重量 / 没节奏”；
- 工程出现大量重复关键帧，后续很难统一修改。

不要因为“有 Overshoot”“有 Easy Ease”“有 3 个关键帧”就升级。

---

## 风险等级

LOW：
局部参数 / 单层 / 单模块修改
→ 不需要方案门禁

MEDIUM：
多个模块、结构调整、明显动画设计
→ 先简短计划

HIGH：
新视觉方向、完整场景、复杂 3D、插件依赖、素材路线改变、大面积重构
→ 必要时先方案 / 静帧 / 用户确认

## Grill-me

只有以下情况触发：
- 多条明显不同的创意路线；
- 关键条件缺失；
- 技术路线成本差异很大；
- 错一次会导致大面积返工。

最多优先问 1–5 个关键问题。
