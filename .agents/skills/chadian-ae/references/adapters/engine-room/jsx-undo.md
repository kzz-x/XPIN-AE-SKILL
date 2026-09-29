# Engine Room｜JSX & Undo

> 仅在大型 `run_jsx` / BUILD、复杂批量结构或 Undo 行为异常时读取。普通 PATCH 不加载。

## 工具选择

- 单属性 / 少量稳定操作 → 原生工具；
- 多个同类确定性操作 → `run_batch`；
- DOM 缺口 / 参数化复杂结构 → `run_jsx`。

大型 BUILD 默认单次或少量执行，不要仅为了 Undo 拆成大量 MCP 往返。

## run_jsx 的外层 UndoGroup

Engine Room 的 `run_jsx` 默认可能建立外层 UndoGroup。AE 嵌套 UndoGroup 会并入外层，因此在默认调用里再写多个 `app.beginUndoGroup()`，最终可能仍只有一个大撤销步。

需要一个 `run_jsx` 内产生多个真正独立阶段时，优先：

```text
run_jsx({
  ...,
  undoGroup: false
})
```

再由 JSX 管理约 3–6 个逻辑阶段：

```js
app.beginUndoGroup("阶段一｜基础结构");
try { /* ... */ } finally { app.endUndoGroup(); }

app.beginUndoGroup("阶段二｜视觉元素");
try { /* ... */ } finally { app.endUndoGroup(); }

app.beginUndoGroup("阶段三｜动画系统");
try { /* ... */ } finally { app.endUndoGroup(); }
```

规则：

- 小 PATCH → 默认单一 UndoGroup；
- 大 BUILD → `undoGroup:false` + JSX 内部 3–6 个阶段；
- 每个自建 UndoGroup 必须在同一次 evalScript / `run_jsx` 内开关；
- Undo 名称中文优先；
- 不要为了分组增加无意义 MCP 往返。

## withoutUndoGroup(fn)

它是局部 escape hatch，不是整场 BUILD 的默认入口。只有某个操作必须临时脱离默认外层 UndoGroup 时使用，例如已验证存在该限制的特定操作。

## Engine Room 脚本边界

- `app.executeCommand()` 依赖宿主焦点 / 当前选择，可能静默无效；优先明确 DOM / Engine Room API；
- `run_jsx` 失败不自动回滚；先 diff / read-back，再 Patch；
- Engine Room 版本变化时，以当前工具 schema / guide 为准；若 `undoGroup:false` 不存在，不猜替代行为。

## Purge

小 PATCH 不 Purge。

大型 BUILD / ASSET_REFACTOR 只有满足全部条件才允许清 Undo Cache：

1. 修改前恢复点存在；
2. BUILD 验证通过；
3. 当前正式 AEP 已保存成功；
4. 再次确认恢复点存在。

否则禁止 `app.purge(PurgeTarget.UNDO_CACHES)`。
