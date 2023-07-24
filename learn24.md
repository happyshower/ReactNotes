# 学习记录 7-24

## react router (v6)
- 使用路由表的方式进行路由注册
  useRoutes()，该hook接受一个参数，该参数为由组件对象组成的数组，其返回值要么是可用于渲染路由树的有效 React 元素，要么null是不匹配的元素
  ```
  const list = [
    {
      path:"/home",
      element: <Home />   
    }
  ]

  const  Home = () =>{
    return <div> Home </div>
  } 

  const  App = () =>{
    const elements = useRoutes(list)  

    return <div>{ element }</div>
  }  
  ```

- 路由传参的三种方式：

1. 路由参数（Route Parameters）： 使用路由路径中的占位符(:)来传递参数，参数值可以从 useParams 钩子函数中获取

```
const App = () => {
  return (
    <Router>
      <Route path="/user/:id" element={<User />} />     //通过占位符 : 在路由中注册
    </Router>
  );
}

const User = () => {

  //useParams()用于获取路由参数的值并返回一个对象，该对象包含了当前路由路径中定义的参数及其对应的值。
  const { id } = useParams();

  return <div>User ID: {id}</div>;
}

const Home = () => {
  return (
    <div>
      <Link to="/user/123">User 123</Link>     //通过路径后接 /xxx 的形式进行参数传递
    </div>
  );
}
```

2. 查询参数（Query Parameters）： 使用查询字符串来传递参数，可以通过 useLocation 钩子函数和 URLSearchParams 对象来获取和解析参数值

```
const App = () => {
  return (
    <Router>
      <Route path="/user" element={<User />} />       //正常注册路由即可
    </Router>
  );
}

const User = () => {
  //useLocation() 用于获取当前页面的 location 并返回一个包含当前页面 location 的对象
  //该函数返回一个对象，如{ pathname, search, state }
  const { search } = useLocation();

  //URLSearchParams 用于解析和操作 URL 查询参数,返回一个URLSearchParams实例，如{id: 123}
  const params = new URLSearchParams(search);
  const id = params.get('id');    //通过get(name) 的方式获取该实例对象中传递的参数

  return <div>User ID: {id}</div>;
}

const Home = () => {
  return (
    <div>
      <Link to="/user?id=123">User 123</Link>    //通过在路径后拼接字符串的方式来进行参数的传递
    </div>
  );
}
```

3. 状态参数（State Parameters）： 使用路由状态来传递参数，可以通过 useLocation 钩子函数的 state 属性来获取参数值

```
const App = () => {
  return (
    <Router>
      <Route path="/user" element={<User />} />     //正常注册路由即可
    </Router>
  );
}

const User = () => {

  //解构赋值获取该对象中的 state 参数
  const { state } = useLocation();

  return <div>User ID: {state.id}</div>;
}

const Home = () => {
  return (
    <div>
      //通过给to传递一个对象，第一个参数是pathname，即跳转的路径，第二个参数是一个state对象，在该对象中传递参数
      <Link to={{ pathname: "/user", state: { id: 123 } }}>User 123</Link>    
    </div>
  );
}
```

## ts 中箭头函数泛型的使用

1. 在箭头函数的参数列表中定义泛型参数

```
const fn = <T>(arg: T): T => {
  //todo
};
```

2. 在箭头函数的返回值类型中使用泛型参数

```
const fn = <T>(arg: T): T => {
  //todo
} as <T>(arg: T) => T;
```

3. 使用类型别名或接口来定义箭头函数的类型，并在其中使用泛型参数：

```
type tsFn = <T>(arg: T) => T;

const fn: tsFn = (arg) => {
  //todo
};
```

4. 在箭头函数的上下文中使用泛型参数

```
class MyClass<T> {
  fn: (arg: T) => T = (arg) => {
    //todo
  };
}
```
