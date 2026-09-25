# XPIN AE Skill Routing

本仓库提供两套 After Effects Skill。任何 AE 制作、修改、调试、MCP / JSX 任务，先按下述规则选择 Skill。

## Available skills

- `chadian-ae-mini`
  - 路径：`.agents/skills/chadian-ae-mini/SKILL.md`
  - 默认优先使用。
  - 适合：单层/少量图层修改、当前选中图层、文字/颜色/尺寸/关键帧微调、简单素材替换、一般局部 Patch、普通 MCP 操作。
  - 目标：最小上下文成本，同时保持 Read Before Write、Patch First、恢复点保护、AE26 中文兼容、能力预检、默认不完整渲染等核心规则。

- `chadian-ae`
  - 路径：`.agents/skills/chadian-ae/SKILL.md`
  - 完整模块化版。
  - 在以下情况升级使用：从零创建完整镜头/工程；多模块或多合成联动；复杂动画系统；2.5D/3D/Camera/Light/3D Model；素材搜索/替换槽；插件；MOGRT/Essential Properties；大型 JSX；Hybrid MCP+JSX；结构重构；历史 AEP / 半模板 / 小元素动画标准化整理；学习 / 拆解优秀 AEP 的构图、关键帧曲线、Motion、材质与视觉规律并形成可复用知识包；质量不达标后的深度排查；全工程审计。
  - 使用完整版时仍然必须渐进式加载：先读其 `SKILL.md`，再按 Router 只读需要的 references / recipes。不要默认读取 `references/archive/ae-standard-v1.4-full.md`。
  - Motion System 位于 `references/motion/`，只有明显动画设计 / 编排 / 共享控制需求时才按 Router 加载。
  - AE 设计 / 制作方案先按需加载 `references/workflows/creative-authority.md` 判断 AI 应做到什么程度，再用 `references/engineering/ae-implementation-spec.md` 把关键帧 / Expression / Layer Style / Native Effect / Plugin / CTRL 等实现方法写进方案。
  - Motion taste / Camera / Typography 是主难点时按需进入 `workflows/previs-first.md`；复杂执行前按需生成 `engineering/ae-build-spec.md`；使用 Engine Room MCP 时按需加载 `adapters/engine-room-mcp.md`。

## Routing rules

1. 默认从 `chadian-ae-mini` 开始。
2. 如果 Mini 足以安全完成任务，不升级完整版。
3. 如果任务明显触发完整版条件，可以直接使用 `chadian-ae`，无需先把 Mini 全文再读一遍。
4. 同一聊天/会话已经读取过的 Skill 或 reference，不要无意义重复读取；任务类型变化、上下文压缩、规则冲突或用户要求复核时再重读。
5. 用户说“这个/当前/选中的图层”时，必须实时读取 AE selection，不按名称猜。
6. **Recovery Point Gate**：每个用户请求视为一次修改批次。对现有 AEP 第一次写入前，必须先确认存在可恢复点；能安全自动备份就先建立备份，不能可靠备份就明确提醒用户。高风险批量修改、结构重构、大型 JSX、删除/替换大量对象时，没有可靠恢复点不要直接做破坏性写入。不要把备份副本切成当前工作工程。
7. 默认不完整渲染成片；用户会在 AE 前台预览。需要验收时只输出少量关键帧静帧，除非用户明确授权完整渲染。
8. **不要因为任务里出现关键帧就升级 Motion System。** 单层/少量图层简单关键帧仍走 Mini。
9. 当任务出现多对象编排、对象运动逻辑差异、Camera choreography、3+ 图层共享同类运动、Master Progress / Parent Rig / Precomp retime、复杂机械运动或大量重复关键帧时，升级完整版并按 `02_task-router.md` 判断 M2–M4。
10. 复杂 Motion 的目标不是“多打关键帧”，而是让 AI 先设计运动，再建立人类易调的控制系统：**Shared motion goes upward. Unique motion stays local.**
11. 用户要求“整理老工程 / 模板 / 小元素动画 / 让人和 AI 都方便改 / 降低后续 Token”时，直接升级完整版并走 `recipes/refactor-existing-asset.md`。默认只读资产根合成及必要依赖，不进行全工程扫描。
12. **Reference First**：创建新视觉/新动画，或任务涉及准确产品、复杂机械、类生物运动、物理规律、成熟 UI/HUD/Camera 语言时，先判断是否需要图片、视频、GIF/Lottie、成熟动效或可复用资产参考。普通改字改色和已有动画小 Patch 不增加这一步；复杂任务按完整版 Router 加载 `workflows/reference-first.md`。
13. **Creative Authority Gate**：涉及“出 AE 制作方案 / 从零做镜头 / 这个镜头适不适合 AI / 让 Codex 做到什么程度 / 模板风格迁移”时，先判断 `AI_DIRECT_BUILD / HUMAN_DESIGN_AI_ENGINEER / HUMAN_MOTION_AI_ASSIST` 与 Authority 0–3。默认优先 `HUMAN_DESIGN_AI_ENGINEER + Authority 1`；Authority 3 默认关闭。详细规则按 Router 加载 `workflows/creative-authority.md`。
14. **Design Includes Implementation**：AE 制作方案不能只写画面和动作结果；应按需明确主 Motion 用关键帧还是 Expression、哪些持续行为用 wiggle / loop / spring / 距离驱动、Layer Style / Native Effect / Plugin 怎么用、控制层暴露什么、哪些必须保留人工 Graph 调整。详细规则按 Router 加载 `engineering/ae-implementation-spec.md`。
15. **来自差点后期的 Handoff**：若上游来自 `kzz-x/chadian-post-GTP-project`，把其已确认的镜头目标、构图、参考、素材、时长/画幅和锁定约束视为输入，不重新从零发散视觉方案。差点AE负责把它转成可执行 AE 方案/工程；仅在 AE 可实现性、工程安全或素材条件确有冲突时提出调整。
16. **AE Project Learning**：用户说“学习这个 AE 工程 / 学一下这个 AEP / 把这个镜头的构图、动画、材质学下来”等时，直接升级完整版并走 `recipes/learn-from-ae-project.md`。学习阶段默认只读，只分析用户指定资产根合成及必要依赖；先生成候选 Learning Pack，不得自动写入 Skill。写入 `references/learned/` 或修改核心规则前，必须先向用户展示候选规律、建议作用域与写入位置，并明确询问“哪些要加入、怎么加入、并存/合并/覆盖哪一种”。只有用户明确确认后才能晋升；后续 learned 内容继续按 Index → 相关卡片渐进读取。


17. **Previs Before Polish**：Hero / 品牌 Motion / Camera / 高级 Typography 等 Motion-sensitive 镜头，如果没有成熟模板或已锁定动画，优先先做灰盒 Previs，确认构图、Pose、节奏、Camera、Hold 后再工程化和上材质。
18. **Build Contract**：复杂完整镜头、多模块、M2–M4、或“给 Codex 完整执行方案”时，设计与执行之间按需建立 `AE BUILD SPEC`；明确 LOCKED、STRUCTURE、RELATIONSHIPS、MOTION PHASES、PRIMARY/SECONDARY、CONTROLS、HUMAN POLISH、VERIFY、DO NOT。
19. **Engine Room Adapter**：当前执行底座是 Engine Room 时，XPIN 负责设计 / 路由，Engine Room 负责真实状态与执行；优先 bounded read、stable id、snapshot→diff、property read-back。写失败可能已经部分落地，禁止原样盲目重跑。
20. **Visual Feedback Loop**：明显视觉变化使用少量代表性关键 Pose / Contact Sheet 做 See→Measure→Correct；截图只定位视觉症状，真实 AE 数据用于定位原因。连续 Motion 的最终手感仍需 AE 前台人工预览。
21. **Learned ≠ Pattern**：`references/learned/` 保存已批准审美与规律；`references/patterns/` 保存已验证 Rig / Expression / JSX / Layout / Build Pattern。成功执行不自动晋升，长期写入仍需用户批准。
22. **Chinese-First Human Interface（中文优先的人机界面）**：凡最终由用户在 AE 工程、控制面板、Marker、注释、Undo、执行方案或验收结果中直接阅读的内容，默认中文优先。新建的合成 / 文件夹 / 图层 / 预合成 / Null / Camera / Light / 控制器 / 自定义 Effect 名 / Placeholder / Marker / Essential Graphics 名称等，优先使用清晰中文语义；技术英文必须保留时写成“中文｜English”或“English（中文说明）”，不得留下无解释的英文堆叠。底层 `matchName`、API、Expression / JSX 标识符、插件固定参数、文件扩展名等机器接口保持原值，避免为了汉化破坏兼容性。已有工程做普通 Patch 时不因本规则擅自批量重命名；新建对象和明确的整理 / 重构任务按中文优先执行。
23. **Undo Finalization Gate（撤销安全封存）**：小型 PATCH 保留 AE Undo，禁止自动清空；大型 BUILD / NEW_PROJECT / ASSET_REFACTOR / 结构重构完成后，只有在“修改前恢复点已验证 + BUILD 验证通过 + 当前正式 AEP 已成功保存 + 恢复点再次确认存在”的前提下，才允许执行 `app.purge(PurgeTarget.UNDO_CACHES)`，把当前结果设为新的人工工作起点。任一条件失败、执行中报错、任务仍属试验 / Previs、或用户明确要求保留 Undo 时，禁止 Purge。Purge 前后不得切换到备份副本工作。
24. **Undo 分组不等于多轮执行**：大型 BUILD 默认优先单次或少量 JSX / MCP 执行，在脚本内部按约 3–6 个逻辑阶段使用独立 UndoGroup；禁止最外层再套一个覆盖整个 BUILD 的总 UndoGroup。不要为了 Undo 分组额外增加 MCP 往返、反复读取工程或拆成大量 Agent 回合；PATCH 则一次用户请求对应一个小型原子 UndoGroup。
25. **Visual Anchor Gate（先定图，再动工）**：从零设计、中高视觉复杂度、2.5D/3D、材质/光影/构图依赖明显、口播转视觉、或用户已反馈 AI 自由设计容易跑偏时，默认先生成/选择并确认一个明确的 Visual Anchor（参考图、关键帧设计图、当前工程截图、用户 Blockout 或已批准静帧），再进入 Codex / MCP / JSX 正式 BUILD。**图不满意，先不做。** 已有足够明确的 Anchor 不重复生图。Anchor 负责锁定“长什么样”；执行提示词只补 Motion / Timing / Audio / Relationship / Constraints / Verify，禁止把图片再机械翻译成长篇文字。详细规则按 Router 加载 `references/workflows/visual-anchor.md`。
26. **AI-Friendly Motion Default（简单运动优先）**：AI 从零参与 AE / 3D 镜头时，默认先把预算花在场景、模型、材质、灯光、构图和可编辑工程结构上；除非叙事确实需要，不主动设计复杂 Camera choreography、多段连续变形、长路径追拍或高难度动作衔接。优先采用固定机位或单一轻推/轻移/轻绕 + 少量持续运动（模型缓慢旋转、灯光闪烁、局部呼吸、简单机械运动、材质/UI状态变化）。若复杂运动确实必要，方案阶段必须标注 `AI易失败/建议人工接管`，优先让 AI 先完成 LookDev / Scene Build / Rig / 基础关键帧，再由用户手调主 Graph、Camera Path 或复杂动作。

27. **Templateization / Style Pack Gate（模板化 / 视觉系统包门禁）**：用户表达“做成模板 / 整理成模板 / 模板化 / 沉淀到模板库 / 拆成独立组件 / 做成视觉系统包 / 人和 AI 共用模板”等意图时，直接升级完整版并加载 `recipes/templateize-style-pack.md`，再按其要求读取 `references/engineering/human-ai-template-library.md`。模板化任务必须先执行 **Source Isolation Gate**：源 AEP 与源素材默认只读，先建立恢复点并复制出独立模板化工作副本，所有重命名、整理、删层、拆组件、Relink / Collect 只作用于副本；禁止覆盖、移动、删除用户本地原件。正式发布模板只读，生产使用必须复制到工作区。模板型 AEP 的控制层属于各自合成内部，主要可编辑合成时间线最上方放控制 Null，不用项目面板独立 CTRL 文件夹替代。 模板化任务允许自动生成**轻量预览视频**：只渲染代表性短片段，默认低分辨率 / 低成本编码，已有预览优先复用；预览不得演变成完整长片渲染或成为流程主要耗时。
