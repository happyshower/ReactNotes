# 学习记录 8-1

### startTransition

- startTransition 接受一个回调函数作为参数，并在下一次渲染时调用该回调函数。在调用回调函数时，React 会将渲染优先级降低，以便在渲染期间执行其他高优先级的任务。这样可以避免在渲染期间执行昂贵的操作，从而提高应用程序的响应性能。

  - startTransition 只能在事件处理函数或其他 React 的副作用函数中调用，不能在组件的渲染方法中调用。
  - startTransition 不能用于同步操作，只能用于延迟执行的异步操作。
  - startTransition 只能延迟执行指定的回调函数，而不能控制回调函数的执行顺序或并发性。

  ```
  const Demo = () => {
    const [list, setList] = useState([]);
    const [value, setValue] = useState("");

    const handleInput = (event) => {
      const items = [];
      //需要耗费大量的时间进行渲染，阻塞渲染进程
      for (let i = 0; i < 2000; i++) {
        event.target.value && items.push(event.target.value);
      }
      setValue(event.target.value);
      //使用 startTransition api 标志 setList 为 transition，降低渲染级别
      startTransition(() => {
          setList(items);
      });
    };

    return (
      <>
        <input type="text" value={value} onChange={(e) => handleInput(e)} />
        <ul>
          {list.length > 0 &&
            list.map((item, index) => <li key={index}>{item}</li>)}
        </ul>
      </>
    );
  };
  ```

### useTransition

- useTransition 不需要任何参数，其返回值是一个由两个元素组成的数组,分别是：

  - isPending：是否存在待处理的转换。当任务处于悬停状态的时候，isPending 为 true，可以作为用户等待的 UI 呈现
  - startTransition：允许你将状态更新标记为转换状态，与 startTransition api 的作用一致

  ```
  const Demo = () => {
    const [list, setList] = useState([]);
    const [value, setValue] = useState("");
    const [isPending, startTransition] = useTransition();

    const handleInput = (event) => {
      const items = [];
      //阻塞渲染进程
      for (let i = 0; i < 2000; i++) {
        event.target.value && items.push(event.target.value);
      }
      setValue(event.target.value);
      //降低渲染级别
      startTransition(() => {
        setList(items);
      });
    };

    return (
      <>
        <input type="text" value={value} onChange={(e) => handleInput(e)} />
        <ul>
          //根据isPending的状态来决定是否渲染页面
          {!isPending &&
            list.length > 0 &&
            list.map((item, index) => <li key={index}>{item}</li>)}
        </ul>
      </>
    );
  };
  ```

### useDeferredValue 和上述 useTransition 区别

- 相同点：

  - useDeferredValue 本质上和内部实现与 useTransition 一样都是标记成了过渡更新任务。

- 不同点：

  - useTransition 是把 startTransition 内部的更新任务变成了过渡任务 transtion,而 useDeferredValue 把原值通过过渡任务得到新的值，这个值作为延时状态。 一个是处理一段逻辑，另一个是生产一个新的状态。useDeferredValue 还有一个不同点就是这个任务，本质上在 useEffect 内部执行，而 useEffect 内部逻辑是异步执行的 ，所以它一定程度上更滞后于 useTransition。useDeferredValue = useEffect + transtion
