# XPIN-AE-SKILL — Temporary Low Token Mode

当前 `main` 已切换为 **Mini-only**，用于排查 AE Agent 异常 token 消耗。

- 默认分支不再暴露完整 `chadian-ae` Skill。
- `.agents/skills/` 只保留极轻量 `chadian-ae-mini`。
- 根 `AGENTS.md` 明确禁止自动读取 Full、references、recipes 或递归扫描仓库。
- 普通 AE 任务甚至不必读取 Mini，只有需要直接约束 AE 操作时再用。

完整版本已原样保存在分支：

`archive/full-skill-2026-10-03`

在 token 消耗原因彻底确认前，不要把完整版重新放回 `.agents/skills/`。
