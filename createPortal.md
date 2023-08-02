# 学习记录 8-2

### createPortal

- createPortal 用于将子元素渲染到父组件之外的 DOM 节点上，将子元素渲染到任意位置，不限于组件树的层级关系。通常用于在应用程序的根节点之外创建弹窗、模态框等。
- createPortal(children, container，key)接收三个参数，第一个参数是 React 可以渲染的任何内容，如 JSX 片段、字符串或数字，以及这些内容构成的数组；第二个参数为应用中存在的节点；第三个参数为可选参数，用作 portal key 的独特字符串或数字。
- portal 只改变 DOM 节点的所处位置。而渲染至 portal 的 JSX 的行为表现与作为 React 组件的子节点一致。该子节点可以访问由父节点树提供的 context 对象、事件将从子节点依循 React 树冒泡到父节点。

```
const Dialog =  ({content,close,show}) =>{
  return (
    show && (
      <div>
        <div >{content}</div>
        <div onClick={() => close()} >
          关闭
        </div>
      </div>
    )
  )
}

const Demo = (props) => {
  const [content, setContent] = useState(new Date().toString())
  const [show, setShow] = useState(false)

  const handleClick = () =>{
    setContent(new Date().toString())
    setShow(true)
  }

  return (
    <div>
      //创建一个传送门，将子元素渲染到body节点下
      {createPortal(
        <Dialog
          show={show}
          content={content}
          close={() => { setShow(false) }}
        >
          {content}
        </Dialog>,
        document.body
      )}
      <button onClick={ handleClick }  >
        弹出消息
      </button>
    </div>
  )
}
```

#### 拓展

- 引用、浅拷贝及深拷贝
  - 引用：对于复杂数据结构，为了节省存储资源，符号 “=” 其实并不是将值赋给新建的变量，而是做了一个地址引用，使其指向原来存储在堆中的数据的地址，此时 testObj 与 secObj 都指向同一个地址，因此在修改 secObj 的数据内容时，即是对其指向的原有数据进行修改。
  - 浅拷贝：将原对象或原数组的引用直接赋给新对象，新数组，新对象／数组只是原对象的一个引用。如 Object.assign()，Object.freeze()，数组赋值，解构赋值
  - 深拷贝：创建一个新的对象和数组，将原对象的各项属性的“值”（数组的所有元素）拷贝过来，是“值”而不是“引用”。如数组的 循环赋值，slice()，concat()，map()，扩展运算符配合字面量实现[...arr]，JSON.stngify 等
- 递归就是一次次的调用自身，当满足某个条件时，递归结束
  ```
  const recursive(i){
    if(i < 10){
        return recursive(i + 1)
    }
    return i;
  }
  recursive(0);
  ```
- 回调：作为参数传递给另外一个函数的函数，它在主体函数执行过程中的特定条件下执行

  ```
  const callback = (num) =>{
    return num * num;
  }

  const calc = (callback,num) =>{
    return callback(num)
  }

  calc(callback, 10)

  ```
