# Engineering｜Project Architecture

## 目标
工程打开后，人和 AI 都能快速理解、快速改。

推荐层级：
```text
00_主合成
01_场景
02_元素
03_动画模块
80_控制
90_素材槽
95_外部素材
99_辅助
```

复杂项目按实际需求调整，不机械套模板。

## Precomp
一个视觉模块如果可以独立理解、移动、替换或复用，优先做独立预合成。

不要：
- 所有内容堆主合成；
- 为每个小点都建预合成；
- 深到用户要钻 5 层才能换一个常用素材。

## Naming
尽量中文且语义明确：
- 背景_主
- 电脑_屏幕
- 窗口_钢板
- 标签_设备编号
- 素材槽_驾驶室
- CTRL_动画

避免：
Shape Layer 1 / Null 3 / Comp 17。

## Stable ID
重要对象 Comment：
```text
AI_ID=window_steel_01
ROLE=content_window
TYPE=replaceable
```

名字给人看，ID 给 Agent / Script。

## Parent / Null
层级：
局部动画
→ 模块运动
→ 场景运动
→ 镜头运动

成组运动使用 Parent / Null，不重复复制关键帧。

## 版本
重要项目记录：
- PROJECT_VERSION
- GENERATOR / MODULE_ID
- 素材 / 插件依赖
- License
以便未来判断 Patch 还是重建。
