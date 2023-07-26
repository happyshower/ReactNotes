# 学习记录 7-26

### forwardRef

- forwardRef()：允许组件使用 ref 将一个 DOM 节点暴露给父组件
- forwardRef( render ) 接收一个参数，该参数为一个函数，是组件的渲染函数，该函数返回的 JSX 作为组件的返回值，且该组件能接受 ref 属性。
- render( props，ref ) 函数接收两个参数，第一个参数 props 为父组件传递过来的参数，第二个参数是父组件传递过来的 ref 属性。该函数返回一个可以在 JSX 中渲染的 React 组件。

  ```
  //该组件接收父组件传递过来的 props 和 ref 属性
  const InputDemo = (props, ref) => {
    const { num } = props

    return <>
      <span>{ num }</span>
      <input ref={ ref } />;
    </>
  };

  const ExportInput = forwardRef(InputDemo);  //返回一个可以接收 ref 属性的组件

  const Demo = () => {
    const [num,setNum] = useState(0)
    const input = useRef();

    const handlefocus = () => {
      input.current.focus();
    };

    return (
      <div>
        子组件
        <ExportInput num={num} ref={input} />     //传递props 和 ref 属性
        <hr />
        父组件
        <button onClick={handlefocus}>focus</button>
      </div>
    );
  };
  ```

### memo

- memo()：允许组件在 props 没有改变的情况下跳过重新渲染，获得该组件的一个记忆化版本从而提高性能
- memo(Component,arePropsEqual) 接收两个参数，第一个参数 Componet 为一个组件，该组件为一个有效的 React 组件，如函数式组件和 forwardRef 组件。第二个参数 arePropsEqual 为可选参数，该参数为一个函数，该函数接收两个参数，第一个参数为组件旧的 props，第二个参数为组件新的 props。通常情况下，不需要指定此函数，React 将使用 Object.is 比较每个 prop。

  ```
  const Demo = (props: any) => {
    const { num } = props;
    const colorList = ["skyblue", "orange", "gray", "lightgreen", "pink"];
    const color = colorList[Math.floor(Math.random() * 5)];

    return (
      <div style={{ backgroundColor: `${color}` }}>  //组件重新渲染时，随机切换背景色
        <h4>Demo</h4>
        <p>Num from Home: {num ? num : 0}</p>
      </div>
    );
  };

  const MemoDemo = memo(Demo);     //使用 memo() 缓存Demo组件

  export const Home = () => {
    const [num, setNum] = useState(0);
    const [toggle, setToggle] = useState(false);

    const handleClick = () => {
      setNum(num + 1);
    };
    const handleToggle = () => {
      setToggle(!toggle);
    };

    return (
      <div>
        <h3>Home</h3>
        <div>Num: {num}</div>
        <button onClick={handleClick}>+ 1</button>
        <br />
        //当 toggle 为 false 时，不会向子组件传递 props ，此时被缓存的子组件将会跳过重新渲染
        //当 toggle 为 true 时，开启prop传值，此时点击+1时，被缓存的子组件也会被重新渲染
        <button onClick={handleToggle}>
          {toggle ? "关闭props传值" : "开启props传值"}
        </button>
        <hr />
        <MemoDemo num={toggle && num} />
      </div>
    );
  };
  ```

- 提高性能的其他方法：

* 将该代码以下面两种方式实现跳过组件的重新渲染，提高性能。

  ```
  const ExpensiveTree = () => {
    let i = 0;
    while (i < 100000) {
      i++;
    }
    return <p>Slow Component Tree.</p>;
  }

  export const Home = () => {
    let [color, setColor] = useState('red');

    return (
      <div>
        <input value={color} onChange={(e) => setColor(e.target.value)} />
        <p style={{ color }}>Hello, world!</p>
        <ExpensiveTree />
      </div>
    );
  };
  ```

* state 下移：让组件的状态精简，尽可能把逻辑和状态放在相关的组件内，而不要上浮到父组件，避免因为一个地方的状态变更，导致子组件的全量更新渲染。

  ```
  const Form = () => {
    let [color, setColor] = useState('red');
    return (
      <>
        <input value={color} onChange={(e) => setColor(e.target.value)} />
        <p style={{ color }}>Hello, world!</p>
      </>
    );
  }

  const App = () => {
    return (
      <>
        <Form />
        <ExpensiveTree />
      </>
    );
  }
  ```

* 子组件作为 children 传递

  - 组件原子化，经常状态变更的组件抽离出去作为包裹组件，其内部状态变更不影响父组件及其兄弟组件
  - 子组件作为 children 传入，由于父组件不会重新渲染，子组件不变，所以在重新渲染的包裹组件里，children 不发生变化

  ```
  const App = () => {
   return (
     <ColorPicker>
       <p>Hello, world!</p>
       <ExpensiveTree />      //将组件作为标签体内容传递给 ColorPicker 组件
     </ColorPicker>
   );
  }

  const ColorPicker = ({ children }) => {
   let [color, setColor] = useState("red");

   return (
     <div style={{ color }}>
       <input value={color} onChange={(e) => setColor(e.target.value)} />
       {children}        //通过组件的props.children属性获取传递过来的组件
     </div>
   );
  }
  ```

* 子组件作为 props 传递

  - 原理基本同children传递一样，区别在于children传递的子组件是一个整体，不能在包裹组件里拆分，而props传递可以

  ```
  const propsComponent = <ExpensiveTree />  
  const App = () => {
   return (
     <ColorPicker comp ={propsComponent}>
       <p>Hello, world!</p>
     </ColorPicker>
   );
  }

  const ColorPicker = ({ comp }) => {
   let [color, setColor] = useState("red");

   return (
     <div style={{ color }}>
       <input value={color} onChange={(e) => setColor(e.target.value)} />
       {comp}     
     </div>
   );
  }
  ```
