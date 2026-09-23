# Adapter｜Engine Room After Effects MCP

> 仅在当前执行底座是 Engine Room 的 `after-effects-mcp` 时加载。XPIN 负责设计、路由与工程决策；Engine Room 负责真实 AE 状态读取、写入与验证。

核心原则：

**XPIN decides what and why. Engine Room executes and measures.**

## 1｜启动顺序

复杂 BUILD / NEW_PROJECT / ASSET_REFACTOR 默认按需：

1. `get_house_style`：读取当前项目局部视觉规范；没有就继续，不阻塞。
2. `get_project_summary`：确认当前工程。
3. `list_comps({include: []})`：只取 comp id / name。
4. 锁定目标 Comp 后 `list_layers({compId, include: []})`。
5. 只对目标对象使用 bounded `get_layer_full({include:[...]})`。

禁止为了“了解工程”做无界读取。

## 2｜Style Priority

发生风格冲突时按以下优先级：

1. 用户本轮明确要求；
2. 本轮已锁定 Reference / Handoff / Previs；
3. 当前 AEP 的真实实现；
4. 项目旁 `house-style.md`；
5. XPIN `references/learned/` 已批准规则；
6. Generic Skill Default。

`house-style` 是当前项目局部记忆，不替代 XPIN Learned Library；Learned Library 也不得覆盖当前项目已经成立的事实。

## 3｜Bounded Read

优先一次有边界的完整读取，不做很多无意义碎读：

- 只要 transform + effects → `include:["transform","effects"]`
- 需要文字实寸 → 加 `text / bounds`
- 需要素材关系 → 加 `source / parent`
- Shape 仅在必要时读取，并限制 depth / detail
- Keyframe 只读取承担当前 Motion 的属性，并限制数量

**Read enough to decide, not enough to archive the whole project.**

## 4｜稳定定位

优先：
1. Engine Room comp/layer stable id；
2. XPIN 的 `AI_ID` / ROLE / TYPE；
3. Comp + Layer Name；
4. 特征匹配；
5. Layer Index 只作临时显示信息，不作为长期定位。

创建对象后立即保留工具返回的 id，不重新按名字搜索。

## 5｜写前基线

中等以上修改优先：

`snapshot_comp({compId}) → write → diff_comp({since})`

若使用 `run_jsx` / `run_batch` 且支持内部 diff，优先 `diff:true`。

Diff 用于确认结构变化；具体数值正确性仍需 property read-back。

## 6｜Partial Write Safety

Engine Room 写失败不代表“什么都没发生”。

若 `run_jsx` / `run_batch` 中途失败：
1. 禁止原样立即重跑；
2. 先读 error 自带 diff 或调用 `diff_comp`；
3. 确认哪些操作已落地；
4. 只 Patch 未完成部分；
5. 若工程状态不可信，退回恢复点。

**Failure may be a partial write. Verify before retry.**

## 7｜Direct Tool / Batch / JSX

优先级：
- 单属性 / 少量稳定操作 → Engine Room 原生工具；
- 多个同类确定性操作 → `run_batch`；
- DOM 缺口 / 参数化复杂结构 → `run_jsx`；
- 不因为 JSX 能写就绕过已有原生工具。

大型 BUILD 不使用“一个不可验证的巨大事务”覆盖全部施工，但也不要为了 Undo 分组机械拆成大量 MCP 往返。默认优先单次或少量执行调用：先 bounded read 确认状态，再让 JSX 在内部按约 3–6 个逻辑 UndoGroup 完成 BUILD，最后统一做必要的 diff / property read-back / visual check。只有确实需要中间验证或存在高风险边界时，才拆成多个 MCP 阶段。

### ⚠️ 执行缺口：`run_jsx` 自带 Undo 组 → 必须用 `withoutUndoGroup`

**这是最容易踩、而且后果最重的一条。**

`run_jsx` 在自己的外层**已经建立了一个 Undo 组**。所以直接在脚本里写多个
`app.beginUndoGroup("阶段N")` **不会**产生多个独立撤销步 —— 最外层那个组会把它们全部吞掉。
结果是「**一次 Ctrl+Z 撤掉整场 BUILD**」，而核心规则里要求的
「BUILD 内部 3–6 个可独立撤销阶段」实际上没有生效。

要真正实现分段撤销，必须显式取消 run_jsx 自带的外层组：

```js
return withoutUndoGroup(function () {
  app.beginUndoGroup("阶段一 建节点");
  try { /* ... */ } finally { app.endUndoGroup(); }

  app.beginUndoGroup("阶段二 建连接线");
  try { /* ... */ } finally { app.endUndoGroup(); }

  app.beginUndoGroup("阶段三 打运动关键帧");
  try { /* ... */ } finally { app.endUndoGroup(); }

  return "完成";
});
```

要点：
- `withoutUndoGroup(fn)` 只取消 **run_jsx 自带的那一层**，不影响内部 `beginUndoGroup`；
- 阶段名用中文，用户在 Undo 菜单里能直接读懂（对应核心规则「中文优先的人机界面」）；
- 顺序保持「**Backup → Build → Verify → Save → 再确认备份 → Purge**」，
  Purge 与封存**不能**放进任何中间阶段组（见 §8）；
- 小 PATCH（一次请求只改目标属性）**保持默认即可**，不必套 `withoutUndoGroup`；
- 若脚本中途抛错，已闭合的阶段仍留在 Undo 历史里 —— 便于只回退出错的那一段。

**写后自检（必做）**：BUILD 完成后在 AE 里按一次 `Ctrl+Z`，
确认撤销的是**最后一个阶段**，而不是整场。若一次撤掉全部，说明分组没生效。

## 8｜撤销安全封存

Engine Room 执行大型 BUILD / ASSET_REFACTOR 后：
- 先确认没有 partial write 未处理；
- 完成必要 diff / property read-back；
- 确认修改前恢复点仍存在；
- 保存当前正式 AEP 并确认成功；
- 再次确认恢复点；
- 最后才可通过受控 JSX 执行 `app.purge(PurgeTarget.UNDO_CACHES)`。

小 PATCH 不 Purge。
Engine Room 返回写入成功 ≠ 可以直接 Purge；必须通过上述验证。
若 Purge 执行结果无法可靠确认，不得向用户声称“撤销缓存已清除”。

## 9｜视觉检查

不要把 screenshot 当持续逐帧反馈。

明显视觉修改优先一次 contact sheet：
`screenshot_frame({compId, times:[t1,t2,t3,...]})`

- 通常 3–6 个关键时间点；
- 默认使用自动 downsample；
- 不连续抓几十帧；
- 静态小 Patch 不截图；
- Motion 质量仍需要人工 AE 预览，Contact Sheet 只承担关键 Pose / Staging / continuity 验证。

详细闭环见：
`../quality/visual-feedback-loop.md`

## 9｜Engine Room 故障边界

- timeout：可能仍在 AE 执行，不立即重发。
- connection refused：先走 Engine Room setup / discovery，不用重建工程。
- queued behind：确认前一个长任务结束后再发。
- screenshot stale / corrupt / timeout：按 Engine Room 自身返回类型处理，不通过关闭图层“修”画面。

具体版本行为以 Engine Room 当前 Skill / Guide 为准；XPIN 不复制易过期的工具实现细节。

## 10｜完成条件

Engine Room 执行结束至少确认：
- diff 与预期修改范围一致；
- 目标 property read-back 正确；
- 无意外新增 / 删除 / 重命名；
- Expression / Parent / Matte / Source 未误伤；
- 视觉变化明显时完成一次代表性 Contact Sheet；
- 未经授权不渲染完整视频。
