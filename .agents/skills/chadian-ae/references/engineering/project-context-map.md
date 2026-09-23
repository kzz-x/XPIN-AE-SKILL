# 工程｜项目上下文地图（Project Context Map）

> 用于整理后的历史 AEP、长期模板或持续迭代项目。目标是给 AI 一个极短 Fast Path，避免每次都重新扫描整个工程。

## 1｜适用

- ASSET_REFACTOR；
- 模板化交付；
- 多次重复修改的项目；
- 复杂 AEP 已有稳定控制入口；
- 用户希望降低后续 Token / 读取成本。

临时一次性镜头不强制生成。

## 2｜建议位置

项目侧可使用：
- `_ae/context.md`
- `_ae/context.json`
- 或用户现有项目规范中的等价文件。

XPIN 不强制具体目录，优先尊重项目已有结构。

## 3｜最小内容

```text
项目 / 资产｜PROJECT / ASSET
根合成｜ROOT COMP

重要 ID｜IMPORTANT IDS
- 主体｜main subject
- 标题｜title
- 镜头控制｜camera rig
- 控制层｜control layers

控制器｜CONTROLLERS
- 动画控制｜CTRL_MOTION
- 样式控制｜CTRL_STYLE
- 材质控制｜CTRL_MATERIAL

素材槽｜ASSET SLOTS
- 产品｜product
- 实拍素材｜footage
- 图标 / Logo｜icon / logo

依赖｜DEPENDENCIES
- 插件｜plugins
- 字体｜fonts
- 外部文件｜external files

快速修改｜FAST PATCH
- 改文案 → ...
- 改颜色 → ...
- 改整体时长 → ...
- 换素材 → ...

人工区域｜MANUAL ZONES
- 主体曲线｜Hero Graph
- 镜头路径｜Camera Path
- 文字精修｜Typography polish

已知风险｜KNOWN RISKS
- 脆弱表达式｜fragile expressions
- 插件缺失回退方案｜missing plugin fallback
```

## 4｜读取策略

未来打开该资产时：
1. Context Map；
2. 主要控制入口（优先中文命名；旧资产可能仍为 `00_CTRL`）；
3. 当前目标 Layer；
4. 直接依赖；
5. 只有遇到不一致 / 缺失 / 深度重构才扩大读取范围。

## 5｜真实性

Context Map 只是索引，不是工程真相。

如果与真实 AE 状态冲突：
**AE state wins**，并在任务完成后修正 Context Map。

## 6｜更新

以下变化后更新：
- Root Comp / 关键层重命名；
- 控制入口变化；
- Asset Slot 变化；
- 插件依赖变化；
- Fast Patch 路径失效；
- Manual Zone 变化。

不要把每个 Layer 和所有参数都抄进去；它必须长期保持“几百字即可定位”的轻量地图。
