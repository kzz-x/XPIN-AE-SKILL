# Workflow｜MCP Direct AE Control

## Session Start
先确认真实连接目标。
尤其同时运行多个 AE 时：
- 读取当前 Project 文件名；
- 读取当前 Active Comp；
- 写操作前确认这是用户要求控制的实例；
- 若使用固定 MCP Port，不自行切到其他 AE 实例。

## Read
优先使用原生读取工具。
不要先用 JSX 扫描全工程。

读取范围应与任务匹配：
- 当前选择
- 当前 Comp
- 明确目标 Layer
- 必要的父级 / 控制器 / Source

## Write
- 优先 MCP 原生工具。
- 批量 / 重复 / 确定性操作可交给 JSX / batch。
- 不依赖 UI 焦点能避免就避免。
- 不主动切换用户正在看的合成，除非操作确实要求。
- 不把大量独立写操作无意义地拆成几十次。

## Selected Layers
用户说：
“这个、选中的、当前这些层”
→ 必须读取当前 selectedLayers。
不能通过图层名字猜。

## Busy / Long Operation
AE 脚本执行可能占主线程。
长批处理：
- 尽量一次 batch；
- 不在未确认结束时重复发送同一写操作；
- 不因为 AE 暂时无响应就重建 Bridge / 工程。

## After Write
重新读取目标状态验证。
不能把“工具返回成功”当作视觉和工程都正确。

## Output
默认不完整渲染。
需要视觉检查时只输出少量代表性帧。
