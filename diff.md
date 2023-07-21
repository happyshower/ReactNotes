#学习记录 7-21

## useRef

1. 创建： 使用 useRef 创建一个引用，可以通过 const ref = useRef(initialValue) 的方式进行创建。这里的 initialValue 是可选的，它可以是任何初始值，如 DOM 节点、对象、数组等。
2. 获取： 使用 ref.current 来获取引用的当前值。ref.current 是一个可变的属性，可以随时读取或修改引用的值
3. 引用的更新不会引发组件重新渲染： 不同于状态变量，修改引用的值并不会触发组件的重新渲染。这是因为 ref.current 的变化并不会引发组件的更新，只有当组件重新渲染时，ref.current 才会被更新为最新的值

```
const RefDemo = () => {
  const [num,setNum] = useState(0)
  const selectRef = useRef(null);

  const handleClick = () => {
    setNum(selectRef.current.value)
  };

  return (
    <div>
      <span>num: {num}</span>
      <select ref={selectRef}>
        <option value="1">1</option>
        <option value="2">2</option>
        <option value="3">3</option>
      </select>
      <button onClick={handleClick}>Get Selected Value</button>
    </div>
  );
};
```

## diff 算法

diff 算法的本质：找出两个对象（使用 js 对象来表示页面中的 dom 结构，即 virtual dom 虚拟 dom 树）之间的差异，目的是尽可能做到节点复用。

1. 调和：将 Virtual DOM 树转换成 Actual DOM 树的最少操作的过程称为调和
2. diff 算法：diff 算法是调和的具体实现
3. diff 策略：
   - tree diff
     - 对树分层比较，两棵树只对同一层次节点进行比较。如果该节点不存在时，则该节点及其子节点会被完全删除，不会再进一步比
   - component diff
     - 如果都是同一类型的组件(即：两节点是同一个组件的两个不同实例)，按照原策略继续比较 Virtual DOM 树即可
     - 如果出现不是同一类型的组件，则将该组件判断为 dirty component，从而替换整个组件下的所有子节点
   - element diff
     - 对于同一层级的一组子节点，可以通过唯一 key 进行区分
     - 当节点处于同一层级时，diff 提供三种节点操作：删除、插入、移动。

## 渲染和提交

React 的工作主要分为两个阶段,分别是：

1. render 阶段（渲染阶段）：计算组件的输出并收集所有需要应用到 DOM 上的变更。
   - 调用函数组件或类组件的 render 方法，生成虚拟 DOM。
   - 对比新旧虚拟 DOM 树的差异，找出需要更新的部分。
   - 生成更新队列，记录需要更新的组件和对应的更新操作。
2. commit 阶段（提交阶段）：将 render 阶段计算出的变更应用到 DOM 上。
   - 执行更新队列中的更新操作，将变化应用到真实 DOM 上。
   - 触发钩子函数（如 useEffect）。
   - 清空更新队列，更新完成。

#### 拓展

闭包：是指能够读取其他函数内部变量的函数。
用途：

1. 可以读取函数内部的变量
2. 让变量的值始终保持在内存中，不会在调用后被自动清除

缺点：

1. 由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，可能导致内存泄露。
2. 闭包会在父函数外部，改变父函数内部变量的值。如果把父函数当作对象使用，把闭包当作它的公用方法，把内部变量当作它的私有属性时，请不要随便改变父函数内部变量的值。

```
const outer = () => {
  let a = 1;

  const inner = (b) => {
    return a+b;
  };

  return inner;
};

const closure = outer();
closure(5);    //返回的结果为6

```
