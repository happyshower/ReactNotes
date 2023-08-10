# 学习记录 8-10

### Fiber

1. Fiber 的了解

   - 从架构角度来看，Fiber 是对 React 核心算法（即调和过程）的重写。

   - 从编码角度来看，Fiber 是 React 内部所定义的一种数据结构，它是 Fiber 树结构的节点单位，也就是 React 16 新架构下的"虚拟 DOM"。

2. React 的工作流程
   - 调和阶段(Reconciler)：用新的数据生成一棵新的树，然后通过 Diff 算法，遍历旧的树，快速找出需要更新的元素，放到更新队列中去，得到新的更新队列。（这个过程可以被中断）
   - 渲染阶段(Renderer)：遍历更新队列，通过调用宿主环境的 API，实际更新渲染对应元素。宿主环境如 DOM、Native、WebGL 等。（该过程不可中断）
3. Stack Reconciler
   - 在 React 16 之前，在调和阶段阶段，采用的是递归的遍历方式（同步代码）即 Stack Reconciler（堆协调器）。该方式的特点是，一旦任务开始遍历，便不可中断，便会一直占用 js 的主线程，若组件树(Virtual DOM 树)的层级越深，递归更新组件的时间越长，需要等到整棵组件树计算完成后，才会让出主线程将执行权交给渲染引擎，这样一来便会导致一些用户交互、动画等任务无法立即得到处理，造成页面卡顿。
4. Fiber Reconciler
   - React 16 及以后使用的是 Fiber Reconciler（纤维协调器），将递归中无法中断的更新重构为迭代中的异步可中断更新过程，这样就能够更好的控制组件的渲染。
5. Fiber Tree 的结构和双缓存树
   - Fiber Tree 由链表构成，节点间通过 return（父节点）、child（第一个子节点）、sibling（下一个兄弟节点）相连。双缓存树是指当前视图对应的 Fiber 树称为 current tree，每次协调发起，都会构建新的 workInProgress tree，通过 diff 算法对比两颗树的差异，并在结束时替换 current tree。
6. React Fiber 工作原理
   - Fiber 把一个渲染任务分解为多个渲染任务，而不是一次性完成，把每一个分割得很细的任务视作一个"执行单元"，React 就会检查现在还剩多少时间，如果没有时间就将控制权让出去，故任务会被分散到多个帧里面，中间可以返回至主进程控制执行其他任务，最终实现更流畅的用户体验。即是实现了"增量渲染"，实现了可中断与恢复，恢复后也可以复用之前的中间状态，并给不同的任务赋予不同的优先级，其中每个任务更新单元为 React Element 对应的 Fiber 节点。
7. Fiber 的工作流程
   - 从根节点开始遍历 Fiber Node Tree， 并且构建 WokeInProgress Tree（reconciliation 阶段）。
     - 本阶段可以暂停、终止、和重启，会导致 react 相关生命周期重复执行。
     - React 会生成两棵树，一棵是代表当前状态的 current tree，一棵是待更新的 workInProgress tree。
     - 遍历 current tree，重用或更新 Fiber Node 到 workInProgress tree，workInProgress tree 完成后会替换 current tree。
     - 每更新一个节点，收集节点产生的变更，同时生成该节点对应的 Effect List。
     - 为每个节点创建更新任务。
   - 将创建的更新任务加入任务队列，等待调度。
     - 调度由 scheduler 模块完成，其核心职责是执行回调。
     - scheduler 模块实现了跨平台兼容的 requestIdleCallback。
     - 每处理完一个 Fiber Node 的更新，可以中断、挂起，或恢复。
   - 根据 Effect List 更新 DOM （commit 阶段）。
     - React 会遍历 Effect List 将所有变更一次性更新到 DOM 上。
     - 这一阶段的工作会导致用户可见的变化。因此该过程不可中断，必须一直执行直到更新完成。
