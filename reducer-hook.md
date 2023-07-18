# 学习记录 7-18

## useReducer

在组件的顶层作用域调用 useReducer 来创建一个用于管理状态（state）的 reducer。

useReducer(reducer, initialArg, init?)

参数：

1. reducer：用于更新 state 的纯函数。参数为 state 和 action，返回值是更新后的 state。state 与 action 可以是任意合法值。
2. initialArg：用于初始化 state 的任意值。初始值的计算逻辑取决于接下来的 init 参数。
3. 可选参数 init：用于计算初始值的函数。如果存在，使用 init(initialArg) 的执行结果作为初始值，否则使用 initialArg。

返回值（useReduce 的返回值是一个数组，如([state，dispatch])：

1. state：初次渲染时，它是 init(initialArg) 或 initialArg （如果没有 init 函数
2. dispatch：一个用于更新 state 并触发组件的重新渲染的函数，其接收一个参数 action(action 是一个对象，其中 type 属性标识类型，且 action 也可携带额外的数据)

使用方式：const [state,dispatch] = useReducer(reducer,initValue,fn)

```
import { useReducer } from "react";

interface IReducerActionProps {
  type: string;
  data: number;
}
const ReducerDemo = () => {
  //普通初始化
  const [state, dispatch] = useReducer(reducer, 0);
  //惰性初始化
  //惰性初始化可以提高性能，惰性初始化使得数据不会再一开始就进行初始化，而是在使用的时候再进行初始化，一般返回一个数字，也可以是数字，但不推荐返回数字
  const [state, dispatch] = useReducer(reducer, 0，()=> 0);

  //接收前一个state值和一个action对象
  const reducer = (preState = 0, action: IReducerActionProps) => {
    const { type, data } = action;
    switch (type) { 
      case "up":
        return preState + data;    //返回新的state
      case "down":
        return preState - data;
      default:
        return preState;
    }
  };
  const handleUp = () => {
    dispatch({ type: "up", data: 1 });   //通过dispatch分发action到reducer中，更新state并触发组件重新渲染
  };
  const handleDown = () => {
    dispatch({ type: "down", data: 1 });
  };

  return (
    <>
      <span>{state}</span>
      <br />
      <button onClick={handleUp}>up</button>
      <button onClick={handleDown}>down</button>
    </>
  );
};

export default ReducerDemo;

```
