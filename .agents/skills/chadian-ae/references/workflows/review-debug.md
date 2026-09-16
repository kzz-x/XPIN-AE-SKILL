# Workflow｜Review / Debug

## 先分类
- 连接 / MCP
- Layer targeting
- Expression
- Keyframe / timing
- Missing asset
- Plugin
- JSX compatibility
- Visual
- Performance
- Render

## 调试顺序
1. 读取真实错误状态。
2. 先找最小复现对象。
3. 不要一上来重建工程。
4. 修复最小问题。
5. 再验证上下游。
6. 必要时输出关键帧。

## 常见检查
- Expression Error
- Missing Footage
- Missing Font
- Missing Plugin
- Duplicate Layer / Comp
- Wrong Parent
- Wrong Track Matte
- Keyframe 被覆盖
- Marker 错位
- 3D / Camera 层级错误
- Source 尺寸 / 素材槽错误

视觉不好时不要只“加 Glow / Blur / Shadow”。
先判断：
构图、层级、素材质量、空间关系、运动逻辑、字体、线条、颜色是否根本有问题。
