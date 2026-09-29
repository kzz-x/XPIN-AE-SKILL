# Adapter｜Engine Room After Effects MCP

> **轻量索引。不要把 Engine Room 全部知识一次性加载。** 普通 Mini Patch 通常无需读取本文件；Full 只有确实需要 Engine Room 专属行为时才进入这里。

Engine Room 是 XPIN AE Skill 默认优先推荐的 AE MCP 执行底座，但不是唯一允许底座。

## 启动硬规则

**先启动 After Effects → 确认 Engine Room / AE MCP 正常 → 再启动 Premiere Pro。**

如果 PR 已先打开且 Engine Room 出现端口 / 连接异常：
1. 先关闭 PR；
2. 让 AE / Engine Room 先恢复；
3. 再开 PR；
4. 仍异常才读 connection recovery。

不要先反复改 7778、固定端口或重装。

## Progressive Loading

根据当前真实需求，只读一个对应子文档：

- **正常高级 Engine Room 执行细节**
  → `engine-room/core.md`
  - bounded read
  - stable id
  - snapshot / diff
  - partial write
  - direct tool / batch / JSX 选择

- **连接 / 端口 / Bridge / Panel 异常**
  → `engine-room/connection-recovery.md`

- **大型 JSX / UndoGroup / Purge**
  → `engine-room/jsx-undo.md`

- **截图 / Contact Sheet / screenshot 异常**
  → `engine-room/screenshot.md`

- **timeout / queued / partial write / 其他故障**
  → `engine-room/troubleshooting.md`

## Context Budget

- 正常任务不要把以上 5 个文件全部读取；
- 一个错误先只读一个最匹配的故障文档；
- 已足够决策就停止；
- Engine Room 版本特性优先以当前 tool schema / 官方 guide 为准，不为旧行为预载大量说明。

**核心原则：XPIN decides what and why. Engine Room executes and measures.**
