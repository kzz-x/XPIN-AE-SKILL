# Workflow｜JSX / ExtendScript

适合：
- 批量创建
- 标准化结构
- 参数化模块
- 重复执行
- 大量确定性操作

不适合把所有视觉判断都塞进脚本。

## 基础
必须有：
```javascript
app.beginUndoGroup("任务名称");
try {
    // work
} catch (err) {
    // meaningful error
} finally {
    app.endUndoGroup();
}
```

## Undo Architecture｜BUILD ≠ PATCH
JSX 执行前先判断本次属于：
- **BUILD**：首次创建较大模块 / 工程。默认使用单次或少量 JSX 执行，在脚本内部按约 3–6 个逻辑阶段建立独立 UndoGroup；不要把整个工程包成一个巨大 Undo，也不要细到每层 / 每关键帧一个 Undo。
- **PATCH**：修改现有工程。一次用户请求只建立一个小型 UndoGroup，只修改目标属性或目标子模块；禁止为了改几个参数重新运行完整 BUILD。
- 禁止在这些阶段外再套一个覆盖全部 BUILD 的总 UndoGroup。
- **拆 Undo 不等于拆工具调用**：不要为了 UndoGroup 额外增加 MCP 往返、重复状态读取或多轮 Agent 执行；能在同一 JSX 中按阶段执行，就保持同一次脚本调用。
- Undo 名称必须可读，例如“创建主体模块”“修改标题字号”“调整入场速度”。
- 每个 `beginUndoGroup()` 都必须用 `try / finally` 确保 `endUndoGroup()` 被调用。
- 大型 BUILD 完成并验证后，进入撤销安全封存检查；只有修改前恢复点已验证、正式 AEP 已成功保存、清理前再次确认恢复点存在时，才执行 `app.purge(PurgeTarget.UNDO_CACHES)`。
- 小 PATCH、试验 / Previs、执行报错、验证失败、备份或保存无法确认、用户要求保留 Undo → 禁止 Purge。

目标是让 AE Undo 历史仍然适合人类继续工作：撤销小修改时不应轻易退回整套 AI 构建。

## Undo Finalization｜撤销安全封存

大型 BUILD 的推荐尾部流程：

```text
修改前备份并验证
→ BUILD
→ 写后验证
→ 保存当前正式 AEP
→ 确认保存状态
→ 再次确认修改前备份存在
→ app.purge(PurgeTarget.UNDO_CACHES)
```

实现注意：
- Purge 只放在所有 BUILD 阶段与验证之后，不放进任何中间 UndoGroup；
- Purge 前不得切换到备份工程；
- 如果工具无法可靠验证保存 / 备份状态，就停止在“已保存 / 待人工封存”，不要猜；
- Purge 成功后，旧 Undo 不再作为恢复路径，恢复依赖明确的 AEP 恢复点；
- 后续 PATCH 会从新的 Undo 历史重新积累，因此小修改仍可正常 Ctrl+Z。

## 规则
- 优先 `matchName`。
- 不依赖中文版 / 英文版属性名。
- 不依赖固定 Layer Index。
- 不静默覆盖同名重要对象。
- 尽量重复执行不制造垃圾重复层。
- 修改现有工程只碰目标对象。
- 对字体 / 素材 / 插件缺失容错。
- 单个素材失败不能让整个工程无意义崩掉。
- JSX 不负责运行时联网找素材；先落地本地文件再导入。
- 重要创建对象写 AI_ID / ROLE / TYPE。
- 完成后打开 / 定位主合成，但不要擅自完整渲染。

## 脚本与视觉
“容易脚本化”不是视觉决策依据。
如果真实素材 / 3D / Native Effect 更合适，就用更合适的路线。
必要时 Hybrid：MCP 检查 → JSX 批量 → MCP 验证。
