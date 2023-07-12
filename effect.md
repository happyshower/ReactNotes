# 学习记录 7-12

## useEffect()

useEffect 用于处理组件中的副作用，用来取代生命周期函数。
useEffect(setup, dependencies?) 接收两个参数，第一个参数 setup 是一个函数，在 Effect 执行时调用，第二个参数 dependencies 是可选参数，是一个数组类型。

1. 当不传入第二个参数时，默认监听挂在当前节点组件中的所有状态 state；
2. 当第二参数传入是一个空数组时，在组件第一次渲染时调用，此后不再调用。
3. 第二参数的组中传入的是一个状态值时，当该状态发生改变时，会再次触发执行该 effect 的回调
4. 该 hook 返回值是 undefined，但第一个参数回调的返回值是一个函数，该函数在状态值发生改变前调用和组件卸载前调用

```
const A = () =>{
  const [num,setNum] = useState(0)    //该组件的状态值 num

  useEffect(()=>{  //在该函数处理副作用，如开启定时器，请求数据等

    const timer = setInterval(()=>{    //开启定时器
      setNum((preState)=>preState+1)
    },1000)

    return ()=>{
      clearInterval(timer)       //组件卸载前调用或监听的状态值发生改变前清除定时器
    }
  },[num])   //监听num的变化
}
```

```
在数组中传入监听依赖时，避免使用渲染期间创建的对象作为依赖项
const num = 0;
const A = ({count}) =>{
  //每次组件渲染时都会生成sum对象或当传入的count发生改变时，也会重新生成sum
  //因此会频繁触发effect，不建议使用该属性作为依赖项
  //const sum = { num, count}    //不推荐

  useEffect(()=>{
    const sum = { num, count}    //推荐
    return ()=>{
      //todo
    }
  },[num])  //应直接监听 num 而不是 sum
}
```
