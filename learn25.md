## 学习记录 7-25

### react router（v6）常用 hook 的使用

- useHref：它用于获取指定位置对象的 URL，接受一个位置对象作为参数，并返回该 location 对象对应的 URL 字符串

  ```
  const Demo = () => {
    const href = useHref('/users?id=123');

    return (
      <div>
      //返回一个URL字符串：/user?id=123
        <span>URL: {href}</span>
      </div>
    );
  };
  ```

- useMatch：用于在组件内部获取与当前 URL 匹配的路径模式的信息，如路径参数、URL 参数等

  ```
  const Demo = () => {
    const match = useMatch('/users/:x/:y');
    //返回的 match 对象如下：
    <!-- {
      params: {x: '1', y: '10'}
      pathname: "/LoGin/1/10"
      pathnameBase: "/LoGin/1/10"
      pattern: {
        path: '/login/:x/:y',
        caseSensitive: false,
        end: false
      }
    } -->

    if (match) {
      // 匹配成功，获取路径参数
      const { id } = match.params;
      // 使用路径参数进行处理
    }

    return (
      <div>
        {match ? <span>Matched! ID: {match.params.id}</span> : <span>No match</span>}
      </div>
    );
  };
  ```

- useNavigate：该函数可以用于在组件内部进行编程式的路由导航

  ```
  const Demo = () =>{
    let navigate = useNavigate();
    const handleClick = (path) => {
      //跳转至home页面
      navigate(path);
    }

    return (
      <button onClick={() => handleClick("/home")}>Go back</button>
    )
  }
  ```

- useOutletContext：它允许开发者在嵌套路由中访问当前出口的上下文。

  ```
  const OutletDemo = () =>{
    return (
      <div>
        //通过Outlet组件的context属性实现向子路由的传递数据
        <Outlet context ={{ num:0, count:1 }} />
      </div>
    )
  }

  const ContextDemo = () => {
    //在路由的子组件中通过useOutletContext()获取上级传递的数据
    const { num, count } = useOutletContext();

    return (
      <div>
        <p>Value 1: {num}</p>
        <p>Value 2: {count}</p>
      </div>
    );
  };
  ```

- useResolvedPath(): 给定一个 URL 值，解析其中的：path、search、hash 值
- useInRouterContext(): 如果组件在 <Router> 的上下文中呈现，则 useInRouterContext 钩子返回 true，否则返回 false

### flex 布局子元素高度或宽度破坏整体布局

使用 flex 布局时，通常使用 flex:1 自动撑满容器，但当子元素的内容太高或者太宽时，会撑开父容器的高度，此时应该给该容器添加 overflow-y:auto 属性，让可视区域的内容可滚动，从而避免布局混乱。

```
export const FlexDemo = () => {
  return (
    <div className="w-full h-screen bg-[skyblue] flex justify-center item-center flex-col">   //此时的高度为892px

      //顶部栏区域
      <div className="w-full h-full bg-[#orange]"></div>

      //导航栏和内容渲染区域
      <div className="w-full flex-1 flex justify-center item-center overflow-y-auto">    //添加overflow属性，使该区域的内容可滚动

        //左侧导航栏，固定宽度
        <div className="w-[200px] h-full bg-[gray]"></div>

        //右侧内容区域，使用flex:1自动撑满剩下的宽度
        <div className="flex-1 h-ull w-ful overflow-y-auto">   //添加overflow属性，使该区域的内容可滚动

          //内容区域（此时自元素的高度已经高于父容器，会撑开父容器，破坏布局）
          <div className="h-[1250px] bg-[lawngreen]"></div>
        </div>
      </div>
    </div>
  );
};
```
