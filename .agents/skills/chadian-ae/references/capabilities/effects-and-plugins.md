# Capability｜Effects & Plugins

## Native First, Not Native Only
原生 Effects 足够高质量时优先原生。
可以主动组合：
- Blur / Sharpen
- Glow
- Distort / Displacement
- Noise / Grain
- Color Correction
- Keying
- Transition
- Channel / Matte
- Generate
- Stylize
- Perspective
- Time
- Utility

## Plugin Gate
只有当插件：
- 明显提高质量；
- 明显提高效率；
- 原生很难达到；
时考虑。

使用前检查：
1. 是否真的需要？
2. 原生是否足够？
3. 是否安装？
4. MCP / JSX 是否能稳定访问？
5. 关键参数能否控制？
6. 缺失时 fallback？
7. 性能 / 移交成本？

## Plugin Exists
- 只暴露关键参数
- 记录依赖
- 不把数百参数全放控制层

## Missing
原生可替代 → 降级并说明。
无合理替代 → 保留入口 / 占位并告诉用户。
不要擅自安装系统级插件。

## Manual Boundary
以下可能需要人工：
- Roto Brush
- 某些 Tracking / Camera Solve
- Content-Aware Fill
- 精细逐帧 Mask
- 插件自定义面板内部功能
- 复杂 3D Rig / Simulation

不能稳定自动化时：
先把前后结构搭好，再明确标记唯一需要人工完成的步骤。
