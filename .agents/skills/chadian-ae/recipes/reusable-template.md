# Recipe｜Reusable Template

1. 找出真正会反复修改的内容。
2. 建立稳定 AI_ID / ROLE。
3. 拆分可复用 Precomp。
4. 建立素材替换槽。
5. 建立 Design Tokens。
6. 用 Marker / 控制器管理主要时序。
7. 只把常用参数暴露到控制层 / Essential Properties。
8. 测试替换文字、图片、Logo、颜色是否破坏动画。
9. 测试 Agent 能否在不依赖 Layer Index 的情况下定位并 Patch。
10. 如果导出 MOGRT，只暴露业务侧真正需要改的参数。
