# 学习记录 7-19

## js 操作符

1. 与运算符 &&
   ```
   a && b ? true : false   //当且仅当a和b同时为真时（a,b为0、null、undefined、负数时为false），返回true，否则false
   //与运算符的进一步使用
   let num = 0 ,count = 1;
   const fn = ()=> num
   count && fn()    //当count为真时，则执行fn 函数，其余运算符也可以进行类似的操作
   ```
2. 或运算符 ||
   ```
   a || b ? true : false   //当a和b有一个为真时，返回true，否则false
   ```
3. 空值合并操作符 ??
   空值合并操作符是一个逻辑操作符，当左侧的操作数为 null 或者 undefined 时，返回其右侧操作数，否则返回左侧操作数。
   ```
   a ?? b    //当a为 null 或者 undefined 时，返回 b
   ```
4. 可选链操作符 ?.
   可选链操作符允许读取位于连接对象链内的属性的值，而不必明确验证链中的每个引用是否有效，在引用为空(null 或者 undefined) 的情况下不会引起错误,仅仅只是不执行，该表达式短路返回值
   ```
   let a = {child:{ c:'5' },key:''2,b:''}
   a ?.child ?.c  //当a存在时返回child，当child存在时返回c
   ```

## useState 和 useReducer 的区别

useReducer 和 useState 都可以用来管理组件的状态，它们之间最大的区别就是：
useReducer 将状态和状态的变化统一管理在 reducer 函数里面，这样对于一些复杂的状态管理会十分方便调试，因为它对状态的改变是封闭的。而由于 useState 返回的 setState 可以直接在任意地方设置我们状态的值，当我们组件的状态转换逻辑十分复杂时，它将很难进行调试和维护，因为它是开放的状态管理。

1. useState 情况使用

   - state 的值是 JS 原始数据类型，如 number, string 和 boolean 等
   - state 的转换逻辑十分简单
   - 组件内不同的状态是没有关联的，它们可以使用多个独立的 useState 来单独管理

2. useReducer 情况使用
   - state 的值是 object 或者 array
   - state 的转换逻辑十分复杂, 需要使用 reducer 函数来统一管理
   - 组件内多个 state 互相关联，改变一个状态时也需要改变另一个，放在同一个 state 内使用 reducer 来统一管理
   - 状态定义在父级组件，不过需要在深层次嵌套的子组件中使用和改变父组件的状态，可以同时使用 useReducer 和 useContext 两个 hook，将 dispatch 方法放进 context 里面来避免组件的 props drilling
   - 当需要状态管理是可预测的和可维护的或者状态变化可以被测试的时候，可以使用 useReducer

## useReducer 和 react-redux

1. react-redux 实现了 React 友好的方法，并提供了许多中间件和包装器，如 Provider，connect，redux-thunk，提供结构化和集中式状态更新。此外，它可以通过上下文将状态提供给整个应用程序使用，然后提供 API 将深层嵌套的组件连接到该状态而无需传递道具。
2. useReducer 的状态仅针对单个组件-如果想要在整个应用中使用此状态，则需要 dispatch 通过 props 向下传递，提供结构化的本地状态更新。
