# 学习记录 8-8

## 高阶组件（HOC）

- 高阶组件（HOC）是 React 中用于复用组件逻辑的一种高级技巧。HOC 自身不是 React API 的一部分，它是一种基于 React 的组合特性而形成的设计模式。高阶组件接受一个组件作为参数，返回一个新的组件。

  1. 增强 props：通过承接上层的 props，添加到自己的 props中，以此达到强化组件的作用
  2. 条件渲染：需要一个条件来控制是否渲染，通过条件来进行触发，而不是操作组件内部控制渲染，通常运用在路由加载页面、懒加载、登录鉴权上。

```
** 增强 props **

//Left 组件
const Left = (props) => {
  const { children, msg, tip } = props;
  return (
    <div style={{ width: "400px", height: "200px", backgroundColor: "skyblue" }} >
      <h2>{children}组件</h2>
      <h3>tips：{msg ? msg : "~~~~~~~"}</h3>
      <h4>tips：{tip ? tip : "~~~~~~~"}</h4>
    </div>
  );
};

//Right 组件
const Right = (props) => {
  const { children, msg, tip } = props;
  return (
    <div style={{ width: "400px", height: "200px", backgroundColor: "orange" }}>
      <h2>{children}组件</h2>
      <h3>tips：{msg ? msg : "~~~~~~~"}</h3>
      <h4>tips：{tip ? tip : "~~~~~~~"}</h4>
    </div>
  );
};

//高阶组件，劫持 Left 和 Right 组件，在高阶组件内给传入的组件部新增props
const ComponentHijack = (Component) => {
  const NewComponent = (props) => {
    const [token, setToken] = useState("133233");
    return <Component {...props} token={token} tip="Direction" />;
  };
  return NewComponent;
};

const LeftComponent = ComponentHijack(Left);
const RightComponent = ComponentHijack(Right);

const Demo = () => {
  return (
    <div style={{ display: "flex" }}>
      <Left>Left</Left>
      <Right>Right</Right>
      <hr />
      <LeftComponent msg="HOC Left">Left(HOC)</LeftComponent>
      <RightComponent msg="HOC Right">Right(HOC)</RightComponent>
    </div>
  );
};
export default HOC;
```

