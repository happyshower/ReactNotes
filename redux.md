# 学习记录 7-14

## Redux

在 React 应用中，会存在很多组件，而每个组件都会存储自身的状态并自己进行管理（包括自身的 state，传递的 props，context 共享的数据），让每个组件的存储自身的状态理论上是不会影响应用的运行，但不利于后期的开发和维护，在这种情况下，将所有的状态进行集中式的管理是很有必要的（如数据存储在 redux 的 store），当需要更新状态时，仅需要对操作 store 即可，不需要关心状态是如何分发到组件内部。其次，由于 React 的数据在组件中是单向流动的，数据从父组件流向子组件（通过 props），但对于两个非父子组件之间通信就相对麻烦，但 redux 的出现使非关系组件也能共享数据。

redux 是一个实现集中式状态管理的容器，其遵循三大原则：

1. 单一数据源，整个应用的数据存储在一个统一的状态树中，即 store 文件，组件都会从这个 store 中获取数据
2. state 是只读的，唯一改变 state 的方法就是触发 action，action 是一个用于描述以发生时间的普通对象。
3. 使用纯函数来执行修改

执行流程：

1. createStore 创建 store
2. store.dispatch 触发 action
3. store.subscribe 订阅 store 变化

```
*********** store.js （全局应只有一个store.js）***********

//在新版本中createStore即将废弃，使用legacy_前缀即可
import { legacy_createStore as createStore, combineReducers } from "redux";

//当store只使用一个reducer时，直接传入需要使用的reducer即可
//当使用多个reducer时，需要需要使用combineReducers()合并reducers
const reducers = combineReducers({ count: countReducer, num: numReducer });

export default createStore(reducers);
```

```
*********** action.js ***********

//定义action并返回规定的格式数据，如{type: "", xxx: value},其中type是必不可少的
const incrementAction = (num: any) => {   //定义返回值中type为0时的方法
  return { type: "0", data: num };
};

const mulAction = (num: any) => {    //定义返回值中type为1时的方法
  return { type: "1", data: num };
};

export { incrementAction, mulAction };
```

```
*********** reducer.js  ***********

interface IActionProps {
  type: string;
  data: number;
}
const init = 1;   //初始化的数据
const countReducer = (preState = init, { type, data }: IActionProps) => {
  //根据type的类型来进行不同的操作并返回数据
  if (type === "0") {
    return preState + data;
  } else if (type === "1") {
    return preState * data;
  } else {
    return preState;
  }
};
```

```
*********** Count页面 ***********

const Count = () => {
  const [update, setUpdate] = useState({});

  useEffect(() => {
    store.subscribe(() => {    //监听store是否发生变化
      setUpdate({});        //触发页面更新
    });
  }, [update]);

  //调用对应的action返回包含type的普通对象，使用store的dispatch分发到reducer中进行数据的处理并返回结果
  const handChange1 = () => {
    store.dispatch(incrementAction(1));
  };

  const handChange2 = () => {
    store.dispatch(mulAction(2));
  };

  return (
    <>
      <h3>{store.getState().count}</h3>
      <button onClick={handChange1}>+1</button>
      <button onClick={handChange2}>*2</button>
    </>
  );
};
```
