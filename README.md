# XPIN AE Skill

Codex / Agent 使用的 After Effects Skill 仓库。

本仓库同时保存：

- `chadian-ae-mini`：默认轻量版，适合大多数日常 AE 修改，减少上下文占用。
- `chadian-ae`：完整模块化版，适合复杂工程、完整镜头、3D、素材/插件/MOGRT、重构和深度验收。

Codex 的选择规则写在根目录 `AGENTS.md`。默认先使用 Mini；只有任务复杂度需要时才升级到完整版。完整版也采用渐进式加载，不应默认读取整个 archive。
