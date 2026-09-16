# Recipe｜Selected Layer Patch

1. 读取当前 active project / comp。
2. 实时读取 selectedLayers。
3. 确认用户指的是这些层。
4. 只读目标层相关 Property / Effect / Parent / Expression。
5. 根据请求做最小 Patch。
6. 重新读取修改后的目标属性。
7. 检查关键帧 / Expression / Parent 未被破坏。
8. 不渲染完整视频。

如果没有选中层，不猜；告诉用户当前没有可确认的选中目标。
