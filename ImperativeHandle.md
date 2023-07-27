# 学习记录 7-27

### useImperativeHandle

- imperativeHandle(ref,createHandle,dependencies)：用于向父组件暴露子组件的实例或某些方法。它可以用于在函数组件中，通过 ref 获取子组件的实例或方法，并在父组件中进行操作
- 参数：

  - ref：forwardRef 渲染函数传递过来的参数 ref
  - createHandle：createHandle 为一个无参函数，返回一个包含暴露 ref 句柄的对象
  - dependencies：可选参数，为 createHandle 中所用到的所有反应式的值的列表

  ```
  const SonDemo = forwardRef((props, ref) => {
    const loaclInput = useRef(null);
    const extralInput = useRef(null);

    //传入父组件传递过来的 ref 参数
    useImperativeHandle(ref, () => {
      return {
        //向父组件 Demo 暴露操作 SonDemo 组件以及其子组件的输入框聚焦的句柄
        SonInput:()=>loaclInput.current.focus(),
        GrandsonInput:()=>extralInput.current.GrandsonInput()
      }
    });
    return (
      <>
        Son
        <input ref={loaclInput} />   //给子组件传递 ref 属性
        <hr />
        <GrandSon ref={extralInput} />  //给子组件传递 ref 属性
      </>
    );
  });

  const GrandSon = forwardRef((props, ref) => {
    const input = useRef(null)

    useImperativeHandle(ref,()=>{
      return {
        GrandsonInput:()=> input.current.focus()   //向父组件暴露操作自身的输入框聚焦的句柄
      }
    })
    return (
      <>
        GrandSon
        <input ref={input}/>
      </>
    );
  });

  const Demo = () => {
    const demo = useRef(null);   //创建一个传递给子组件的 ref

    const handleChild = () => {
      demo.current.SonInput();     //通过 demo 的 current 属性调用子组件暴露的方法
    };
    const handleOther = () => {
      demo.current.GrandsonInput();
    };

    return (
      <>
        <SonDemo ref={demo} />    //给子组件传递 ref 属性
        <hr />
        <button onClick={handleChild}>子组件输入框聚焦</button>
        <button onClick={handleOther}>孙组件输入框聚焦</button>
      </>
    );
  };
  ```

### React Activation 实现路由缓存

- React Activation 实现 keep-alive 功能保留组件状态。react 和 vue 大多数会选择单页面开发，即各种组件组成一个项目，每个页面其实都是一个组件，在页面切换或者触发组件的其他行为时，组件就会被卸载掉，导致它丢失其中所有已变化的状态，当这个组件再一次被显示时，会创建一个只带有初始状态的新实例。当使用了 Activation 后，就可以避免组件重新渲染时的状态丢失。

  - AliceScope 组件：一般在根组件中使用，包裹整个应用。与 react-router 或 react-redux 配合使用时，建议将 ==AliveScope== 放置在 ==Router== 或 ==Provider== 内部
  - keepAlive 组件：包裹需要保持状态的组件，用于缓存组件的实例，以便在组件被销毁后可以保留其状态和数据。通过使用 KeepAlive，可以避免在组件频繁切换时重复渲染和重新初始化组件的开销，提高应用的性能和用户体验。注：当有多个 KeepAlive 组件时，需要给每个<KeepAlive cacheKey="UNIQUE" />声明全局唯一且不变的 cacheKey 属性。

  ```
    //Demo 组件
    const Demo = () => {
      const [num,setNum] = useState();

      const handleClick = () =>{
        setNUm(num+1)
      }

      return(
        <>
          //开启缓存后，会保存状态 num ，避免重新渲染时，状态丢失
          //即当 num 加到10时，点击切换按钮，num的值依然是10，不会被清空
          <span> NUM: { num }</span>
          <button onClick={ handleClick }>+ 1</button>
        </>
      )

    }

    //根组件
    const App = () =>{
      const [isKeep,setIsKeep] = useState(true)
      return(
        <AliveScope>
          <button onClick={ () => setIskeep(!isKeep) }> 切换 </button>
          <hr />
          普通 Demo 组件
          { isKeep && <Demo /> }
          <hr />
          { isKeep && (
            开启 KeepAlive 缓存 Demo 组件
            <KeepAlive>
              <Demo />
            </KeepAlive>
          )}
        </AliveScope>
      )
    }

  ```

- 手动控制缓存：useAliveController 获取控制函数

  - drop(name): "卸载"仅可用于缓存状态下的节点，如果节点没有被缓存但需要清空缓存状态，请使用 “刷新” 控制,按 name 卸载缓存状态下的 <KeepAlive> 节点，name 可选类型为 String 或 RegExp，注意，仅卸载命中 <KeepAlive> 的第一层内容，不会卸载 <KeepAlive> 中嵌套的、未命中的 <KeepAlive>

  - dropScope(name): "卸载"仅可用于缓存状态下的节点，如果节点没有被缓存但需要清空缓存状态，请使用 “刷新” 控制,按 name 卸载缓存状态下的 <KeepAlive> 节点，name 可选类型为 String 或 RegExp，将卸载命中 <KeepAlive> 的所有内容，包括 <KeepAlive> 中嵌套的所有 <KeepAlive>

  - refresh(name): 按 name 刷新缓存状态下的 <KeepAlive> 节点，name 可选类型为 String 或 RegExp，注意，仅刷新命中 <KeepAlive> 的第一层内容，不会刷新 <KeepAlive> 中嵌套的、未命中的 <KeepAlive>

  - refreshScope(name): 按 name 刷新缓存状态下的 <KeepAlive> 节点，name 可选类型为 String 或 RegExp，将刷新命中 <KeepAlive> 的所有内容，包括 <KeepAlive> 中嵌套的所有 <KeepAlive>

  - clear(): 将清空所有缓存中的 KeepAlive

  - getCachingNodes(): 获取所有缓存中的节点
