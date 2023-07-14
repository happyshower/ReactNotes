# 学习记录 7-11

## createContext()和 useContext()

createContext()接收一个参数，该参数作为上下文没有提供值时的默认值，且该值在初始化后不会再发生改变，该 hook 返回一个 context 上下文对象，将需要传递数据的组件以 xxxContext.Provider 的形式包裹在内，里面的组件都可通过 context 上下文对象获取传递的数据，如：
const demoContext = createContext(defaultValue) //defaultValue，当上下文环境中没有找到值时的备选值（默认值）

```
<demoContext.Provider value={data}> //通过 value 将 data 放进 context 上下文对象中
  <A />
  <B />
</demoContext.Provider>
```

以下两种方法获取上下文的值:1.函数式获取 context 传递的值（不推荐）

```
const A = () =>{
return (
<demoContext.Consumer>
  {
    data => <h1>{data}</h1>
  }
<demoContext.Consumer>
)
}
```

2.通过 hook useContext()来获取（推荐）
该 hook 接收一个参数，该参数为 createContext 创建的 context，并返回 context 上下文的值，且该值为该 context 向上查找的最近的 context Provider。

```
const data = useContext(demoContext);
const A = () =>{
  return <h1>{data}</h1>
}
```

## 自定义 Hook 的使用

1. 自定义 Hook 的命名方式必须是以后跟一个大写字母的 use 开头，如 useAction()等
2. 自定义 Hook，是对于复用代码的抽取和封装，其共享的是状态逻辑，而不是共用同一个状态。如一个自定义 Hook useAction(),当在组件中调用两次该 hook 或两个组件同时调用该 hook 时，那两者中的状态和副作用都是相互隔离的。

```
const useAction = () =>{
const [num,setNum] = useState()
  return { num }
}

若 A 组件和 B 组件内都使用了该 hook，那么两个组件分别会生成该组件的状态和副作用的副本来保存两个组件的数据，两者的数据并不相通。
```

3. 自定义 hook 和普通函数：
   a. hook 函数也是特殊的函数
   b. 若函数中使用了 react 的 hooks,如 useState、useEffect 等来启动逻辑、添加状态或添加副作用，那该函数就是一个 hook
   c.hook 是以 use 为开头为约定，但并不是硬性要求。
   d. 普通函数，其数据是在所有调用者中共享的；hook 函数，其状态或者说数据存放于当前组件节点当中。
