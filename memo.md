# 学习记录 7-13

## useMemo()

useMemo(calculateValue, dependencies)

1. 第一个参数是一个函数，该函数在组件每次渲染时调用
2. 第二个参数是依赖列表，是一个数组，是所有在 calculateValue 函数中使用响应变量的数组
3. 该 hook 的返回值是 calculateValue 返回的缓存结果。
   注：在组件初次渲染时，useMemo 返回不带参数调用 calculateValue 的结果。
   当组件重新渲染时，若依赖项没有发生改变，它将返回上次缓存的值；否则将再次调用 calculateValue 函数计算并返回最新结果。

```
const Demo = (props) => {
  const [count, setCount] = useState(0);

  //若页面再次渲染时，使用 Object.is 将count与其之前的值进行比较
  //若count没有发生变化，则不再执行useMemo的回调，直接返回上次缓存的结果并跳过此次渲染
  //若count发生变化，调用useMemo的计算函数（第一个参数）重新计算并返回缓存结果
  const isEvenNumber = useMemo(() => {
    return count % 2 === 0;
  }, [count]);

  const onClick = ()=>{
    setCount(count+1)
  }

  return (
    <div>
      <div>{count} is {isEvenNumber ？ 'even':'odd'} number</div>
      <button onClick={onClick}></button>
    </div>
  );
};
```

## useCallback()

useCallback(fn, dependencies)

1. 第一个参数是一个函数，fn 为需要缓存的函数
2. 第二个参数是依赖列表 dependencies，是一个数组，由有关是否更新 fn 的所有响应式值组成
3. 该 hook 的返回值是传入的参数 fn 函数。
   注：在初次渲染时，useCallback 返回你已经传入的 fn 函数，在之后的渲染中, 若依赖没有改变，useCallback 返回上一次渲染中缓存的 fn 函数；否则返回这一次渲染传入的 fn。

```
const Button = ({onClick})=>{
  return <button onClick={onClick}>xxx</button>
}
const Test = ({value}) => {
  const [num, setNum] = useState(0);
  const isEvenNumber = (count % 2 === 0);

  //当页面重新渲染时，若该函数为普通函数或该函数中使用了响应值且没有发生改变，则跳过此函数的渲染
  //当该函数中的响应值没有发生改变时，返回传入的函数，否则 useCallback 将返回此次渲染中传递的函数。
  const onClick = useCallback(()=>{
    setCount(count+value)
  },[num,value])

  return (
    <div>
      <div>{num} is {isEvenNumber ？ 'even':'odd'} number</div>
      <Button onClick={onClick} />
    </div>
  );
};
```

### 两者的共同点

在依赖数据发生变化的时候，才会调用传进去的回调函数去重新计算结果，起到一个缓存的作用

### 两者的区别

1. useMemo 缓存的结果是回调函数中 return 回来的值，主要用于缓存计算结果的值，应用场景如需要计算的状态
2. useCallback 缓存的结果是函数，主要用于缓存函数，应用场景如需要缓存的函数，因为函数式组件每次任何一个 state 发生变化，会触发整个组件更新，一些函数是没有必要更新的，此时就应该缓存起来，提高性能，减少对资源的浪费。此外，一般会搭配 memo 进行使用。
