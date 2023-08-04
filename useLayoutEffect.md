# 学习记录 8-3

### useLayoutEffect

- useLayoutEffect 在浏览器完成渲染之后和屏幕更新之前在 React 组件中执行副作用。它类似于 useEffect hook，但它在 React 执行完所有 DOM 变更后立即同步运行。
- useLayoutEffect(setup, dependencies?)接收两个参数，第一个参数 setup 是一个函数，在 Effect 执行时调用，第二个参数 dependencies 是可选参数，是一个数组类型。
  1. 当不传入第二个参数时，默认监听挂在当前节点组件中的所有状态 state；
  2. 当第二参数传入是一个空数组时，在组件第一次渲染时调用，此后不再调用。
  3. 第二参数的组中传入的是一个状态值时，当该状态发生改变时，会再次触发执行该 effect 的回调
  4. 该 hook 返回值是 undefined，但第一个参数回调的返回值是一个函数，该函数在状态值发生改变前调用和组件卸载前调用
- useLayoutEffect 在 DOM 更改后立即同步运行，这意味着如果它执行大量计算或直接操作 DOM，它可能会阻止屏幕的绘制。在大多数情况下，使用常规的 useEffect 挂钩，因为它在渲染提交到屏幕后异步运行

```
const LayoutDemo = () => {
  const [width, setWidth] = useState(0);

  useLayoutEffect(() => {
    //在每次调整窗口大小时更新宽度状态变量
    const updateWidth = () => {
      setWidth(window.innerWidth);
    }
    //监听窗口变化
    window.addEventListener('resize', updateWidth);
    updateWidth();  //初始化宽度 width
    return () => {
      //在组件卸载时执行，取消监听
      window.removeEventListener('resize', updateWidth);
    };
  }, []);

  return (
    <div style={{width:width/2,height:width/4,backgroundColor:"pink",margin:"auto"}}>
      <p>Window Width: {width}px</p>
    </div>
  );
};
```
##### useLayoutEffect 和 useEffect 区别：
- useLayoutEffect是同步的，就是说在页面上出现dom元素之前，会等待useLayoutEffect的执行。也就是说，react处理的dom结构，在提交给浏览器渲染之前，会等待useLayoutEffect的再处理（也就是说会阻碍页面渲染）。
- useEffect是异步的，对于每一个 effect 的 callback 都会放入任务队列，等到主线程任务完成，DOM 更新，js 执行完成，视图绘制完毕，才执行，所以 effect 回调函数不会阻塞浏览器绘制视图不会阻碍页面渲染，当浏览器已经渲染页面后，才会被执行。

### useInsertionEffect

- useInsertionEffect，允许开发人员在元素被插入到 DOM 中时执行一些操作。在 React 中，元素被插入到 DOM 中通常是在组件挂载时发生的可以在布局副作用触发之前将元素插入到 DOM
- useInsertionEffect 是在元素插入到 DOM 中时执行的，因此它可以确保操作的高效性能，而不会影响渲染速度
- useInsertionEffect 可以执行任意操作，因此它可能会导致一些副作用，例如对 DOM 进行修改或触发网络请求等，需谨慎使用

```
const Demo = () => {
  //在类名为my-element的div插入到DOM时，添加样式 fade-in
  useInsertionEffect(() => {
    document.querySelector('.my-element').classList.add('fade-in');
  });

  return (
    <div className="my-element">Hello World!</div>
  );
}

```
