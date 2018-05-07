
##一.搭建react环境使用的是Facebook维护的create-react-app脚手架 
1. 使用命令`npm install -g create-react-app`生成环境
2. 搭建好之后`webpack`的默认配置文件都是隐藏的，如果想自定义配置文件要运行`npm run eject`
3. 运行`npm start `启动成程序
4. 运行`npm run build`命令进行打包
5. 修改端口号在`scripts`文件夹里的`start.js`里找到
  const DEFAULT_PORT = parseInt(process.env.PORT,10) ||`8086`;
6. 修改根目录层级在`config`文件夹里的`webpack.config.dev.js`里找到    
```javascript
 output: {
    //增加js面的内容
    // Add /* filename */ comments to generated require()s in the output.
    pathinfo: true,
    // This does not produce a real file. It's just the virtual path that is
    // served by WebpackDevServer in development. This is the JS bundle
    // containing code from all our entry points, and the Webpack runtime.
    filename: 'gfwkpfe/bundle.js',
    // There are also additional JS chunk files if you use code splitting.
    chunkFilename: 'gfwkpfe/[name].chunk.js',
    // This is the URL that app is served from. We use "/" in development.
    publicPath: publicPath,
    // Point sourcemap entries to original disk location (format as URL on Windows)
    devtoolModuleFilenameTemplate: info =>
      path.resolve(info.absoluteResourcePath).replace(/\\/g, '/'),
  },
```
7. 修改打包后的路径在config文件夹里的paths.js里找到
```javascript
function getServedPath(appPackageJson) {
  const publicUrl = getPublicUrl(appPackageJson);
  const servedUrl =
    envPublicUrl || (publicUrl ? url.parse(publicUrl).pathname : '/gfwkpfe/');
  return ensureSlash(servedUrl, true);
}
```
8. 添加htaccess文件配置应该写到config文件夹里的webpack.config.prod.js里的`plugins`内
##二．父组件给子组件传参数
1. 直接把参数写在组件上```<BodyIndex  userid={ddd}/>```,组件开头字母要大写，而且因为是JXS写法所以必须闭合有`/`
2. 子组件通过`this.props.xxx`进行接收父组件传过来的参数
3. 如果组件没传过来参数，但是子组件里又必须有个参数，可以通过设置```const defaultProps = {};``` 这句写在`import`引入一些文件的下面，还要在组件最下面再加一句，```组件名.defaultProps = defaultProps```;
4. 父页面参数传到子，子再传到子子页面通过```<BodyIndex  {...this.prop}  id={4} />```
  通过...this.prop把父页面参数都传过去，也可以在这基础上在定义自己的参数，例如：```id={4}```就是自己定的参数
##三．子组给父组件传参数
1. 绑定事件的方法用`Es6`写法的话调用方法```<input type=”button” value=”提交” onClick={this.changeFunction.bind(this)}>```必须有```.bind(this)```不然对应方法里的```this.setState```会说找不到，会报错
2. 子组件里如果`ononChange`执行了就会调用父组件的`handleChidValueChange`
  ``` <input type=”button” value=”提交” onChange={this.props.handleChidValueChange}>``` 父组件里的方法
```
handleChidValueChange(event) {
  ........
}
```
然后通过`event.target.value`取出传过来的值
3. 父组件里
```
render () {
  return(
   <BodyChild handleChidValueChange{this.handleChidValueChange.bind(this)}>
   )
}
```
4. 子组件往父组件传递参数只能通过这种事件的方式传递

```
render() {
  return(<input type=”button” value=”提交” onClick={this.changeFunction.bind(this)}>
)}
```
给事件传参数就写在this后面就可以```<input type=”button” value=”提交” onClick={this.changeFunction.bind(this,99)}```
5.  ```组件名.propTypes = {} ```可以对传过来的参数类型进行显示

##四．react样式
1. 第一种生成时候是直接加在html里，一般手机端可能会用到，网页端不用
  写在`render`里面`return`之前通过
```
const xxx = {
  Header:{
  “pading-top”:”15px”,
  //如果不想加双引号,第二个开头字母大写
  paddingBottom:”15px”
 }
}
```
然后在
```
render(){
  Return(
    <header style={xxx.header}></header>
  )
}
```
2. 外部样式引用
  ```<header  className=”xxxx”></header>```
3. 全局引用
  在```index.html```文件里```<head><link rel=”stylesheet” href=”.src/css/style.css”></head>```
4. 用`true`或`false`控制样式
  `Constructor`里设`miniHeader：false`
  在一个点击事件里给`miniHeader：!this.state.miniHeader`
  在`render`里面`return`之前通过
```
const xxx = {
  Header:{
    “pading-top”:(this.state.miniHeader)?15px”:”3px”
  }
} 
```
5. `css`模块化，保证自己写的不影响其他模块，避免如果有一个模块有个叫`aaa`的样式，另一个模块也有一个叫`aaa`的样式互不影响
  ① 安装三个插件 `babel-plugin-react-html-attrs,style-loader,css-loader`
  ② `className`可以换成`class`
  ③ `webpack`里配置`plugins：[‘react-html-attrs’]`
  ④ 可以用`var xxx = require(“../../css/footer”);`把`css`引进来，然后
  `<footer class=”xxx .css文件里的样式名字”></footer>`
  查看样式时候样式前面自动加了`xxx-样式名字`，所以不会影响同名的样式
  ⑤`jsx`样式与`css`互转可以使用一个工具， `css to React`
  ##五．接收数据用axios，跟vue里面用法一样
  ①```xxxApi.js``` 文件里配置```import axios from 'axios';```
   例如：
 ```javascript
 const GET_URL = "/api-gateway/gfwkp-api/getSpecialPlanLayoutCtl";

export const getxxx = params => {
  return axios.get(GET_URL, {params: params});
};
 ```
`import`引入需要的接口
```javascript
getxxx(params) {
  let imageListParams = {
           
   }
getxxx(imageListParams).then((result) => {
  let res = result.data;
  if (res.resultCode === '00') {
    ..........         
  } 
  else {
    ........
  }
   })
   .catch(err => {
     ..........
   });
 }
```
##六.State
1. `State`属性是页面组件自身的属性，`State`对当前组件更新之后，他会立马反映到虚拟`dom`上，然后在更新到`DOM`,这个过程是自动更新的，相当于`vue`里面`data`里定义的属性状态。
2. 初始化放置在构造函数`constructor`里
3. 用`{this.state.xxxx}`取到状态值显示在`render`的`return`里
4. 修改状态用`this.setState({username:”mmm”})`
5. `state`的作用域只属于当前类，不污染其他模块
6. `super`是固定写法必须得有的
```javascript
export default class BodyIndex extends React.Component {
constructor () {
  super();//调用基类的所有的初始化方法
  this.state = {username:”ppp”};
}
render() {
  return (
    <div>{this.state.username}
    </div>
   )
  }
}
```
##七．生命周期
组件初始化：`constructor`
组件马上要挂载了：`componentWillMount`
① 如果这个函数调用`setState`，本次的`render`函数可以看到更新后的`state`并且只渲染一次
组件已经挂载：`componentDidMount`
② 去服务器取数据一般写在这个周期里
组件要接收父组件的值了：`componentWillReceiveProps`
判断是不是要更新组件：`shouldComponentUpdate`要返回`true`，返回`false`的话就不会渲染render
马上要更新组件了：`componentWillUpdate`
组件更新完毕：`componentDidUpdate`
组件卸载了：`componentWillUnmount`
##八．组件Refs
1. 获取原生的html节点可以用`refs`
2. 在`html`中`ref=”aaa”`,然后通过`this.refs.aaa`获取这个`html`元素
##九．onclick长找不到this的解决方案
① 在`constructor`里绑定`this.方法名 = this.方法名.bind(this)`,强制的把方法名绑定在当前实例上
② `onclick`里直接使用箭头函数`<button onclick={()=>this.方法名()}></button>`
③ 直接把方法变成箭头函数形式，`方法名 = ()=>{...}`

##十．路由-react-router4

① 和之前的版本不兼容

② `npm install react-router-dom --save`

③ `import`引入`import{BrowserRouter,Router,Link} from ’react-router-dom ’`

④,包裹整个应用，`Router`路由对应渲染的组件，可以嵌套，`Link`跳转用

⑤
```
<div>
  <BrowserRouter>
    <ul>
      <li><Link to=’/’></Link></li>
      <li><Link to=’/aaa’></Link></li>
    </ul>
    //针对不同的路由显示不同组件
    //exact是保证路由完全比配，因为第一个是/，第二个也带/不能完全匹配路由，所以要加 exact
    <Router path=”/” exact component={App}></Router>
    <Router  path=”/aaa” component={Bbb}></Router>
  </BrowserRouter>
</div>
```
⑥ `this.props.history.push(‘/’)`可以跳转路由

⑦ `this.props.match.params.location`可以去`url`后面参数

⑧ `import`引入`Redirect`

```<Redirect path=”/aaa”></Redirect>写在<Router path=”/” exact component={App}></Router>```

上面，路由重定向

⑨ ```<Switch></Switch>```包裹所有```Router``` 只渲染命中的第一个```Router ``` 

⑩ 
//每个reducer都有一个state，所以需要合并所有reducer

```import {conbineReducers} from ‘redux’```

```export default conbineReducers({xxx1,xxx2})```
##十一．引入antdesign 的css按需加载方法
① `npm install babel-piugin-import --save`
② 把`[‘import’,{libraryName:.....}]`粘贴到`package.json`里`babel`里加个`”plugins”:[]的[]里`
##十二．redux
① `redux`专注于状态管理和`react`解耦，`angular`或者`vue`里都可以用`redux`
② `store`储存状态，`state`用来记录，需要改变时候告诉专员`（dispatch）`要干什么`（action）`
处理变化的人`（reducer`）拿到`state`和`action`,生成新的`state`
③ `store.getSstate`获取状态，用`store.subscribe`监听每次修改
④` npm install redux --save` 安装
⑤
//声明常量，保证下面可以调用
```javascript
const ADD = ‘加’
const REMOVE= ‘减’

//新建store,根据老的state和action，生成新的state
//定义一个index.redux.js文件存储reducer操作
export function counter(state=0, action) {
  Switch(action.type) {
    case ‘ADD ’:
    return state +1
    case ‘REMOVE’:
    return state -1
    return 10
  }
}
export function add() {
  return {type:ADD }
}
export function add() {
  return {type:REMOVE’}
}
```
⑥ 
//新建store
`import {createStore} from ‘redux’`
`const store = createStore(counter) `
组件里通过，`<App store={store}>` 往组件里传递`store`，写在一个函数里
然后函数名()
//状态有任何变化render会重新执行
`store.subscribe(函数名())`

⑦
//派发事件，相当于传递action
`store.dispatch({type:’加’})`
`store.dispatch({type:’减’})`

⑧ 在`<App/>`组件里
`Import {add} from ‘...’`
//获取store状态
`const int = store.getState() `
`<button onClick={()=>store.dispatch(add())}></button>`

⑨ `redux`优化组件解耦
`Import {add} from ‘...’ `里的`add`最好以参数形式传到组件里
`<App store={store} add={add}>`, 然后组件内部通过，`this.props.add`获取
然后通过`const = this.props.add`
`<button onClick={()=>store.dispatch(add())}></button>`

⑩ `redux`处理异步，需要r`edux-thunk`插件，调试工具用`npm install redux-devtools-extension`, 还得使用`react-redu`x库 ，可以简化`redux`操作
`npm install redux-thunk --save`
使用`applyMiddleware`开启`thunk`中间件
开启方法` import{createStore} from’redux’`添加`applyMiddleware,const store = createStore(counter)`里添加`applyMiddleware(thunk).
import {thunk} from ‘redux-thunk’`引入`redux-thunk`
//之前是return个对象，现在用是return一个函数,函数的参数是dispatch
在`index.redux.js`文件里新添加
```javascript
export function addAsync() {
  return dispatch=>{
  //需要提交action的地方用dispatch执行一下
  setTimeout(()=>{dispatch(add())},2000)
  }
}
```
组件里调用方法跟之前一样
##十三．Chrome中redux调试工具的使用
① 全局变量`index.js`里放 
```javascript
const reduxDevtools = window.devToolsExtension?
window.devToolsExtension():f=>f
const store = createStore(counter,compose(applyMiddleware(thunk),reduxDevtools ))
```
##十四．使用react-redux
① `npm install react-redux --save`
② `Provider`组件在应用最外层，传入`store`就可以，只用一次
`Import{Provider} from ‘react-redux’`
把`<App/>`组件放大`<Provider></Provider>`里的，`store={store}`放到`<Provider></Provider>`里，只传`store`，剩下其他参数都不传
③ `Connect`负责从外部获取组件需要的参数
组件里`import {connect} from’react-redux’`
//connect先执行，最后App 当成参数传进来
//connect需要传1.需要哪些数据 2.需要哪些动作
```javascript
const num = store.getState()变成const num = this.props.num
const mapStatetoProps=(state)=> {
  return {num:state}
}
```
组件最后写`App = connect(mapStatetoProps,actionCreators )(App )`
④ 把所有`action important`进来， `const actionCreators = {action名字}`这些`action`都塞到`props`可以直接使用` const add = this.props.action名`
⑤ `<button onClick={()=>store.dispatch(add())}></button>`变成
`<button onClick={add}></button>`
