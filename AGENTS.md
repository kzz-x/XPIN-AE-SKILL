# XPIN AE Skill Routing

本仓库提供两套 After Effects Skill。目标：**能力不缩水，但默认上下文尽可能小。**

## 1｜先选 Skill

默认使用 `chadian-ae-mini`：
- 选中层 / 单层 / 少量图层；
- 改字、颜色、尺寸、位置、素材；
- 简单 M0–M1 关键帧；
- 普通局部 Patch。

直接使用 `chadian-ae`：
- 从零完整镜头 / 工程；
- 中高视觉复杂度、Visual Anchor、Reference / Asset Search；
- M2–M4 多对象 Motion / Camera / 3D；
- 完整新建多图层动画，尤其要求少关键帧、Parent 层级、非破坏 Offset、后续人工易改；
- 建立 / 整理系统级 CTRL 控制面、参数化、Preset、差点控件；
- 大型 JSX / Hybrid / 结构重构；
- 口播自动分析、MOGRT、插件；
- 模板化 / Style Pack；
- 学习 / 拆解 AEP；
- 全工程审计或深度 Debug。

**明显命中 Full 时不要先读 Mini。**

## 2｜直接操作 AE 前

只咨询、出方案、解释时不要求 MCP。

要直接读取 / 修改 / 制作 AE：
- 已有可用 AE MCP → 使用真实状态执行；
- 没有可用 MCP → 默认推荐 Engine Room After Effects MCP；
- 未连接时先说明需要连接，不得假装已操作工程；
- Engine Room 不是唯一允许底座，已有其他稳定工具不强制迁移。

### Engine Room + Premiere
使用 Engine Room 时：**先启动 AE，确认 MCP 正常，再启动 PR。**

若 PR 已先开且出现端口 / 连接异常：**第一动作关闭 PR，让 AE / Engine Room 先恢复，再开 PR。** 仍异常才进入 Engine Room connection recovery，不先反复改端口或重装。

## 3｜永远生效的底线

- **Read Before Write**：只读当前任务需要的真实状态。
- **Patch First**：能局部改，不重建。
- **Preserve Manual Work**：保护人工关键帧、Graph、Expression、Parent、Matte、Mask、Effects、素材与结构。
- **Recovery Point**：现有 AEP 本轮第一次写入前先确认可恢复点；高风险修改没有可靠恢复点不做破坏性写入。
- 用户说“当前 / 这个 / 选中的” → 实时读取 selection，不猜。
- 用户可见的新建对象默认中文优先；机器接口保持原值。
- 默认不完整渲染；需要验收只取少量代表性帧，除非用户明确授权成片。
- 写后必须回读关键状态；工具返回成功 ≠ 工程正确。
- **Visual Anchor 模型路由**：平面设计、3D/2.5D、软件/UI、电脑生成视觉等参考图若用 GPT 容易偏写实，主动提醒并优先建议改用 **Banana** 锁定参考图/关键帧；图没锁定前不要急着进入正式 BUILD。

## 4｜Context Budget｜渐进加载硬规则

**不要预读 references。**

- Mini 普通 Patch：除 Mini `SKILL.md` 外，默认读取 **0 个 reference**。
- Full：先只读 Full `SKILL.md`；执行前默认新增 **0–2 个直接命中的 reference / recipe**。
- 已经足够执行就停止读取。
- 同一会话已经读过的文件不重复读取，除非内容确实丢失 / 变化。
- 故障文档只在真实故障出现后加载。
- `00_core-invariants.md`、`01_capability-map.md`、`02_task-router.md` **都不是 Full 启动必读项**。
- 禁止为了“全面理解规范”扫描整个 `references/`、`recipes/` 或 archive。
- 只有复合高风险任务确实同时命中多个独立模块时，才可超过 2 个；仍应逐个加载，而不是一次性全读。

**默认路径：AGENTS → Mini → MCP。复杂任务才：AGENTS → Full → 命中的 1–2 个模块 → MCP。**
