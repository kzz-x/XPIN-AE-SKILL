# XPIN AE Skill

Codex / Agent 使用的 After Effects Skill 仓库。

本仓库同时保存：

- `chadian-ae-mini`：默认轻量版，适合大多数日常 AE 修改，减少上下文占用。
- `chadian-ae`：完整模块化版，适合复杂工程、完整镜头、3D、素材/插件/MOGRT、重构和深度验收。

Codex 的选择规则写在根目录 `AGENTS.md`。默认先使用 Mini；只有任务复杂度需要时才升级到完整版。完整版也采用渐进式加载，不应默认读取整个 archive。

完整版现包含按需加载的 Motion System：
- `motion-principles.md`：运动设计原则与动作结构；
- `motion-profiles.md`：UI / Mechanical / Typography / Data / Camera / Soft Graphic 的差异化运动逻辑；
- `motion-control-architecture.md`：Master Motion Channels → Precomp + Time Remap → Layer Local Motion；
- `quality/animation-qa.md`：复杂动画与关键帧可编辑性验收。

普通局部关键帧修改仍优先 Mini，不因 Motion System 的存在增加默认上下文成本。


## XPIN AE v2 workflow layer

在现有 Mini / Full + Motion System 之上，完整版新增一层“设计 → 工程执行”的中间协议：

- `workflows/previs-first.md`：Motion-sensitive 镜头先灰盒预演，避免直接堆材质后才发现构图和节奏像 PPT。
- `engineering/ae-build-spec.md`：复杂镜头的结构化施工合同，把设计意图转成 Codex 可稳定执行的 Build Spec。
- `adapters/engine-room-mcp.md`：针对当前主要使用的 Engine Room MCP，规范 bounded read、stable id、snapshot/diff、partial write safety 与 contact sheet。
- `quality/visual-feedback-loop.md`：See → Measure → Correct，有边界地使用关键 Pose / Contact Sheet，而不是逐帧视觉循环。
- `motion/motion-primitives.md`：EASE / SPRING / FOLLOW / STAGGER / PATH_FOLLOW 等成熟动作积木。
- `patterns/index.md`：与 Learned Library 分开的“成功执行 Pattern”注册表。
- `engineering/project-context-map.md`：老工程 / 模板整理后的轻量 Fast Path，降低未来 AI 读取成本。

推荐复杂生产链：
`Reference → Creative Authority → Previs → AE BUILD SPEC → Engineering Skeleton → Primary Motion → Secondary/Material → Visual Feedback → Human Polish → Patch → Pattern/Learning Promotion`

简单 Patch 仍优先 Mini，不读取上述完整链路。
