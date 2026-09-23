# 工程｜控制层与设计参数（Controls & Design Tokens）

## 原则
“统一控制”不等于“全部一样”。

按功能拆控制：
```text
全局样式控制｜CTRL_GLOBAL
文字系统控制｜CTRL_TYPE
动画控制｜CTRL_MOTION
镜头控制｜CTRL_CAMERA
调色控制｜CTRL_COLOR
素材控制｜CTRL_ASSET
```
简单工程可合并，但逻辑仍分类。

## 设计参数（Design Tokens）
高频可改项：
- 背景 / 主文字 / 次文字 / 强调色
- 细线 / 标准线 / 强调线
- 圆角 S / M / L / Pill
- 标题 / 副标题 / 正文 / 数据 / 标签 / 注释
- 常用透明度
- 常用尺寸 / 间距

不要把所有元素强制绑定成同一个描边、字体、圆角。

## 暴露标准
经常改 → 控制层。
局部造型内部参数 → 保留局部。
不要把几百个 Effect / Plugin 参数全暴露。

## 动画控制（Motion Controls）
简单动画不需要先搭 Master Controller。

当 3+ 图层共享同类运动，或任务达到 M3–M4 时，优先让 `动画控制｜CTRL_MOTION` 暴露真正高频控制，例如：
- 总进度｜Master Progress
- 入场 / 出场进度｜In / Out Progress
- 动画强度｜Motion Strength
- 速度 / 时长倍率｜Speed / Duration Scale
- 错帧｜Stagger
- 过冲强度｜Overshoot Strength
- 收束强度｜Settle Strength

不同对象仍可按 Motion Profile 对这些通道产生不同响应，不要把所有层绑定成完全相同的曲线。

完整规则按需读取：`../motion/motion-control-architecture.md`。

## 文字层级（Typography）
至少有层级：
- T1 主标题
- T2 副标题
- T3 正文
- T4 数据
- T5 工业标签
- T6 注释

字体缺失时：
- fallback；
- 不让脚本崩；
- 留替换提示。

## 修改友好
用户应该快速知道：
改颜色 / 文字 / 素材 / 动画速度 / Stagger / 镜头 / 圆角 / 图标 → 去哪里。

如果为了调一个共享动画速度必须逐层移动十几组关键帧，应升级 Motion Control Architecture，而不是继续复制关键帧。
