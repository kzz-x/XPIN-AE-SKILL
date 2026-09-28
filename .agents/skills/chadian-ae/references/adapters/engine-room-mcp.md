# Adapter｜Engine Room After Effects MCP

> Engine Room 是 XPIN AE Skill **默认优先推荐的 AE MCP 执行底座**。仅在当前实际使用 Engine Room 的 `after-effects-mcp` 时加载本 Adapter；已有其他稳定可用 MCP 时无需强制迁移。XPIN 负责设计、路由与工程决策；Engine Room 负责真实 AE 状态读取、写入与验证。

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

### ⚠️ Undo 执行缺口：`run_jsx` 默认外层组会吞掉内部阶段

Engine Room 当前的 `run_jsx` 默认会在调用外层建立一个 UndoGroup。AE 的嵌套 UndoGroup 会并入外层，因此如果直接在默认 `run_jsx` 里再写 3–6 个 `app.beginUndoGroup()`，用户最终仍可能只得到一个「整场 BUILD」撤销步。

### 大型 BUILD：首选 `undoGroup:false`

当目标就是让一个 `run_jsx` 内部产生 3–6 个真正独立的撤销阶段时，**首选在工具调用层关闭默认外层组**：

```text
run_jsx({
  ...,
  undoGroup: false
})
```

然后由 JSX 自己管理阶段：

```js
app.beginUndoGroup("阶段一｜基础结构");
try { /* ... */ } finally { app.endUndoGroup(); }

app.beginUndoGroup("阶段二｜视觉元素");
try { /* ... */ } finally { app.endUndoGroup(); }

app.beginUndoGroup("阶段三｜动画系统");
try { /* ... */ } finally { app.endUndoGroup(); }
```

这样才与核心规则「BUILD 内约 3–6 个独立 UndoGroup、且不增加无意义 MCP 往返」一致。

### `withoutUndoGroup(fn)`：只做局部 escape hatch

`withoutUndoGroup(fn)` 的职责不同：当本次 `run_jsx` **仍保留默认外层 UndoGroup**，但某一个操作必须临时脱离该组时使用。典型例子是当前 Engine Room 已知的 `copyToComp` 限制。

不要把「整个 BUILD 自己管理 3–6 个 UndoGroup」默认实现成：

```js
return withoutUndoGroup(function () {
  // 整场 BUILD
});
```

因为它会关闭当前外层组，并在结束后重新打开 Engine Room 的 continuation group；这不是大型 BUILD 自主管理完整 Undo 架构的最清晰入口。

要点：
- 大型 BUILD 需要内部独立撤销阶段 → **`undoGroup:false` + JSX 内部 3–6 个 UndoGroup**；
- 小 PATCH → 保持 `run_jsx` 默认单一 UndoGroup 即可；
- 单个特殊操作必须暂时离开默认 UndoGroup → `withoutUndoGroup(fn)`；
- 每个自建 UndoGroup 必须在**同一次 `run_jsx` / evalScript 调用内**打开并关闭，不能跨调用；
- 阶段名按中文优先规则命名；
- 顺序仍为「Backup → Build → Verify → Save → 再确认备份 → Purge」，Purge 不放入任何阶段组；
- Engine Room 版本变化时，以当前工具 schema / 官方 guide 为准；若 `undoGroup:false` 不存在，不要猜替代行为。

**Undo 架构验证不作为生产 BUILD 的破坏性必做步骤。** 需要验证该机制时，只在测试工程 / 新版本升级检查中人工确认一次 AE Edit 菜单和 Ctrl+Z 行为；生产工程不要为了“自检”主动撤销再重做。


### Engine Room 特有脚本边界

这些属于**当前 Engine Room 执行器行为**，不写进通用 AE26 Gotchas；版本升级后优先以 Engine Room 自身 Skill / Guide / tool schema 为准。

- `app.executeCommand()` 依赖宿主焦点 / 当前选择，在 Bridge / MCP 环境可能静默无效；优先使用明确的 DOM / Engine Room API 等价操作，例如 `CompItem.duplicate()`、`layer.duplicate()`、原生 reorder 工具等。
- 不要在 Raw `run_jsx` 里直接把 `comp.saveFrameToPng(...)` 当常规截图方案：它存在异步写盘 / 对话框等宿主边界。优先使用 Engine Room 的 `screenshot_frame / screenshot_layer`，由执行器负责等待 PNG 完整落盘与返回图像。
- `run_jsx` 失败不会自动回滚已经落地的前半段；仍按 §6 的 Partial Write Safety 先看 diff / read-back，再决定 Patch。

### ⚠️ 端口陷阱：旧 op port 被非 AE MCP HTTP 服务占用时，自动重发现可能失效

**现象**

`check_setup` 能看到真正的 AE MCP Panel 已经健康运行在端口 Y，但实际 tool call 仍发往旧端口 X；X 上恰好有另一个本地 HTTP 服务，因此调用返回 HTTP 404、非预期 JSON 或其他普通错误，而不是 connection refused。

典型诊断形态：

```text
bridgeReachable = Panel 正在端口 Y 正常响应
portAgreement   = tool calls 仍指向端口 X
```

### 成因

Engine Room 启动时会根据：

```text
AE_MCP_PORT（若设置）
→ port file
→ 默认端口 7777
```

确定初始 op port。

当前版本**不是“一次确定后永不重新发现”**：
- 当 tool call 遇到 connection refused / unreachable 时，服务端会重新探测候选端口；
- 如果找到另一个真正回答 AE MCP health 的端口，会自动切换并安全重发一次；
- timeout 不会自动重发，因为请求可能已经进入 AE。

真正的盲区是：

> **旧 op port 虽然不是 AE MCP Panel，但上面有其他 HTTP 服务正常响应。**

这时请求并没有发生 connection refused，因此可能被包装成普通 `AeError` / 非 JSON / HTTP 错误，而**没有进入现有的 port rediscovery 路径**。于是 `check_setup` 能看到 Panel 在 Y，但实际 op 仍持续发往 X。

### 关于 Premiere / 其他 CEP

如果当前机器上，先启动 Premiere 会同时启动某个占用目标端口的 CEP / 本地 HTTP 服务，那么“先开 PR → 再开 AE”**可能稳定触发**这个问题。

但：
- **Premiere 本身不是根因**；
- 也不要把某个具体插件（例如 Motion Bro）写成通用结论；
- 真正根因是：**当前缓存的 op port 被“不是 AE MCP、但会正常响应 HTTP”的程序占用，而 AE MCP Panel 已在另一个端口。**

具体占用者以当前机器实测为准。

### 修复顺序

#### A｜先诊断

1. 运行 `check_setup`；
2. 同时看 `bridgeReachable` 和 `portAgreement`；
3. 如果 Panel 在 Y，而 tool calls 仍指向 X，先按“port disagreement”处理，不要直接重装 / 重启 AE。

#### B｜优先恢复

1. 重新连接 / 重启 **MCP Server**，让它重新读取当前 port file；
2. 再运行一次 `check_setup`；
3. 只有 `portAgreement` 一致后，才继续 AE 写操作。

Panel 已经健康运行时，**不要优先重启 After Effects**；这会打断用户工作，而且通常不是根因。

#### C｜长期重复冲突时，再考虑固定专用端口

只有已经确认某个端口长期空闲，并且确实希望固定 Engine Room 时，才同时配置：

服务端：

```text
AE_MCP_PORT=<确认空闲的端口>
```

Panel：

```json
{
  "port": 7799,
  "allowPortWalk": false
}
```

Windows 的 Panel 配置文件：

```text
%USERPROFILE%\.engineroom-ae-mcp\config.json
```

Unix 风格路径：

```text
~/.engineroom-ae-mcp/config.json
```

**重要区别：**
- `AE_MCP_PORT` 是服务端的**硬 pin**。一旦设置，服务端不会自动寻找其他端口；
- Panel 的 `config.json.port` 是**起始绑定端口**，不是绝对硬锁；
- `allowPortWalk:false` 主要控制“目标端口被另一个 AE MCP Panel 占用”时是否绕过去；如果目标端口被**非 AE MCP 服务**占用，Panel 仍可能向后寻找空闲端口。

因此固定端口前必须确认该端口真的空闲。否则可能出现：

```text
Panel：7799 被其他程序占用 → 自动走到 7800
Server：AE_MCP_PORT=7799 → 永远只打 7799
```

这会把本来可恢复的端口漂移变成真正的硬失配。

### 判断表

| 情况 | 优先处理 |
|---|---|
| op port X connection refused，Panel 在 Y | 重试一次；Engine Room 通常可自动 rediscover |
| op port X 有其他 HTTP 服务响应，Panel 在 Y | 重新连接 MCP Server，再检查 `portAgreement` |
| `AE_MCP_PORT=X`，但 Panel 在 Y | 修改或取消 pin，再重新连接 MCP Server |
| Panel 本身没有运行 / 没有任何端口响应 | 进入 Panel / AE setup 诊断 |
| 同一端口冲突长期重复 | 确认空闲端口后，再考虑 Panel + Server 同时固定 |

### 巡检原则

驱动 AE 前如果怀疑连接异常：
- 不要只看 `bridgeReachable`；
- 必须同时看 `portAgreement`；
- `bridgeReachable` 只说明“某个 Panel 活着”；
- `portAgreement` 才说明“实际 tool calls 是否会发到它那里”。

**核心记忆：**

> Connection refused 可以触发 Engine Room 自动找新端口；错误端口如果被其他 HTTP 服务正常占用，反而可能绕过 rediscovery。遇到 `bridgeReachable` 与 `portAgreement` 不一致时，优先重新连接 MCP Server；长期冲突才考虑 pin，而且 pin 前必须确认端口真的空闲。

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

## 10｜Engine Room 故障边界

- timeout：可能仍在 AE 执行，不立即重发。
- connection refused：先走 Engine Room setup / discovery，不用重建工程。
- queued behind：确认前一个长任务结束后再发。
- screenshot stale / corrupt / timeout：按 Engine Room 自身返回类型处理，不通过关闭图层“修”画面。

具体版本行为以 Engine Room 当前 Skill / Guide 为准；XPIN 不复制易过期的工具实现细节。

## 11｜完成条件

Engine Room 执行结束至少确认：
- diff 与预期修改范围一致；
- 目标 property read-back 正确；
- 无意外新增 / 删除 / 重命名；
- Expression / Parent / Matte / Source 未误伤；
- 视觉变化明显时完成一次代表性 Contact Sheet；
- 未经授权不渲染完整视频。
