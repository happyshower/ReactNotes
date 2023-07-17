# 学习记录 7-17

## react-redux

1. 所有的 UI 组件都应该包裹一个组件，它们是父子关系
2. 容器组件是真正和 redux 打交道的，里面可以随意的使用 redux 的 API
3. UI 组件中不能使用任何的 redux 的 api
4. 容器组件会通过 props 将 redux 中保存的状态和操作状态的方法传递给 UI 组件

使用方式：connect(mapStateToProps, mapDispatchToProps)接收两个参数，第一个参数将 store 通过 props 注入到 UI 组件中，第二个参数向组件中注入一些函数，这些函数是用来派发 action 的，组件可以通过 props 接收函数并调用传参，把要修改的值传过去，然后通知仓库修改数据。除此之外，还要在容器组件的上一级传递 store 参数，如：

```
import { Provider } from "react-redux";
import store from "./react-redux/store";

//通过Provider组件的store属性传递store参数
<Provider store={store}>
  <Demo />
</Provider>
```

```
------ store.js ------
import { legacy_createStore as createStore } from "redux";

import { numReducer } from "../reducers/index";

export default createStore(numReducer);

```

```
 ------ action.js ------
export const incrementAction = (num: number) => {
  return { type: "0", data: num };
};
export const decrementAction = (num: number) => {
  return { type: "1", data: num };
};

```

```
------ reducer.js ------
interface IReducerProps {
  type: string;
  data: number;
}
const initState: number = 0;
export const numReducer = (pre = initState, { type, data }: IReducerProps) => {
  switch (type) {
    case "0":
      return pre + data;
    case "1":
      return pre - data;
    default:
      return pre;
  }
};

```

```
------ UI组件和容器组件 ------
onst Demo = (props) => {
  console.log(props);
  const { num, increase, decrease } = props;

  const handleIncrement = () => {
    increase(1);
  };
  const handleDecrement = () => {
    decrease(1);
  };
  return (
    <>
      <span>Num：{num}</span>
      <br />
      <button onClick={handleIncrement}>+1</button>
      <button onClick={handleDecrement}>-1</button>
    </>
  );
};
//方式1:
//映射状态，返回一个对象
const mapStateToProps = (state) => ({ num: state });
//映射操作状态的方法，返回一个对象
const mapDispatchToProps = (dispatch) => {
  return {
    increase: (num) => dispatch(incrementAction(num)),
    decrease: (num) => dispatch(decrementAction(num)),
  };
};
//通过connect将属性和组件联合起来，创建并返回一个新的组件
export default connect(mapStateToProps, mapDispatchToProps)(Demo);

//方式2（简写方式）:
export default connect((state) => ({ num: state }), {
  increase: incrementAction,
  decrease: decrementAction,
})(Demo);
```
