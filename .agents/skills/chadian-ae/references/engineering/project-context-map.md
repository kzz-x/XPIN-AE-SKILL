# Engineering｜Project Context Map

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
PROJECT / ASSET
ROOT COMP

IMPORTANT IDS
- main subject
- title
- camera rig
- control layers

CONTROLLERS
- CTRL_MOTION
- CTRL_STYLE
- CTRL_MATERIAL

ASSET SLOTS
- product
- footage
- icon / logo

DEPENDENCIES
- plugins
- fonts
- external files

FAST PATCH
- 改文案 → ...
- 改颜色 → ...
- 改整体时长 → ...
- 换素材 → ...

MANUAL ZONES
- Hero Graph
- Camera Path
- Typography polish

KNOWN RISKS
- fragile expressions
- missing plugin fallback
```

## 4｜读取策略

未来打开该资产时：
1. Context Map；
2. `00_CTRL` / 主要控制入口；
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
