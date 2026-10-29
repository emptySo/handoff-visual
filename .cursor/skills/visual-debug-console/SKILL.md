---
name: visual-debug-console
description: >-
  Adds the Figma-style visual debug console to every new visual page. Opening
  it freezes normal interaction; clicking an on-screen element selects it;
  dragging moves it; the transform box resizes and rotates it; closing restores
  interaction. Use when creating or editing a visual page, 视觉前端, 新页面,
  调试, 控制台, 界面调试, 缩放框, or layout tuning. Do not add it to the
  functional prototype unless the user asks.
---

# 视觉调试控制台

每个新的视觉页面都要带这套控制台。参照实现是 `视觉前端.html`。交互照那一页做，不要改成另一套选中或拖动方式。

用户要求的行为：

> 打开控制台界面后，整个画面就进入调试状态，就不可再交互了，然后这个时候选择画面内的元素可以直接进行定位，然后选中画面内元素后，可以拖动来改变位置，然后加入缩放框，鼠标拖动缩放框可以改变大小以及角度，逻辑参考figma等常用软件。关闭控制台然后可以恢复原始交互状态。

## 行为

- 打开控制台：给 `body` 加上 `is-debug`。页面原有点击（切换界面、提交、打开面板）全部不执行。
- 点画面里的元素：选中它，并让控制台的类型和对象跳到这一项。这就是定位。
- 拖选中的元素：改位置。元素跟着指针走。
- 缩放框套在选中元素的边框上，并跟着元素的旋转走。四个角和四条边改大小，对面的手柄保持不动。正上方的圆点改角度。按住 Shift 时角度按 15° 吸附。按住 Alt 时从中心缩放。
- 点空白处取消选中。背景画面不能被选中，也不进控制台列表。
- 关闭控制台：去掉 `is-debug`，隐藏缩放框，原有交互恢复。
- 数字滑块仍然改同一组 x、y、宽度、角度，并和缩放框同步。
- 调试窗口的标题可以拖动，把窗口挪开，免得挡住正在调的元素。
- 同一组里的元素可以上移一层、下移一层，或直接改图层数字。数字大的盖住数字小的。便签只和同一张纸上的便签、铅笔比较；纸条和网格纸互相比较；三张卡包互相比较；四个侧边按钮互相比较。
- 侧边按钮的第二态要淡入，时间和别的按钮一样，是 420ms。卡包的彩色图要写在每一张图上，不要只改整组父级的透明度。

## 接到新页面

1. 只加在视觉页面上。不要加进 `交接功能原型-v2.html`，除非用户当次明确要求改那个文件。
2. 每个可调元素加稳定的标记（现页用 `data-piece`，侧边按钮用 `.seam`，叠放的层用 `data-layer`）。背景节点不要加标记。
3. 复制 `视觉前端.html` 里的 `#xform` 缩放框、`body.is-debug` 样式，以及指针这三段：`pointerdown` 里区分手柄 / 命中元素 / 空白，`pointermove` 里移动、缩放、旋转，`click` 捕获阶段在调试中拦住页面点击。
4. 原有点击处理开头加 `if (document.body.classList.contains("is-debug")) return;`。
5. 命中时选最上面的可调元素。叠在里面的便签优先于底下的纸。看不见的按钮（`opacity: 0` 或 `pointer-events: none`）不能被选中。
6. 位置换算进该元素自己的坐标系：相对屏幕用 vw / vh，相对父级用百分比，叠放层的位移用它自身宽高的百分比。父级如果有旋转，先把屏幕位移换算到父级的轴上。
7. 调试中不要播放位置过渡。松手后再写入该页的布局存档。

## 不要改

- 不改被调试页面原来的切换、提交和存档含义。
- 不把背景放进可调列表。
- 不在控制台关闭时留下 `is-debug` 或挡在页面上的缩放框。
