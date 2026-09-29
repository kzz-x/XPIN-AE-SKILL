# Engine Room｜Troubleshooting

> 仅在真实故障出现后读取。不要在正常任务启动阶段预读。

## Partial write

`run_jsx / run_batch` 报错不代表没有写入：

1. 不原样重跑；
2. 看 error 自带 diff，或 `diff_comp`；
3. 回读目标属性 / 结构；
4. 只补未完成部分；
5. 状态不可信时回恢复点。

## Timeout

timeout 可能意味着 AE 仍在执行。不要立即重发同一写操作，先确认前一个任务是否结束。

## Queued behind

先确认前一个长任务完成，再发下一次写入；不要因为排队就重建工程或 Bridge。

## Connection refused / 404 / 非预期 HTTP

读取 `connection-recovery.md`。尤其 Engine Room + Premiere 环境先恢复 **AE 先、PR 后**。

## JSX / Undo

大型 `run_jsx`、UndoGroup、`undoGroup:false`、`withoutUndoGroup` 问题读取 `jsx-undo.md`。

## Screenshot

stale / corrupt / screenshot timeout 读取 `screenshot.md`。

## 宿主焦点

`app.executeCommand()` 等依赖焦点 / 选择的命令可能静默失败。优先 DOM 或 Engine Room 明确 API，不用 UI 焦点绕路，除非当前工具确实只能这么做。

## 原则

故障排查也要 bounded：

**先根据错误类型只加载一个对应文档；不要因为一次失败把所有 Engine Room references 全读一遍。**
