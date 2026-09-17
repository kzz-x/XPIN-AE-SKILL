# Motion｜Relationship & Constraint Rigs

> 目标：先定义对象之间的关系，再决定关键帧。多个对象有关联运动时，不要第一反应就是分别打关键帧。

## 1｜Relationship Scan

动画前先判断对象之间是否存在：
- Parent / Child
- Attach / Detach
- Carry / Payload
- Follow
- Target
- Look At
- Align
- Connect
- Constraint
- Shared Motion
- Auto Layout
- Dynamic Bounds
- Camera Target / Focus Target
- Path Dependency

如果关系真实存在，优先把关系编码进工程。

**Relationships should be encoded, not manually synchronized.**

错误：
```text
爪子 Position KF KF KF
零件 Position KF KF KF
```

正确方向：
```text
爪子
└─ NULL_ATTACH

零件
└─ Parent / Attach / Constraint → NULL_ATTACH
```

移动爪子后，零件关系仍应成立。

---

## 2｜Attach / Carry Pattern

适合：爪子抓零件、手拿物品、机械臂搬运、卡车载货、托盘运输、鼠标拖拽。

推荐：
```text
CARRIER
└─ NULL_ATTACH

PAYLOAD
```

状态：
```text
抓取前：PAYLOAD 保持原始空间
抓取后：PAYLOAD → NULL_ATTACH
释放后：PAYLOAD → 新目标空间
```

可按任务选择：Parent / Expression Blend / Null Constraint / 分段 Parent / Precomp。

需要动态抓取时可暴露：
```text
CTRL_抓取
  Attach Progress
  Release Progress
```

不要分别手动同步 Carrier 与 Payload 的完整路径。

---

## 3｜Destination-Driven Motion

A 从起点移动到目标 B 时，不要默认把 B 当前坐标烘焙成 A 的终点 Keyframe。

优先：
```text
NULL_START
TARGET_B
MASTER_PROGRESS
```

A 的 Position 由：
```text
Start → TARGET_B 当前实时位置
```

以后用户移动 B，A 的终点自动更新。

适合：飞入目标、吸附、归位、零件装配、指针移动、节点连接。

---

## 4｜Path Rig

复杂路径不要只靠一串 Position Keyframe 隐式定义。

可使用：
```text
NULL_START
NULL_CONTROL_A
NULL_CONTROL_B
NULL_END
```

对象根据 Progress 沿路径运动。

- START → 起点
- END → 终点
- CONTROL → 弧度 / Line of Action

修改路径时不需要重做整套 Position Animation。

---

## 5｜Follow / Offset

标签、标注、UI 跟随主体：
```text
Follower Position = Target Position + Offset
```

Offset 独立可调。
不要复制主体 Position Keyframes。

---

## 6｜Look At / Aim

箭头、Camera、机械部件需要指向目标时，Rotation / Orientation 应由 Source → Target 的实时方向计算。

Target 改位置后，朝向自动更新。
不要手工同步目标位置和旋转关键帧。

---

## 7｜Connector

节点间连线：
```text
START = Node_A
END = Node_B
```

节点重新布局后，连线自动更新。
适合架构图、流程图、Callout、技术标注、数据关系。

---

## 8｜Dynamic Bounds / Auto Layout

文字背景、标签框、信息卡需要随内容变化时，优先：
```text
sourceRectAtTime()
+ Padding
```

文字变化后，背景尺寸与相关布局继续成立。
不要把文字尺寸和背景框尺寸维护成两套独立数据。

---

## 9｜Shared Motion

保持固定相对关系的一组对象：
→ Parent / Group Null。

相似运动但需要错帧：
→ Shared Progress + Stagger / Delay。

不要复制完整关键帧后再逐层手工错帧。

---

## 10｜Camera Relationship

Camera 优先拆分：
```text
CAMERA
CAMERA_RIG
CAMERA_TARGET
FOCUS_TARGET
```

空间运动、观察目标、Focus 尽量解耦。
主体位置调整后，不应必然要求重画整套 Camera Path。

---

## 11｜Keyframe Compression

目标不是零关键帧，而是：

**零重复关键帧。**

合理结构可以是：
```text
Master Progress     2 KF
Camera Progress     2 KF
特殊对象 Local      4 KF
```

但驱动几十个图层。

判断一个值是否应该烘焙成 Keyframe 前先问：
1. 它是否来自另一个对象？
2. 它是否属于可计算关系？
3. 它是否与其他对象共享 Progress？
4. 用户以后是否可能移动目标？
5. 用户是否需要 Graph Editor？

动态关系 → Parent / Expression / Rig。
共享时间 → Master Progress。
真正独有动作 → Local Keyframes。

---

## 12｜Graph Editor 与 Master Progress

共享动画优先保留少量真正可编辑的 Master Keyframes：
```text
CTRL_动画
  MASTER_PROGRESS  0 → 100
```

用户仍可在 Graph Editor 调两个主关键帧的速度曲线。

其他对象可派生：
```text
localProgress = masterProgress + Delay / Stagger / Profile Mapping
```

不要为了“无关键帧”把工程写成难维护的 Expression 黑盒。

---

## 13｜Rig Controls

按需要暴露：
- Master Progress
- Duration / Speed
- Delay / Stagger
- Motion Strength
- Weight
- Overshoot / Settle
- Path Bend
- Attach / Release
- Target Layer

只暴露真正会人工调整的参数。

---

## 14｜通过标准

完成后用户应能：
- 移动 Target → 动画自动适配；
- 移动 Carrier → Payload 继续保持关系；
- 修改文字 → 背景 / 布局自动适配；
- 调 Master Graph → 整体节奏改变；
- 改 Stagger → 多对象节奏统一变化；
- 移动 Camera Target → 镜头关系继续成立。

如果改一个位置就需要重新调多个本应相关的对象，Relationship Rig 不合格。
