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
13. **来自差点后期的 Handoff**：若上游来自 `kzz-x/chadian-post-GTP-project`，把其已确认的镜头目标、构图、参考、素材、时长/画幅和锁定约束视为输入，不重新从零发散视觉方案。差点AE负责把它转成可执行 AE 方案/工程；仅在 AE 可实现性、工程安全或素材条件确有冲突时提出调整。
14. **AE Project Learning**：用户说“学习这个 AE 工程 / 学一下这个 AEP / 把这个镜头的构图、动画、材质学下来”等时，直接升级完整版并走 `recipes/learn-from-ae-project.md`。学习阶段默认只读，只分析用户指定资产根合成及必要依赖；先生成候选 Learning Pack，不得自动写入 Skill。写入 `references/learned/` 或修改核心规则前，必须先向用户展示候选规律、建议作用域与写入位置，并明确询问“哪些要加入、怎么加入、并存/合并/覆盖哪一种”。只有用户明确确认后才能晋升；后续 learned 内容继续按 Index → 相关卡片渐进读取。
