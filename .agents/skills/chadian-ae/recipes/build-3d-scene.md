# Recipe｜Build 3D Scene

1. 明确是真 3D 还是 2.5D。
2. 检查 AE 当前版本、Renderer、MCP / Script 能力。
3. 列出需要真实模型的对象。
4. 复杂对象优先外部模型；简单几何再考虑 AE 原生 3D / Parametric Mesh。
5. 建立统一比例和场景坐标。
6. Camera Rig / Target。
7. Lighting / Shadow / AO-like contact。
8. 材质与色彩统一。
9. 动画：物体 / 车辆 / Camera 分离。
10. 环绕镜头检查所有面是否成立。
11. 性能检查。
12. 输出关键角度 / 关键时间帧验收。

如果自动化接口不能可靠创建某个高级 3D 能力：
不要退化成廉价伪 3D；建立 handoff 或改用外部 3D 资产。
