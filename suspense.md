# 学习记录 7-31

### Suspense

- Suspense 组件用于处理异步加载的组件，可以在组件加载过程中显示一个 loading 状态，直到异步加载的组件加载完成并渲染出来。
  - Suspense 组件必须包裹在其他组件中，用于指定需要异步加载的组件。
  - Suspense 组件可以设置 fallback 属性，用于指定在异步加载的组件加载过程中显示的 loading 状态。
  - Suspense 组件只能包裹一个或多个通过 React.lazy 函数异步加载的组件，不能包裹普通的组件。
  - 如果 Suspense 正在显示 React 组件树中的内容，但是被再次挂起（suspend），fallback 将再次显示，除非导致它的更新是由 startTransition 或 useDeferredValue 发起的。
  - React 不会保留任何在首次挂载前被挂起的渲染的任何状态。当组件完成加载后，React 将从头开始重新尝试渲染挂起的组件树。
  ```
  //fallback 接收任何有效的 React Node
  //在真正要显示的组件尚未加载完成时，代替其渲染的备用 UI
  <Suspense fallback={<Loading />}>
    <SomeComponent />   //当该组件在渲染中挂起，即尚未加载完成时，Suspense 边界将切换到渲染 fallback
  </Suspense>
  ```

### useDeferredValue

- useDeferredValue(value)，用于延迟更新某个值的渲染，以改善页面的响应性能。它适用于在用户输入频繁或处理大量数据时，避免过多的更新操作导致页面卡顿。它接受一个参数 value，value 为要延迟的值，可以是任何类型。返回值为 value 延迟更新后的值.
  - useDeferredValue 的主要用途是在性能优化方面。当某个值的更新不会立即影响到组件的渲染结果时，可以使用其来延迟获取和更新该值，以减少不必要的渲染操作。
  - useDeferredValue 可以与 React 的 suspense 机制一起使用，以实现更好的加载和渲染控制。当使用 suspense 时，可以将 useDeferredValue 的返回值作为 suspense 的 fallback，以在数据加载时展示一个占位符，直到数据加载完成后再展示真实的内容。
  - useDeferredValue 只是降低渲染的优先级而不是代码执行的优先级

```
const DeferrDemo = () => {
  const [value, setValue] = useState("");
  //接收value作为初始值，并返回延迟更新的值
  const deferredValue = useDeferredValue(value);

  useEffect(() => {
    const fetchData = async () => {
      const response = await fetch("https://api.example.com/data");
      const result = await response.json();
      setValue(result);
    };

    fetchData();
  }, []);

  return (
    <div>
    //结合 Suspense 组件在内部组件数据未渲染完成挂起时，显示 Loading...
      <Suspense fallback={<div>Loading...</div>}>
        <ul>
          //使用延迟的值进行数据渲染，当数据未获取到时，将该组件挂起。
          {deferredValue.map((item) => (
            <li key={item.id}>{item.name}</li>
          ))}
        </ul>
      </Suspense>
    </div>
  );
};
```

### lazy

- lazy(load)函数可以将组件进行懒加载。懒加载是一种优化技术，它可以延迟加载组件，只有在需要使用该组件时才会进行加载，从而减少初始加载时间和资源占用。该函数接受一个函数 load 作为参数，load 会动态地 import 组件。当组件被需要时，lazy 函数会返回一个 Promise，该 Promise 在组件加载完成后会 resolve 为一个包含默认导出组件的模块对象。

  - 该函数只能用于默认导出的组件，若组件使用命名导出，需要使用其他方法进行懒加载。
  - lazy函数只能在函数组件或其他lazy函数中使用，不能在类组件中使用
  - 该函数可以与Suspense组件一起使用，以实现更好的加载和渲染控制

  ```
  //将该组件进行懒加载，当页面渲染时，不会立马进行加载，而是在使用到该组件时才进行加载
  const LazyDemo = lazy(() => import('./LazyDemo'));

  const App = () => (
    <div> 
      //结合 Suspense 组件在页面懒加载时，在内部组件未加载完成时显示的加载状态
      <Suspense fallback={<div>Loading...<div/>}>
        <LazyDemo />
      </Suspense>
    </div>
  );
  ```
