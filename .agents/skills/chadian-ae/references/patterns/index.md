# AE Pattern Library｜Index

这是“成功执行模式”的长期库，和 `references/learned/` 的审美学习库分开。

- Learned Library：构图 / Motion Grammar / Material / Visual taste。
- Pattern Library：已经验证好用的 Rig / Expression / JSX / Layout / Build Recipe。

## 生命周期

`candidate → approved/saved → pinned → deprecated/archive`

AI 成功执行一次，不等于自动写入核心 Skill。

## 适合晋升的 Pattern

例如：
- Destination-driven curved path；
- 响应式文字背景；
- 动态 Connector；
- 安全 Precompose；
- Marker-driven IN/HOLD/OUT；
- 多卡片 grouped stagger；
- 素材替换并保留 Crop / Matte / Motion；
- Camera target rig；
- 批量标准化命名；
- 某个稳定、可参数化的 JSX BUILD 模块。

## 推荐目录

- `patterns/rigs/`
- `patterns/expressions/`
- `patterns/jsx/`
- `patterns/layout/`
- `patterns/animation/`
- `patterns/build/`

## 每张 Pattern Card 至少记录

```text
ID
TYPE
TRIGGER
USE
AVOID
INPUTS
OUTPUT
DEPENDENCIES
IMPLEMENTATION
EDITABLE CONTROLS
VERIFICATION
SOURCE / PROVENANCE
STATUS
```

如果包含可执行 JSX，应优先记录稳定参数接口，而不是复制一个硬编码到具体 Comp 名称的脚本。

## Promotion Gate

当一个新执行方案明显值得长期复用时：
1. 先报告“发现可复用 Pattern”；
2. 给出 Trigger / 参数 / 依赖 / 风险；
3. 询问用户是否晋升；
4. 用户批准后才写入本库。

若 Engine Room / 其他 MCP 自己有 Tool Library，可把实际可执行 artifact 留在工具层；XPIN Pattern Card 记录：
- 什么时候调用；
- 需要什么参数；
- 与 Motion / Engineering 的关系；
- 失败时怎么退回。

这样避免把大段 JSX 塞进 Skill。

## Registry

当前暂无已批准 Pattern。

后续登记示例：

```md
- id: destination_path_v1
  type: rig
  scope: generic
  tags: [path, target, relationship]
  path: references/patterns/rigs/destination-path-v1.md
  trigger: A 从当前位置移动到未来可能继续调整的 Target B
  summary: Start/Control/End + Progress，终点动态引用 Target，不烘焙坐标。
```
