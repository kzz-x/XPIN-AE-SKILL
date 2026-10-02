# 02｜Task Router

> **非启动必读。** Full `SKILL.md` 的 Quick Router 足够时不要读取本文件。只有任务分类模糊、多个模块同时命中或需要判断 Motion Complexity 时读取。

## Context Budget

- 先判断任务，再加载 reference；
- 执行前默认新增 0–2 个最直接相关模块；
- 不因“可能有用”读取整个目录；
- 已足够执行就停止；
- 故障 reference 只在真实故障后加载。

## 1｜任务类型

### EXISTING_PROJECT_PATCH
现有工程局部修改。
- 小范围 → 应优先 Mini。
- 结构复杂才加载 `workflows/modify-existing.md`。
- Engine Room 专属行为按真实需要读取对应 adapter 子文档。

### NEW_PROJECT / COMPLETE_SHOT
从零创建完整镜头 / 工程。
按需：
- 创作权限 → `workflows/creative-authority.md`
- 未锁视觉 → `workflows/visual-anchor.md`
- 真实参考 → `workflows/reference-first.md`
- 现成资产 → `workflows/asset-first.md`
- 实现翻译 → `engineering/ae-implementation-spec.md`
- 完整多图层动画 / 强调可编辑工程 / 少关键帧 / Parent / Override → `engineering/editable-engineering-gate.md`

不要默认全部加载。

### JSX_BUILD / HYBRID
只有大型批量 / DOM 缺口才使用。
- 一般 JSX → `workflows/jsx-generation.md`
- AE26 特定脚本坑 → `engineering/ae26-scripting-gotchas.md`
- Engine Room 大型 Undo → `adapters/engine-room/jsx-undo.md`

### ASSET_REFACTOR
→ `recipes/refactor-existing-asset.md`

### TEMPLATE_STYLE_PACK
→ `recipes/templateize-style-pack.md`
需要模板库结构细节时再加 `engineering/human-ai-template-library.md`。

### AE_PROJECT_LEARN
→ `recipes/learn-from-ae-project.md`
学习默认只读；长期晋升仍需用户批准。

### REVIEW_DEBUG
先根据症状读取最小模块。
Engine Room 错误不要一口气读整个 Adapter：
- connection → `adapters/engine-room/connection-recovery.md`
- JSX/Undo → `adapters/engine-room/jsx-undo.md`
- screenshot → `adapters/engine-room/screenshot.md`
- other → `adapters/engine-room/troubleshooting.md`

## 2｜Reference / Asset / Visual Anchor

### Reference First
触发：真实产品结构、复杂机械 / 生物 / 物理运动、成熟 UI/HUD/Camera 语言等。
→ `workflows/reference-first.md`

### Asset First
触发：Logo、SVG、Lottie、3D Model、HDRI、Texture、Material、Template / Component。
→ `workflows/asset-first.md`

### Visual Anchor
触发：从零构图、中高视觉复杂度、2.5D/3D、材质光影决定质量、口播转视觉且画面未锁。
→ `workflows/visual-anchor.md`

三者互不自动绑定。已有锁定结果时直接复用。

## 3｜Motion Complexity

### M0
无动画 / 静态参数。
→ 不加载 Motion。

### M1
单层 / 少量图层简单动画。
→ Mini 优先；无需 Motion System。

### M2
多个对象 Stagger / Overlap / 运动性格差异明显。
→ `motion/motion-principles.md`
需要类型差异再加 `motion/motion-profiles.md`。

### M3
3+ 对象共享 Motion、Master Progress、Parent Rig、Camera、Precomp retime。
→ 在 M2 基础上按需加：
- `engineering/editable-engineering-gate.md`（新建 / 重构且可编辑性是交付要求时优先）
- `motion/motion-control-architecture.md`
- `motion/relationship-rigs.md`

### M4
多合成、复杂 3D / Camera、机械系统、大型 Hybrid / 模板化 Motion Architecture。
→ M3 基础上只加真实命中的 3D / Engineering / QA 模块。

不要因为有 Overshoot、Easy Ease 或几个关键帧就升级。

## 4｜Speech Trigger

用户要求按口播 / 旁白 / 音频自动识别节奏、语义点、Source↔Comp 时间映射或生成 Marker：
→ `motion/speech-driven-motion.md`

已有足够 Marker 时直接用 Marker，不做 ASR。

## 5｜能力不确定时

只有真的不知道 AE 原生 / MCP 能否实现，才读：
→ `01_capability-map.md`

不要把 Capability Map 当固定前置。

## 6｜复杂执行合同

完整复杂镜头、M3–M4、多模块高风险 Build，且结构/关系容易返工时：
→ `engineering/ae-build-spec.md`

简单明确任务不要机械生成完整 Build Spec。

## 7｜Pattern / Learned

- 复用已验证 Rig / Expression / JSX / Layout → 先读 `patterns/index.md`，只读命中卡。
- 需要已批准审美规律 → 先读 `learned/index.md`，只读命中卡。

禁止扫描全部长期库。
