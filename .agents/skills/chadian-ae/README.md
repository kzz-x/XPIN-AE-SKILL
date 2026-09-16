# 差点AE

模块化 Codex / MCP / JSX After Effects Skill。

## Runtime 原则
只加载：
`SKILL.md → Core → Capability Map → Task Router → 任务需要的模块`

不要默认读取 `references/archive/ae-standard-v1.4-full.md`。

## 推荐安装名
`差点AE`

## 典型加载
- 改选中图层：Core + Router + modify-existing + mcp-direct
- 新建普通镜头：Core + Capability + Router + new-project + architecture + animation + validation
- 复杂 3D：再加 3d-camera-models + assets + visual-quality
- MOGRT：mogrt-essential-properties + controls + architecture
