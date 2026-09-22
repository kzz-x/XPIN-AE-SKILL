# 02｜Task Router

先路由，再加载。不要“任务复杂 = 读全部”。

## A｜EXISTING_PROJECT_PATCH
典型：改选中图层、文字 / 颜色 / 尺寸 / 少量关键帧、替换素材、修改一个模块。

加载：
- `workflows/modify-existing.md`
- `workflows/mcp-direct-control.md`（若 MCP）
- 与目标属性有关的 1 个 Engineering / Capability 模块

普通局部动画仍可留 Mini；不要因为出现关键帧就自动升级完整版。

## B｜NEW_PROJECT
加载：
- `workflows/new-project.md`
- `engineering/project-architecture.md`
- `engineering/controls-and-tokens.md`
- `engineering/animation-and-timing.md`
- 任务触发的素材 / 3D / Effects 模块
- `quality/validation.md`

创建新视觉元素 / 动画模块时按下方 AE Expert Preflight 判断是否加载 Native / Relationship 模块。
若 Motion Complexity 达 M2–M4，再走 Motion Router。
若 NEW_PROJECT 同时要求 AI 自己决定设计 / 构图 / Motion，先按 `K｜AE_DESIGN_PLAN` 做 Creative Authority Gate；若视觉与 Motion 已由用户 / 上游 Handoff 锁定，则直接执行，不重复发散。

## C｜JSX_BUILD
加载：
- `workflows/jsx-generation.md`
- 相关工程模块
- `engineering/expressions-and-compatibility.md`

若 JSX 包含复杂共享动画，不要因为“脚本能批量打关键帧”就复制动画；按 Motion Router / Relationship Trigger 加载对应模块。

## D｜DIRECT_MCP
加载：
- `workflows/mcp-direct-control.md`
- 修改现有工程再加 `modify-existing.md`

## E｜HYBRID
加载：
- `workflows/hybrid.md`
- `mcp-direct-control.md`
- `jsx-generation.md`
- 只加载本任务涉及模块

## F｜COMPLEX_3D
加载：
- `capabilities/3d-camera-models.md`
- `engineering/assets-and-replacement.md`
- `engineering/project-architecture.md`
- `engineering/animation-and-timing.md`
- `quality/visual-quality.md`
- 若从零做，再加 `new-project.md`

复杂 Camera / 多对象动画按 M3+ 处理 Motion；Camera Target / Focus 等关系触发 Relationship Rig。

## G｜MOGRT_TEMPLATE
加载：
- `capabilities/mogrt-essential-properties.md`
- `engineering/controls-and-tokens.md`
- `engineering/project-architecture.md`
- `engineering/assets-and-replacement.md`

复杂动画控制再加载 `motion/motion-control-architecture.md`。

## H｜REVIEW_DEBUG
加载：
- `workflows/review-debug.md`
- `quality/validation.md`
- `quality/render-policy.md`
- 再按错误类型加载对应模块

动画质量 / 节奏 / Camera / 关键帧架构 → `quality/animation-qa.md`。
若问题是 Shape 堆砌、错误手工模拟、对象同步困难 → 加载 Native / Relationship 模块。

## I｜ASSET_REFACTOR
典型：整理用户历史 AEP、半模板、小元素动画、复杂插件/表达式工程，使其同时适合人工维护与 AI 低 Token 快速修改。

加载：
- `recipes/refactor-existing-asset.md`
- `workflows/modify-existing.md`
- `engineering/project-architecture.md`
- `engineering/controls-and-tokens.md`
- `engineering/expressions-and-compatibility.md`

规则：
- 先锁定资产根合成，只沿必要依赖读取，不默认全工程扫描；
- 保持最终视觉与动画结果，不借整理之名重做；
- 高频参数集中到明确控制入口，低频参数不要过度暴露；
- 第三方插件允许保留，只记录与映射关键依赖；
- 整理后未来 AI 默认走 `Asset Map / 00_CTRL → 目标图层 → 必要依赖`；
- 批量整理时一次一个 AEP / 一个资产根合成，避免上下文、Undo 和依赖混杂。

这是结构重构，默认完整版；但不自动加载 Motion / 3D / 素材模块，只有真实触发时才追加。


## J｜AE_PROJECT_LEARN
典型：
- “学习这个 AE 工程 / AEP”
- “把这个镜头里的关键帧曲线、构图、材质学下来”
- “提炼这个优秀工程的动效规律”
- “以后照这个工程的审美做”

加载：
- `recipes/learn-from-ae-project.md`
- 若 Motion 是重点，再按需加载 `motion/motion-principles.md` / `motion/motion-profiles.md`
- 共享控制 / 复杂 Motion 才追加 `motion/motion-control-architecture.md`
- 曲线质量需要深度判断时追加 `quality/animation-qa.md`
- 材质 / 插件 / 3D 只有真实触发时才加载对应 Capability

规则：
- 这是完整版任务，但**学习阶段默认只读**，不修改源 AEP；
- 先锁定用户指定的资产根合成，只沿必要依赖读取，不默认扫描整个 Project；
- 优先采样代表性静帧 + 工程结构 + 真实关键帧 / Ease / Effect 证据；
- 把绝对坐标、秒数和参数同时归一化为比例 / 帧数 / 动作百分比，区分 `OBSERVED` 与 `INFERRED`；
- 输出候选 `COMPOSITION_CARD / MOTION_CARD / MATERIAL_CARD / VISUAL_CARD`，工程结构确有价值时再加 `ENGINEERING_CARD`；
- **学习不等于写入 Skill**。先生成候选 Learning Pack；
- 写入 `references/learned/`、修改已有 learned card 或提升到核心 reference 前，必须进入 Promotion Gate，向用户说明候选规律、证据、建议 scope、目标位置、冲突与“新增 / 并存 / 合并 / 覆盖”建议，并明确询问用户；
- 只有用户明确批准的条目才能写入；未确认内容保持候选状态；
- 单案例默认进入 learned library，不直接升级为核心通用规则。

## K｜AE_DESIGN_PLAN
典型：
- “给我出 AE 制作方案 / 动效方案”
- “这镜头适不适合直接让 AI 做”
- “给 Codex 一份执行方案”
- “我先做好模板，AI 接下来做什么”
- “把模板 B 改成模板 A 的液态玻璃 / 插件 / 材质系统”

加载：
- `workflows/creative-authority.md`
- `engineering/ae-implementation-spec.md`
- `engineering/animation-and-timing.md`（有 Motion 时）
- 创建新元素 / 材质时按 AE Expert Preflight 决定是否加 `capabilities/native-ae.md`
- 需要真实参考时走 Reference-First Trigger
- Motion 达 M2–M4 时再按 Motion Router 增量加载，不因“写方案”自动读取整套 Motion

规则：
- 先判断 `AI_DIRECT_BUILD / HUMAN_DESIGN_AI_ENGINEER / HUMAN_MOTION_AI_ASSIST`；
- 再判断 Creative Authority 0–3；默认优先 Authority 1，Authority 3 默认关闭；
- 方案必须同时说明“怎么动”和“AE 里怎么实现”：Keyframe / Expression / Relationship / Native / Layer Style / Plugin / CTRL / 人工可调边界；
- 结构型信息镜头可以 Authority 2 直接生成；Hero / 品牌 Motion / 高级 Typography / 复杂 Camera / 类生物与真实物理默认由人主导；
- 已有模板 / 已批准 AEP / 已确定 Motion 时，AI 以读取、迁移、扩展为主，不重新发明视觉语言；
- 用户只要求执行一个已锁定方案时，不必重复做完整 Creative Authority 讨论，只保留已确定权限边界。

## Creative Authority / Implementation Trigger

以下情况命中 `workflows/creative-authority.md`：
- 从零设计 AE 镜头；
- 判断某镜头是否值得交给 AI；
- 规划 Codex / MCP / JSX 的工作范围；
- 已有模板 / AEP，要扩展、迁移风格或复制 Motion / Material System；
- 用户反馈 AI 构图 / 节奏 / 动画 taste 不稳定，希望改成辅助模式。

以下情况同时命中 `engineering/ae-implementation-spec.md`：
- 要输出 AE 制作方案 / 施工说明；
- 设计阶段需要决定 Keyframe vs Expression；
- 需要明确 wiggle / loop / spring / distance-driven / Follow / Auto Layout 等程序化技巧；
- 需要明确 Layer Style / Native Effect / Plugin / Adjustment Layer / CTRL 的实现方式；
- 需要把设计方案直接交给 Codex 执行。

普通改字 / 改色 / 改参数 / 已有动画小 Patch 不加载这两个模块。

---

## Learned Library Trigger

只有以下情况读取 `references/learned/index.md`：
- 用户明确说“用之前学的 / 用频道风格 / 用这个工程学到的规律”；
- 用户点名某个已学习的 motion / composition / material / visual；
- 当前 Handoff 明确带有 learned tag；
- 当前任务明确属于某个已批准的 channel / project / asset scope。

读取 Index 后只加载最相关的 1–3 张卡，不扫描全部 learned 文件。
简单 Patch 不因为存在 learned library 就额外增加上下文。

---

# AE Expert Preflight Trigger

以下情况加载 `capabilities/native-ae.md`：
- 创建新的视觉元素；
- 创建新的动画模块；
- 从零搭镜头；
- 重构现有结构；
- 用户反馈“太基础 / 太像 Shape 堆砌 / 不好修改”；
- Agent 准备用多个基础层模拟一个视觉效果。

如果只是改文字、颜色、尺寸、已有 Effect 参数：
→ 不额外加载。

以下情况加载 `motion/relationship-rigs.md`：
- 多个对象存在 Follow / Attach / Carry / Target / Connect / Align / Look At；
- 目标位置未来可能变化；
- 多个对象靠独立关键帧人工保持同步；
- 大量重复 Position / Rotation / Scale Keyframe；
- Auto Layout / Dynamic Bounds；
- Camera / Focus 需要跟随目标。

Relationship Rig 本身不自动意味着 M3/M4。简单 Parent / Follow Patch 可以低成本完成。

---

# Reference-First Trigger

以下情况加载 `workflows/reference-first.md`：
- 从零创建新视觉 / 新动画，且参考会显著影响结果；
- 产品 / 品牌 / 设备 / 零件外观必须准确；
- 人、手、动物等类生物动作；
- 复杂机械、装配、液体、金属、碰撞等结构或物理运动；
- UI / HUD / 产品广告 / Camera 需要成熟运动语言；
- 用户反馈“动作不自然 / 太模板 / 一眼 AI / 结构画错”；
- 存在直接复用 PNG / SVG / Lottie / Footage / 3D / Template 的可能。

以下情况通常不加载：
- 改文字 / 颜色 / 尺寸；
- 已有动画的小型 Patch；
- 简单几何 / 数据 / 路径，且运动规律明确；
- 用户明确要求只按现有参考 / 素材执行。

Reference-First 不自动升级 Motion Complexity，也不等于必须搜索互联网；优先读取用户提供、工程已有和本地可用参考，必要时再外搜。

---

# Motion Router

Motion Complexity 只判断 Motion System 加载范围，不替代任务类型路由。

## M0｜无动画
文字、颜色、素材、布局、静帧、纯参数修改。
→ 不加载 Motion 模块。

## M1｜局部简单动画
单层或少量图层；简单关键帧微调；无复杂共享节奏 / Camera / 动画系统。
→ 默认 Mini 或 `engineering/animation-and-timing.md` 足够。
→ 若出现简单 Relationship，可只加载 `relationship-rigs.md`，不必整套 Motion。

## M2｜编排型动画
多个对象需要先后、Stagger、不同运动性格，或 Motion Quality 是重点。

加载：
- `motion/motion-principles.md`
- `motion/motion-profiles.md`

3+ 图层共享同类运动时加：
- `motion/motion-control-architecture.md`

有关联关系时加：
- `motion/relationship-rigs.md`

## M3｜系统型复杂动画
多对象编排、Camera、Parent Rig、Master Progress、共享表达式、重复模块 retime、明显分段动作。

加载：
- `motion/motion-principles.md`
- `motion/motion-profiles.md`
- `motion/motion-control-architecture.md`
- `quality/animation-qa.md`
- 关系触发时 `motion/relationship-rigs.md`

## M4｜大型 / 高风险 Motion System
多合成联动、复杂 Camera + 3D、机械系统、Hybrid / 大型 JSX、模板化 Motion Architecture、深度重构。

→ M3 基础上按任务追加 3D / Expression / Workflow / Capability 模块。
→ 完成前必须做 `quality/animation-qa.md`。

## Motion 升级信号
- 3+ 对象共享同类动画；
- 多对象 Stagger / Overlap；
- Camera 与主体协调；
- UI / 机械 / 文字 / 数据需要不同运动逻辑；
- 用户反馈统一 Easy Ease / 太模板 / 没重量 / 没节奏；
- 大量重复关键帧难以统一修改。

不要因为“有 Overshoot / Easy Ease / 3 个关键帧”就升级。

---

## Speech-Driven Motion Trigger

用户要求按口播 / 旁白 / 音频节奏、自动识别语义点、生成 / 校准 Motion Marker时：
→ 加载 `motion/speech-driven-motion.md`

规则：
- Marker First；
- Marker 不足才 Speech Assist；
- 只分析当前时间线实际使用的 source ranges；
- 自动结果先转 Comp Marker；
- 用户 Marker 永远优先。

Speech-Driven 本身不强制 M3/M4。

---

## 风险等级
LOW：局部参数 / 单层 / 单模块修改 → 不需要方案门禁。

MEDIUM：多个模块、结构调整、明显动画设计 → 先简短计划。

HIGH：新视觉方向、完整场景、复杂 3D、插件依赖、素材路线改变、大面积重构 → 必要时先方案 / 静帧 / 用户确认。

## Grill-me
仅在多条明显不同创意路线、关键条件缺失、技术路线成本差异很大、错一次会大面积返工时触发；最多 1–5 个关键问题。
