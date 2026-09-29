# Engine Room｜Core

> 仅当任务确实需要 Engine Room 专属行为时读取。普通 Mini Patch 已由 Mini Skill 覆盖，不要为了“保险”额外加载本文件。

## 1｜启动前置

**先启动 After Effects，确认 Engine Room / AE MCP 可用，再启动 Premiere Pro。**

若 PR 已先启动且 Engine Room 异常：先关闭 PR，让 AE / Engine Room 恢复，再开 PR。仍异常才读取 `connection-recovery.md`。

## 2｜Bounded Read

只读做决定所需的最小真实状态：

- 先确认 Project / Active Comp / selection；
- 再锁定目标 Comp / Layer；
- `get_layer_full` 只请求当前任务需要的字段；
- Shape / keyframe / source / bounds 等只有任务需要时才加；
- 禁止为了“了解工程”无界扫描。

**Read enough to decide, not enough to archive the project.**

## 3｜稳定定位

优先级：

1. Engine Room 返回的 stable id；
2. `AI_ID / ROLE / TYPE`；
3. Comp + Layer Name；
4. 特征匹配；
5. Layer Index 仅作临时显示信息。

创建对象后保留工具返回 id，不重新按名字搜索。

## 4｜写入与验证

- LOW 风险小 Patch：最小写入后直接 property read-back；
- MEDIUM / HIGH：优先 `snapshot_comp → write → diff_comp`，再回读关键属性；
- `run_jsx / run_batch` 失败可能已经部分写入，**禁止原样盲重跑**；先看 error / diff / read-back，只补未完成部分；
- timeout 也可能仍在 AE 执行，不立即重复发送写操作。

## 5｜工具选择

- 少量属性 → 原生 MCP 工具；
- 多个同类确定性操作 → `run_batch`；
- DOM 缺口 / 参数化复杂结构 → `run_jsx`；
- 不因为 JSX 能写就绕过更直接的原生工具。

大型 JSX / BUILD 的 Undo 规则只有触发时读取 `jsx-undo.md`。

## 6｜完成条件

至少确认：

- 改对对象与属性；
- 无意外新增 / 删除 / 重命名；
- Expression / Parent / Matte / Source 未误伤；
- 写失败没有遗留未识别 partial write；
- 未经授权不完整渲染。

明显视觉变化需要截图验收时再读 `screenshot.md`。
