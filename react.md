6.20
今天学习了解了react的基础语法,函数式组件、类组件，react的生命周期,受控组件和非受控组件
1.使用箭头函数的方式
  saveInput = () =>{}
  <input type="text" onChange={this.saveInput}/>
2.通过bind()绑定this指向（或者在构造器中定义this.saveInput = this.saveInput.bind(this)）
  saveInput() {}
  <input type="text" onChange={this.saveInput.bind(this)}/>
3.通过内联箭头函数的方式
  saveInput() {}
  <input type="text" onChange={()=>this.saveInput()}/>

父子组件间传值(父组件Parent,子组件Son)
1.父组件向子组件传值，可以通过props的方式，如在父组件Parent中进行传值,<Son {...state} />,在子组件中可以通过this.props来取值
2.子组件向父组件传值，将父组件的改变状态的方法传入子组件的props,绑定给子组件中的某些触发事件譬如按钮的点击，得到子组件的值或状态或动作，再调用父组件的方法得到子组件中的值。

受控组件和非受控组件
1.受控组件,state属性和表单元素的value值进行绑定，通过onChange并结合setState()动态更改state中的数据
2.非受控组件，可以通过ref打标识，通过this.refs.input.value获取节点数据


6.21
今天学习react的ref的三种写法，React的事件处理是基于原生的js进行封装，为了更好的兼容性，其次都通过事件委托处理的，高阶函数及函数柯里化。
ref的三种写法:
--字符串形式(ref="input1")，该用法即将废弃
--回调函数形式
	a:内联函数形式：会调用两次，第一次清空旧的Ref(null)，第二次传入当前节点； ref = {currentNode => this.input1 = currentNode}
	b:类绑定形式：ref = {this.saveInput}(saveInput为回调函数)
--createRef Api形式，React.createRef()调用后返回一个容器，该容器可以存储被ref标识的节点且只能存储一个({current:input1}) 
	myRef = createRef()
	<input ref={this.myRef} />
	取值：this.myRef.current.value

高阶函数：若A函数，接受的参数是一个函数，或调用的返回值是一个函数，A可以称之为高阶函数
跨域配置代理：
1.在package.json中配置"proxy":"http://localhost:5000"，该方法只能配置一个代理
2.在scr目录下添加setupProxy.js文件，使用Common.js引入,可配置多个代理，代码如下：
const {createProxyMiddleware} = require('http-proxy-middleware')
module.exports = function(app){
    app.use(
        createProxyMiddleware('/api',{
            target:'https://mock.apifox.cn/m1/2477671-0-default',
            changeOrigin:true,
            pathRewrite:{'^/api':''}
        })，
	createProxyMiddleware('/api1',{
            target:'https://mock.apifox.cn/m1/2477671-0-default',
            changeOrigin:true,
            pathRewrite:{'^/api1':''}
        })
    )
}

6.22
V5版本的React Router

NavLink与NavLink的二次封装：
1.Navlink可以实现路由连接的高亮，在点击该链接时动态添加一个类active(className="active")，除此之位可以通过activeClassName指定样式名
2.二次封装的NavLink，其标签体内容是一个特殊的标签属性children，通过this.props.children可以获取标签体的内容
<MyNavLink to="/home"> Home <MyNavLink>
class MyNavLink extends Component{
   render(){
      <NavLink {...this.props} />  
   }
}

<Switch></Switch>
通常情况下，path和component是一一对应的关系，当Route存在一个path对应多个component时，当匹配完第一个路由时，还会继续往下匹配，使用Switch可以提高路由的匹配效率(单一匹配)

多级路径刷新样式丢失问题，可以在public/index.html引入样式时 ./  ==>  /  或者   ./ ==> %PUBLIC_URL%  或者使用HashRouter

v5版本及以下Router默认使用模糊匹配，输入的路径必须包含匹配的路径，且顺序必须一致，可以在Route中使用exact开启严格匹配；

嵌套路由
路由组件传参
1.params参数，路由链接携带参数，注册路由并声明接受，通过this.props.match.params接收参数
    <link to={`/home/${id}`} >XXX</Link>
    <Route path="/home/:id" component={Home} />
2.search参数，路由链接携带参数，正常注册路由即可，通过this.props.location.search接收参数，获取到的search是urlencodeed编码字符串，需要借助query-string(npm i query-string -D) 的parse方法进行解析或者直接导入qs
    <Link to={`/home/message/details?id=${id}&title=${title}`}>xxxx</Link>
3.state传参，路由链接携带参数，正常注册路由即可，通过this.props.location.state接收参数
<Link to={{pathname:'/home/message/details',state:{id,title}}>xxxx</Link>

withRouter(组件名)可以加工组件，让一般组件具备路由组件所特有的API，其返回值是一个新组件使用




6.23
redux
reducer的本质是一个纯函数，其主要作用是初始化状态和加工状态，接收两个参数previousState，action，此外，redux只负责状态的管理，至于状态的改变驱动视图的改变
store.getState()
store.dispatch({type:'',data:value}) 或者   利用action定义函数并暴露(如incerement(data))，使用store.dispatch(inceremrnt(data))
store.subscribe(()=>{this.setState({})})   监测store中状态的改变，一旦发生改变利用this.setState({})调用render()重新渲染视图

同步action和异步action
同步action，就是指action的值为Object类型的一般对象
异步action，就是指action的值为函数，且异步action中一般会调用同步action，开启异步action需要安装redux-thunk,然后在store中引入applyMiddleware，即createStore(reducerName,applyMiddleware(thunk))

实现各个组件间数据共享，可以在store中合并各个组件的reduce,如combineReducers({count:countReducer,person:personReducer})，通过this.props取值即可


react-redux
容器组件包含UI组件，UI组件只负责页面的呈现、交互，容器组件负责和redux通信，将结果交给UI组件。容器组件可以随意使用redux的api，且容器组件会给UI组件通过props传递redux中所保存的状态和操作状态的方法
通过connect(mapStateToProps,mapDispatchToProps)(UI)创建并返回一个容器组件，且在容器组件的上一级传递store参数
----<CountContainer store = {store} />

connect(mapStateToProps，mapDispatchToProps)接收两个参数，参数必须为函数，且其返回值必须为对象，第一个参数向props传递redux的状态('state')，第二个参数向props传递操作状态的方法
----mapStateToProps = (state) => ({count:state})
----mapDispatchToProps = (dispatch) => ({increment:(number)=> dispatch({incrementAction(number)})})  //引入action中的incrementAction(data)
----简写方式：
	connect(
	    state=>({count:state.num}),
	    {increment:incrementAction}
	)

connect()的返回值也是一个函数，其返回的函数接收一个参数，即UI组件
----export default connect(mapStateToProps,mapDispatchToProps)(CountUI) 


6.25
setState扩展
setState()是同步到的，但其调用完后引起的后续动作是异步的，即React状态的更新是异步的
setState的两个写法：
1.对象式setState：setState(stateChange,[callback])，stateChange为状态改变对象，该对象可以体现出对象的的更改，callback是可选的回调函数，在状态更新完毕和页面也更新后才被调用，即在render()调用后才被调用
2.函数式setState：setState(updater,[callback])，updater为返回的stateChange对象的函数，该对象可以体现出对象的的更改，callback是可选的回调函数，在状态更新完毕和页面也更新后才被调用，即在render()调用后才被调用
this.setState((state,props)=>{},()=>{})

V6版本的React Router
与React Router5.xx相比：
1.内置组件的改变：
----移除了了<Switch />组件，新增了<Routes />，<Route />必须包裹在<Routes></Routes>内
----废弃了<Redirect />组件，新增了<Navigate />组件，该组件默认的路由跳转方式是push，可以使用replace={true}开启replace模式；只要该组件被渲染，势必会引起路径的修改，视图的切换
2.语法的改变：
----由<Route path='/home' component={Home} />，变为<Route path='/home' element={<Home />} />
----<NavLink />自定义组件高亮，废除了activeClassNmae属性,由<NavLink activeClassNmae="xxx" classNmae="xxx active" />，变为<NavLink classNmae={({isActive}) => isActive ? "xxx active" : "xxx"} />
3.路由表
----通过useRoutes()使用路由表,但需要通过<Outlet />组件来渲染子路由，const element = useRoutes([{path:'/home',element:<Home />}])，
4.嵌套路由
----
5.路由传参
----params参数：<Link to={`detail/${id}/${title}`}> XXX </Link>
		路由：detail/:id/:title
		在函数式组件中可以使用useParams()获取params参数，如const {id,title} = useParams()
----search参数：<Link to={`detail?id=${id}&title=${title}`}> XXX </Link>
		路由：detail（正常注册即可）
		在函数式组件中可以使用useSearchParams()获取search参数，如const [search,setSearch] = useSearchParams()，const id = search.get('id')，const title = search.get('title')
----state参数：<Link to="detail" state={{id,title}}> XXX </Link>
		路由：detail（正常注册即可）
		在函数式组件中可以使用useLocation()获取state参数，如const {state:{id,title}} = useLocation()
6.编程式导航
----使用useNavigate(to,[{replace:boolean,state:{}}]),如 const navigate= useNavigate('/home',{state:{id,title}})
----前进navigate(1)，后退navigate(-1)
7.useInRouterContext Hook：如果组件处于<Router>的上下文中呈现，则useInRouterContext()返回ture，否则返回false
8.useNavigationType Hook：返回当前的导航类型（用户是如何来到当前页面的），返回值POP（指在浏览器中直接打开了这个路由组件）、PUSH、REPLACE
9.useOutLet Hook：用来呈现当前组件中渲染的嵌套路由，嵌套路由没有挂载则为null，否则展示嵌套的路由对象
10.useResolvedPath Hook：给定一个URL值，解析其中的：path、search、hash值




6.27
React Hooks新特性，可以在函数式组件中使用state以及其他React特性
1.State Hook：让函数式组件也可以使用state状态，并进行状态数据的读写操作,useState()第一次初始化指定的值会在内部缓存
----const [xxx,setXxx] = useState(initValue)，initValue为初始化的值；const [num,setNum] = useState(0)
----改变num的值，setNum(num+1) 或者  setNum(num=>num+1)
2.Effect Hook：可以在函数式组件中执行副作用操作，用于模拟类组件中的生命周期钩子（副作用操作：Ajax请求，定时器，手动更改真实dom，设置订阅）
----useEffect(()=>{ 
	//在此可以执行带副作用的操作
        return ()=>{
	    //在组件卸载前执行
	} 
    })
3.Ref Hook：可以在函数式组件中存储/查找组件内的标签或任意数据，功能与createRef()一样
----const myRef = useRef()

组件间通讯总结：
1.props
2.context
3.redux

向组件内部动态传入带内容的标签结构(<A> <B/> </A>)
----使用children props，通过组件标签体传入结构，如 <A> <B/> </A>,{this.props.children}（注：B组件获取不到A组件内的数据）
----使用render props，通过组件标签属性传入结构，如  <A render={(data)=> <B data={data} /> }  />,  {this.props.render(data)}

组件优化
1.使用shouldComponentUpdate(nextProps,nextState)这个生命周期来让子组件不更新，需要手动给每个子组件去添加判断条件（不推荐）
2.使用PureComponent来代替Component来进行组建的优化


高阶组件(HOC)
高阶组件就是一个函数，这个函数接收一个组件作为参数，通过将组件包装在容器组件中来组成新的组件，并返回一个新的组件。
1.增强props：在不改变原来的代码结构的基础上，对每一个组件使用之前进行劫持，给每一个组件添加一个新的属性



7.4
今天学习了开发功能的流程，在master分支的基础下，新建一条功能分支，然后在实现完功能代码后，通过新建的分支将代码提交并推送，此时在gitlab上可以看到新建的分支，接着可以在issues新建一个里程碑milestone，再新建一个issues，然后在Merge Request中创建一个PR，并将PR和对应的issues进行关联，然后再将issues和PR与milestone进行关联，这样可以起到一个版本控制的效果。PR和issues的关系，就像issues是一个问题，PR是一个解决问题的工具或者是平台，在这个平台中，我们可以看到用户提交的修改，管理者可以评审该用户提交的修改并给出优化的建议，从而达到降低bug出现的可能，从而提高开发效率和系统的稳定性，当该功能完善并获得认可后，在gitlab上手动将代码进行合并，至此一个PR就结束了，相对的issues也会自动关闭，gitlab上该分支也会消失，当要开启新的功能时，重新开启一个分支，重复实施以上步骤。

7.5
今天进行了考核项目底部导航栏的编写以及其路由跳转的实现，在这个过程中对于路由的跳转方式也更加清晰，一开始时采用Link搭配Routes进行导航的跳转，但是这种导航方式一个Link对应一个Route且当导航比较多的时候就需要编写大量的Link和Route，也不便于路由的调整和修改，后来使用Hooks的useRoutes()、useNavigate()，配合路由表来循环渲染底部导航栏来实现编程式路由导航。
学习了TS的一些基础知识
1.TS基本类型
boolean 、number、string、Array、Tuple（元组）、enum（枚举）、any（任意值）、void（空值）、null、undefined、never
- Array定义数组的两种方法：list：Array<number>、list：number[]
- 元组定义：元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同，当访问一直索引的元素时会得到正确的类型，但当访问越界元素时，会使用联合类型代替。
   如：let x: [string, number]  ，  x = [“hello”,10]
- enum枚举：使用枚举类型可以为一组数值赋予友好的名字，
       如：enum Color {Red, Green, Bule}，let c: Color = Color.Green
- never类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是never的子类型或可以赋值给never类型（除了never本身之外）

2.类型断言
- let strLength: number = (<string>someValue).length;
- let strLength: number = (someValue as string).length;  可在react中使用

3.接口
接口的作用就是为这些类型命名和为代码或第三方代码定义契约。
- 可选属性：接口里面的属性不全都是必须的
	interface SquareConfig {
  		color?: string;   //在属性名后接 ? 即为可选属性
  		width?: number;
	}
- 只读属性：一些对象属性只能在对象刚刚创建的时候修改其值	
	interface Point {
    		readonly x: number;      //添加readonly即为可读属性
    		readonly y: number;
	}
- 额外属性检查：当将它们赋值给变量或作为参数传递的时候，如果一个对象字面量存在任何“目标类型”不包含的属性时，会报错
       如：定义的接口如上SquareConfig所示，当传入字面量参数{height: 10, width: 100}作为	变量	时,height属性并不存在于接口所定义的属性时，会引发错误。
- 函数类型
	interface SearchFunc {  //采用了签名的形式
  		(source: string, subString: string): boolean;   // 传入的参数类型：返回的数据类型
	}
- 可索引类型（签名）
       可索引类型具有一个索引签名，它描述了对象索引的类型，还有相应的索引返回值类型
       如：interface IStringArray{.   
		[index: number] : string.   //字符串数组
	}
- 继承接口
      接口可以相互继承且可以继承多个接口，从而灵活将接口分割到可重用模块
- 接口合并
      多次声明的同名 interface 会进行声明合并

7.6
今天完成了考核项目底部导航栏的路由表优化和rem适配。进行了Account页面的编写，构思页面内组件的复用，通过编写组件数组循环渲染，然后props传参的形式来实现组件的复用。学习了解了react官网上井字棋的逻辑和函数组件封装思想，在顶层组件管理数据，通过props参数传递参数和回调函数，在底层组件也可以操作顶层组件的数据。
组件复用的拓展：render props和HOC 
1.在不确定子组件的前提下，可通过render props动态传入子组件，也可通过render props 将子组件内部的state数据暴露出去
<A render={(data)=>{<B data={data} />}}/>
const A = () =>{ return <div> this.props.render(this.state.data) </div> }
2.HOC高阶组件
高阶组件接收一个组件参数，使用组合的方式返会一个新的组件

7.7
学习了解了类组件的生命周期，在新的版本中componentWillMount、componentWillUpdate、componentWiiReceiveprops即将废弃，新增了getDerivedStateFromProps和getSnapshotBeforeUpdate。getDerivedStateFromProps(props，state)接收两个参数，返回null或者一个状态对象，props为该组件接收的props，state是组件自身的状态state；getDerivedStateFromProps(preProps,preState),preProps为上一次的props，preState上一次的state，该方法可在组件更新之前从DOM获取信息，返回null或者snapshot(快照值)，该值可传递给componentDidUpdate，
学习了解了setState()、Hooks的useState()
----setState()，触发组件重新调用render()更新数据和页面，它不是绝对的异步和同步
同步：setTimeout、setInterval、在原生事件中调用（通过绑定dom绑定事件的方式实现）
异步：在合成事件中、生命周期中（合成事件是模拟原生DOM事件所有能力的一个事件对象）
----在没有Hooks之前，函数式组件中无法使用state进行状态的管理，在页面重新渲染时，会重新初始化函数式组件的局部变量，只能做页面的UI组件，在16.8版本后，可以使用Hooks useState()来为函数式组件添加状态数据，使用useState()会为该组件添加一个state状态和标明该组件state的一个标识符（索引值），且该状态在页面再次重新渲染时会被缓存机制将上一次的数据保存下来，不会被再次初始化。该方法会返回一个数组，数组包含状态变量和操作状态变量的方法。useState()可以接收一个参数，该参数可以为数字、字符串等，也可以接收一个函数，初始值是在组件挂载时执行的，每次组件重新渲染时都会调用，但当参数为函数时只会执行一次，重新渲染不会再被调用，这是useState的惰性初始化。


