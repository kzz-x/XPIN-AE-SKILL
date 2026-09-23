# 工程｜AE26 / ExtendScript 实战陷阱

> **仅在 Router 命中时加载**：Raw JSX、Shape Contents / `addProperty`、Parent / 坐标空间、Repeater、图层重排、KeyframeEase，或出现「Object is invalid / 保留字 / 坐标异常」等脚本故障时读取。普通 MCP Patch 不为此增加上下文。
> 格式：**现象 → 原因 → 正确写法 → 自检**。
> 本文件只保存 AE26 / ExtendScript 相对稳定的宿主行为；Engine Room 特有行为放在 `../adapters/engine-room-mcp.md`，避免执行器升级后留下过时结论。
> 与 `expressions-and-compatibility.md` 的分工：那边讲兼容性与通用写法约定，这边只讲**容易直接报错或静默写错**的实战陷阱。

---

## 1｜Indexed Group 调用 `addProperty()` 后，旧 Property 引用可能失效

**现象**
先取到 `var rg = vg.property(1)`，接着 `vg.addProperty(...)` 加了填充 / 中继器，
再回来用 `rg.property("ADBE Vector Rect Size").expression = ...` → 报 **「对象无效」**。

**原因**
AE 对 `PropertyType.INDEXED_GROUP` 执行 `addProperty()` 等结构写入时，可能重建该 indexed group；此前缓存的 Property 引用因此可能变成 `Object is invalid`。Shape Contents / Effect Parade 都属于需要警惕的场景。不要泛化成“任何结构变化都会让所有引用失效”。

**正确写法**
能确定顺序时，优先「**先设完当前引用，再做会重建 indexed group 的结构添加**」：

```js
var rg = vg.addProperty("ADBE Vector Shape - Rect");
rg.property("ADBE Vector Rect Size").setValue([5, 22]);
rg.property("ADBE Vector Rect Size").expression = "...";   // ← 先设
vg.addProperty("ADBE Vector Graphic - Fill");              // ← 后加
```

必须晚设时，在最后一次结构变更之后**重新从父组获取引用**，最好按稳定名称 / matchName / 已知路径重新 resolve，而不是长期保存旧 Property 对象。

**自检**：报「对象无效 / invalid object」时，先怀疑引用失效，而不是怀疑值不对。

---

## 2｜对象字面量不能用保留字当 key

**现象**：`{ name: L.name, in: L.inPoint }` → 报 **「非法使用保留字」**。
**原因**：ExtendScript 中 `in` / `for` / `class` / `delete` 等是保留字，不能作字面量键。
**正确写法**：`{ name: L.name, inPt: L.inPoint }`（键名加后缀或改写）。
**自检**：错误信息里出现「保留字」时，不要去看语法结构，直接扫对象字面量的 key。

---

## 3｜`KeyframeEase.influence` 下限为 0.1；Engine Room `ease()` helper 传 0 也会失败

**现象**：`ease(prop, 1, 0, 40)` → 报 **「值 0 在 0.1 至 100 的范围外」**（中文版可能是构造器报错）。
**原因**：AE 的 `KeyframeEase.influence` 合法范围是 **0.1–100**。Engine Room 的 `ease()` helper 最终也会构造 `KeyframeEase`，因此同样受这个范围约束。
**正确写法**：想要「接近线性」就用 `0.1`：

```js
ease(prop, 1, 0.1, 95);   // 出帧接近线性
ease(prop, 2, 25, 55);
```
**自检**：凡是直接构造 `KeyframeEase` 或通过 Engine Room `ease()` helper 设置 influence，都不要传 0。这里说的是脚本 KeyframeEase，不是 AE Expression 语言里的 `ease(t,...)` 插值函数。

---

## 4｜null 图层不传导 Opacity（只传导 Transform）

**现象**
把一个形状 / 文字层挂到 null 下，给 **null** 打不透明度关键帧想整体淡入淡出 →
**子级完全不跟着变**，null 自己的 op 已经是 0 了，子级还亮着。

**原因**
AE 的父子关系只传导 **Transform**（位置 / 缩放 / 旋转）；**Opacity 不继承**。
这和「挂在 null 下的所有层一起淡出」的直觉相反。

**正确写法**
父级只当「可见性开关」时，子级各自写表达式跟随：

```js
thisComp.layer("父级名称").opacity
```

**自检**：任何「用父级控制可见性」的设计，都必须逐个确认子级。

> 真实事故：搬运块 rig 打了 0→100 的 opacity，但它的 5 个子级都没跟 →
> 结果整块从第 0 帧就挂在画面里；同时一个「厚度层」因为被别的脚本覆盖了表达式，
> 变成常显的灰色方块 → 用户直接指出「这个灰色是干嘛的」。

---

## 5｜设 `.parent` 会「保持原位」，坐标不是父级空间

**现象（两种，方向相反）**

- 想「让这层落在父级坐标系 (0,0)」→ 先 `position.setValue([0,0])`，再 `layer.parent = 父级` →
  该层**跑到画面左上角**。
- 想「让这层落在画面 (1050,540)」→ 先 `position.setValue([1050,540])`，再挂父级 →
  它**留在 (1050,540)**，但你期望的是「相对父级的某个点」。

**原因**
脚本里赋值 `layer.parent = X` 时，AE 会**自动补偿，保持视觉位置不变**（等同 UI 的
「保持位置」）。于是 `position` 的含义在赋值前后从「合成空间」变成了「父级空间」。

**正确写法**：**先挂父级，再设 position**，且 position 写的是**父级空间**的值。

```js
L.parent = rig;                 // 先
L.position.setValue([200, 0]);  // 后：这是相对 rig 的偏移
```

**自检**：写完任何 `parent` 赋值，回读一次 `position.value` 与 `parent.name`，确认数值符合预期。

---

## 6｜子级 Position 是父级空间；复杂父级不能直接“画布坐标 − 父级原点”

**现象**：一个放在画面中心的 rig / null 带有旋转、缩放、3D 倾斜或多级父子关系，把子级按画布绝对坐标打关键帧后整体偏位。

**原因**：存在 Parent 后，子级 Position 是**父级局部空间**中的值。只有父级接近 identity（无旋转、Scale=100%、无 3D / 多级变换）时，才可以把“画布坐标 − 父级位置”当作简单近似。

**正确写法**
- 简单 2D identity parent：可以用简单偏移换算；
- 父级有 Rotation / Scale / 3D / 多级 Parent：**禁止直接坐标相减**，应做完整空间变换换算，或从一开始就在父级局部空间设计坐标；
- 需要依赖 AE Layer Space Transform 时，可在 Expression 中使用 `toComp / fromComp / toWorld / fromWorld` 等空间转换；Raw JSX 若没有等价宿主 API，则使用明确的矩阵换算、临时表达式采样，或调整 Rig 架构，不能凭直觉相减；
- 如果整个模块本来就以画布绝对坐标为主，也可以避免不必要的 Parent，把共享运动交给更合适的控制结构。

**自检**：不仅回读 `position.value`；还要确认最终**可见的 comp/world-space 位置**是否与设计目标一致。父级自身也在运动时，只采子级 local Position 不能证明画面位置正确。

---

## 7｜Repeater 会级联作用；需要互不影响时才独立嵌套

**现象**：想做两组互不影响的柱状波形，却把两个「矩形 + 填充 + Repeater」平铺在同一个 Vectors Group，结果第二个 Repeater 又复制了前面的输出，宽度远超预期。

**原因**：Repeater 会对同组内位于其作用范围中的上游内容继续重复；多个 Repeater 放在同组时可以形成**级联重复**。这是 AE 的正常能力，不是错误——例如有意做二维网格时就可能需要 Repeater 重复另一个 Repeater。

**正确写法**
- 两组重复结构需要**互不影响** → 各自放进独立 Vector Group；
- 明确需要级联 / 网格效果 → 可以故意让多个 Repeater 处于同组，但必须把层级和作用范围设计清楚。

```js
var outer = root.addProperty("ADBE Vector Group");
var vg    = outer.property("ADBE Vectors Group");
// vg 内只放这一组需要独立控制的 Shape / Fill / Repeater
```

**自检**：用 `sourceRectAtTime()` / Shape 结构回读确认真实包围盒与 Repeater 层级；不要仅凭“看起来差不多”判断。

---

## 8｜`moveToBeginning()` 只移动调用它的那一层

**现象**：一个「蓝底白字」的 toast，为了压过别的层，
先 `text.moveToBeginning()`、再 `pill.moveToBeginning()` → **蓝底上没字了**。
（把顺序反过来写成先 pill 再 text，才正确。）

**原因**：`moveToBeginning()` 是逐层操作。后调用的那一层会**插到最前**，
把之前移上去的层又压下去。

**正确写法**：按「**从后往前**」的顺序移 —— 先移应该在最底下的，最后移应该在最上面的。

```js
pill.moveToBeginning();   // 先：底
text.moveToBeginning();   // 后：顶
```

**自检**：`layer.index` 回读，确认最终叠放顺序（1 = 最上）。

---

## 9｜缓动公式先分清映射方向：`time → progress` 与 `progress → time` 不能混用

**现象**：同一个公式看起来像“ease-out”，换到另一种时间映射写法后却出现末端暴冲，于是误以为公式本身方向反了。

**原因**
必须先定义变量：

### A｜直接映射：时间 → 路径进度
```text
u = normalized time
s = f(u) = path progress
```

这种最符合 Motion Designer 的直觉。例如 `k>1` 时：

```js
s = 1 - Math.pow(1 - u, k); // 快起步、慢收尾（常规 ease-out）
s = Math.pow(u, k);         // 慢起步、快收尾（常规 ease-in）
```

### B｜反向分配：路径进度 → 时间
```text
u = g(s)
```

此时真正的运动是 `s = g^-1(u)`，速度与 `1 / g'(s)` 相关；不能把 A 中的“ease-in / ease-out”标签原样搬过来。某些 `g(s)` 在末端导数趋近 0，就会让反函数速度在末端放大，产生暴冲。

**正确写法**
- 能直接写 `progress = f(time)` 时优先使用直接映射，最不容易把方向搞反；
- 必须使用 `time = g(progress)` 时，把它当“时间分配函数”而不是普通 easing，明确求逆后的速度趋势并采样验证；
- **不要把 `s^k` 或 `1-(1-s)^k` 任意一个写成普适正确答案。** 起点 / 终点速度、路径弧长和 Motion Profile 都会改变结论。

**自检**：先在注释中写清“自变量是谁、输出是谁”，再看采样速度是否符合当前阶段的预期 Profile。非单调速度并不天然错误；Bounce / Recoil / Impact 等本来就会改变方向或速度。

---

## 10｜其它高频小事（不成章节但会咬人）

- **`layer.property("ADBE Position")` 无效**：位置要走
  `layer.property("ADBE Transform Group").property("ADBE Position")`，或直接用 `layer.position`。
- **中继器 / 偏移路径等属性名用 matchName**：`ADBE Vector Filter - Repeater` /
  `ADBE Vector Repeater Copies` / `ADBE Vector Repeater Transform` / `ADBE Vector Repeater Position`。
- **写 Expression 前先确认该属性 canSetExpression**；形状路径要用 `createPath()`，
  不能直接对点数组赋值。

---

## 使用方式

1. **只在 Task Router 命中时读取**，不要让每个简单 JSX / MCP Patch 都支付这份上下文成本。
2. 报错信息或结构命中对应陷阱 → 先在本清单里找同款，再动手改。
3. 写完**回读**：结构变更后引用是否还有效、父级关系下的最终可见坐标、包围盒尺寸、叠放顺序。
4. Engine Room 专属行为（`run_jsx` Undo、`app.executeCommand`、截图 / Bridge 细节等）去 `../adapters/engine-room-mcp.md` 查当前版本规则。
5. 发现新坑 → 先记进当前任务 HANDOFF；只有证据稳定、作用域明确后，再按 Promotion Gate 决定是否进入长期规则。
