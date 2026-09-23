# Engineering｜AE26 / ExtendScript 实战陷阱

> 写入 JSX / Expression 前按需加载；写入后出现「说不通的失败」时也先查这里。
> 格式：**现象 → 原因 → 正确写法 → 自检**。
> 全部来自真实执行记录（AE26 中文版 / Windows / Engine Room MCP），不是理论推测。
> 与 `expressions-and-compatibility.md` 的分工：那边讲兼容性与写法约定，这边只讲**会直接报错或静默写错**的坑。

---

## 1｜形状组：加过子项之后，旧属性引用会失效

**现象**
先取到 `var rg = vg.property(1)`，接着 `vg.addProperty(...)` 加了填充 / 中继器，
再回来用 `rg.property("ADBE Vector Rect Size").expression = ...` → 报 **「对象无效」**。

**原因**
Vector Group 的子项结构一变，先前取到的 Property 引用即作废。

**正确写法**
优先「**先设完属性，再加子项**」：

```js
var rg = vg.addProperty("ADBE Vector Shape - Rect");
rg.property("ADBE Vector Rect Size").setValue([5, 22]);
rg.property("ADBE Vector Rect Size").expression = "...";   // ← 先设
vg.addProperty("ADBE Vector Graphic - Fill");              // ← 后加
```

必须晚设时，**重新取一遍引用**：`vg.property(1).property("ADBE Vector Rect Size")`。

**自检**：报「对象无效 / invalid object」时，先怀疑引用失效，而不是怀疑值不对。

---

## 2｜对象字面量不能用保留字当 key

**现象**：`{ name: L.name, in: L.inPoint }` → 报 **「非法使用保留字」**。
**原因**：ExtendScript 中 `in` / `for` / `class` / `delete` 等是保留字，不能作字面量键。
**正确写法**：`{ name: L.name, inPt: L.inPoint }`（键名加后缀或改写）。
**自检**：错误信息里出现「保留字」时，不要去看语法结构，直接扫对象字面量的 key。

---

## 3｜`ease()` 的 influence 下限是 0.1，不能用 0

**现象**：`ease(prop, 1, 0, 40)` → 报 **「值 0 在 0.1 至 100 的范围外」**（中文版可能是构造器报错）。
**原因**：AE 的 KeyframeEase influence 合法区间是 `(0.1, 100]`。
**正确写法**：想要「接近线性」就用 `0.1`：

```js
ease(prop, 1, 0.1, 95);   // 出帧接近线性
ease(prop, 2, 25, 55);
```
**自检**：凡是用 `ease()` 的脚本，先在脑子里把 0 换成 0.1。

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

## 6｜子级坐标属于父级空间（嵌套 rig 时最容易整层偏掉）

**现象**：一个 2.5D 倾斜 null 放在画面中心 (960,540)，把节点 / 搬运块 / 光标都挂进去，
按**画布绝对坐标**打关键帧 → 全部偏出画面。

**原因**：父级的原点 = 该父级自身的 (0,0) 位置。倾斜 null 的原点就在 (960,540)，
所以子级的 `[620,640]` 实际落在画面 `(1580,1180)`。

**正确写法**：子级坐标 = **画布坐标 − 父级原点**。
或者干脆**不挂父级**，用画布坐标，靠别的机制做分组运动。

**自检**：写完一组带 rig 的动画，把关键帧位置换算回画布坐标打印一次（父级位置 + 局部位置），
和设计稿逐个对。

---

## 7｜Repeater（中继器）必须「每组独立嵌套」，否则叠加重复

**现象**：想用中继器做两组柱状波形，把两个「矩形 + 填充 + 中继器」平铺在**同一个 Vectors Group** 里 →
波形宽度变成预期的**两倍**（320px → 644px），横跨出面板。

**原因**：中继器作用于**它上方同一组内的全部内容**。
第二个中继器把第一个中继器的**输出**又复制了一遍。

**正确写法**：每组各自放进**独立的 Vector Group**，中继器只在自己组内：

```js
var outer = root.addProperty("ADBE Vector Group");   // 每根柱组一个
var vg    = outer.property("ADBE Vectors Group");
// vg 内：Shape - Rect → Fill → Repeater
```

**自检**：写完用 `layer.sourceRectAtTime(t, false)` 读一次**真实包围盒**，
和设计宽度对；不要靠肉眼估。

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

## 9｜缓动方向：`1-(1-s)^k` 会让「末端时间几乎停住」，反而末端暴冲

**现象**：想让搬运块「起步快、末端减速吸附」，用了
`g(s) = 1 - (1-s)^1.8` 做时间映射（s = 路径进度）→ 实测末端**反而**暴冲（位移 132 / 0.1s）。

**原因**：这个曲线的导数在末端 → 0，意味着「末端**时间**几乎不再流动」，
于是最后一点点空间被压进极短时间里 → 速度飙升。

**正确写法**：要「起步快、末端慢」应当用 **凸函数** `g(s) = s^k (k>1)`：

```js
g(s) = Math.pow(s, 1.8);        // 起步快、末端持续减速
t = T0 + (T1 - T0) * g(s);
```

**自检（关键）**：**不要靠肉眼看**。每 0.1s 采样一次位置，算位移量：

```js
for (var t = t0; t <= t1; t += 0.1){
  var v = prop.valueAtTime(t, false);
  // 与上一采样点求距离 → 打印序列
}
```

期望：**单调递减 / 连续**，没有「暴冲-骤停-暴冲」的锯齿。
真实修前序列：`8 100 5 100 6 99`；修后：`128 65 52 41 40 34 … 18 18 18`。

---

## 10｜其它高频小事（不成章节但会咬人）

- **`layer.property("ADBE Position")` 无效**：位置要走
  `layer.property("ADBE Transform Group").property("ADBE Position")`，或直接用 `layer.position`。
- **`saveFrameToPng` 出的是带透明通道的 PNG**：预览器会显示成白底，
  不要据此判断「合成背景变成了白色」。
- **`saveFrameToPng` 之后 `File.exists` 可能返回 false**（对象缓存），**去磁盘上核对**。
- **`app.executeCommand()` 在 MCP 环境里静默无效**（依赖 UI 焦点），
  用 API 等价物（`duplicate()` / `remove()` 等）。
- **中继器 / 偏移路径等属性名用 matchName**：`ADBE Vector Filter - Repeater` /
  `ADBE Vector Repeater Copies` / `ADBE Vector Repeater Transform` / `ADBE Vector Repeater Position`。
- **写 Expression 前先确认该属性 canSetExpression**；形状路径要用 `createPath()`，
  不能直接对点数组赋值。

---

## 使用方式

1. 写 JSX 前扫一眼本清单（尤其 1 / 5 / 6 / 7）。
2. 报错信息 → 先在本清单里找同款，再动手改。
3. 写完**回读**：结构变更后引用是否还有效、父级关系下的真实坐标、包围盒尺寸、叠放顺序。
4. 发现新坑 → 先记进当前任务的 HANDOFF，再按 Promotion Gate 决定是否写入本文件。
