# 学习记录 7-28

### useReducer+Context 实现小型的状态集中式管理

1. 调用 createContext 来创建一个全局的上下文
2. 调用 useReducer 以创建一个用于管理状态的 reducer
3. 用上下文 provider 包裹需要传递数据的组件，来为里面所有的组件指定一个 context 的值，即 { state,dispatch }
4. 子组件通过 state 获取数据，通过 dispatch 分发一个带有 type 属性的对象，从而实现操作数据更新

```
const init = 0;

//创建一个全局的 Context 上下文
const GlobalContext = createContext();

const ReducerContextProvider = (props) => {

  const reducer = (preState = init, action) => {
    const { type, data } = action;
    switch (type) {
      case "increment":
        return preState + data;
      case "decrement":
        return preState - data;
      default:
        return preState;
    }
  };

  //创建一个用于管理全局状态的 reducer
  const [state, dispatch] = useReducer(reducer, 0)

  return (
    //通过GlobalContext的 Provider 的上下文 context 传递 reducer
    <GlobalContext.Provider value={{ state, dispatch }}>
      {props.children}
    </GlobalContext.Provider>
  );
};

const useReducerContext = () => {
  return useContext(GlobalContext);
}

const ShowDemo = () => {
  //通过useReducerContext获取传递的上下文中的 state 和 dispatch
  const { state, dispatch } = useReducerContext();
  const handleIncrease = (params) => {
    //通过dispatch分发一个action（一个带有特殊属性 type 的对象），实现状态的更新
    dispatch({ type: "increment", data: 2 });
  };
  const handleDecrease = (params) => {
    dispatch({ type: "decrement", data: 1 });
  };

  return (
    <>
      <span>count: {state}</span>
      <div>
        <button onClick={handleIncrease}> increase </button>
        <button onClick={handleDecrease}> decrease </button>
      </div>
    </>
  );
};

const MainDemo = () => {
  return (
    //通过 ReducerContextProvider 组件的 children 属性传入子组件
    <ReducerContextProvider>
      <ShowDemo />
    </ReducerContextProvider>
  );
};
```

### Dockerfile

- Dockerfile 是用于构建 Docker 镜像的文本文件，其中包含了一系列指令来定义镜像的构建过程。
  - FROM：指定基础镜像，用于构建当前镜像。FROM 命令必须是 Dockerfile 的首个命令
  - ENV：设置环境变量，可以在后续的指令中使用
  - COPY：将从构建上下文目录中 <源路径> 的文件/目录复制到新的一层的镜像内的 <目标路径> 位置
  - WORKDIR：设置工作目录，后续的指令将在该目录下执行。
  - EXPOSE：声明镜像运行时所监听的端口
  - RUN：在镜像中执行命令，可以用来安装软件包、运行脚本等
  - CMD：设置容器创建是执行的默认命令。支持三种格式，默认会被 docker run 指定的参数覆盖。每个容器只能执行一条 CMD 命令。执行的命令和参数当指定多个时，只有最后一个起效。
    ```
    CMD [“executable”, “参数1”,“参数2”]：使用 exec 方式执行，推荐。
    CMD command 参数1 参数2：shell 方式执行。
    CMD [“参数1”,“参数2”]：提供给 ENTRYPOINT 做默认参数。
    ```
  - ENTRYPOINT：指定容器的“入口”。支持两种格式，默认不会被 docker run 指定的参数覆盖。可以指定 --entrypoint 参数去覆盖。每个 Dockerfile 中只能有一个 ENTRYPOINT，当指定多个时，只有最后一个起效。
    ```
    ENTRYPOINT [“executable”, “参数1”,“参数2”]：使用 exec 方式执行,推荐
    ENTRYPOINT command 参数1 参数2：shell 方式执行。shell 方式下不能接受 CMD 的参数。
    ```
  ```

  ```

### CI/CD

- CI/CD 是持续集成（Continuous Integration）和持续交付/持续部署（Continuous Delivery/Continuous Deployment）的缩写。CI/CD 的目标是实现软件开发流程的自动化和持续性，以提高开发团队的效率和软件的质量。通过频繁地集成代码、自动化构建和测试、自动化部署等措施，可以减少手动操作的错误和延迟，加快软件的交付速度，提高团队的协作效率，降低软件开发和交付的风险。

  1. CI：是指开发团队将代码频繁地集成到共享的代码仓库中，并通过自动化的构建和测试流程来验证代码的质量。这样可以确保团队成员的代码能够及时地与其他人的代码进行集成，减少冲突和问题的发生
  2. CD：是指将经过集成和测试的代码自动发布到生产环境中。持续交付是指将代码交付到生产环境的过程自动化，但仍然需要人工触发。而持续部署则是指将代码自动部署到生产环境中，完全自动化，无需人工干预
