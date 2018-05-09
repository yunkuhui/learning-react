
## 搭建 react 环境使用的是 Facebook 维护的 create-react-app 脚手架 

* 使用命令 `npm install -g create-react-app` 生成环境 
* 搭建好之后 `webpack` 的默认配置文件都是隐藏的，如果想自定义配置文件要运行 `npm run eject`  
* 运行 `npm start` 启动成程序 
* 运行 `npm run build` 命令进行打包 
* 修改端口号在 `scripts` 文件夹里的 `start.js` 里找到 
  const DEFAULT_PORT = parseInt(process.env.PORT,10) ||`8086`;
* 修改根目录层级在 `config` 文件夹里的 `webpack.config.dev.js` 里找到    
```javascript 
 output: {
    // Add /* filename */ comments to generated require()s in the output.
    pathinfo: true,
    // This does not produce a real file. It's just the virtual path that is
    // served by WebpackDevServer in development. This is the JS bundle
    // containing code from all our entry points, and the Webpack runtime.
    //修改位置
    filename: 'gfwkpfe/bundle.js',
    // There are also additional JS chunk files if you use code splitting.
    //修改位置
    chunkFilename: 'gfwkpfe/[name].chunk.js',
    // This is the URL that app is served from. We use "/" in development.
    publicPath: publicPath,
    // Point sourcemap entries to original disk location (format as URL on Windows)
    devtoolModuleFilenameTemplate: info =>
      path.resolve(info.absoluteResourcePath).replace(/\\/g, '/'),
  }
```
* 修改打包后的路径在 config 文件夹里的 paths.js 里找到
```javascript 
function getServedPath(appPackageJson) {
  const publicUrl = getPublicUrl(appPackageJson);
  const servedUrl =
    envPublicUrl || (publicUrl ? url.parse(publicUrl).pathname : '/gfwkpfe/');
  return ensureSlash(servedUrl, true);
}
```
* 添加 htaccess 文件配置应该写到 config 文件夹里的 webpack.config.prod.js里的 `plugins` 内

## 父组件给子组件传参数

* 直接把参数写在组件上 ```<BodyIndex userid={ddd}/>```，组件开头字母要大写，而且因为是 JXS 写法所以必须闭合有 `/`
* 子组件通过 `this.props.xxx` 进行接收父组件传过来的参数
* 如果父组件没传过来参数，但是子组件里又必须有个参数，可以通过设置 ```const defaultProps = {};```  这句写在 `import` 引入的一些文件的下面， 还要在组件最下面再加一句， ```组件名.defaultProps = defaultProps```; 
* 父页面参数传到子，子再传到子子页面通过 ```<BodyIndex {...this.prop} id={4} />```
* 通过...this.prop把父页面参数都传过去，也可以在这基础上再定义自己的参数，例如： ```id={4}``` 就是自己定的参数

## 子组给父组件传参数

* 绑定事件的方法用 `Es6` 写法的话调用方法 ```<input type=”button” value=”提交” onClick={this.changeFunction.bind(this)}>``` 必须有 ```.bind(this)```  不然对应方法里的 ```this.setState``` 会提示找不到，会报错
* 子组件里如果 `onChange`  执行了就会调用父组件的 `handleChidValueChange` 
* ``` <input type=”button” value=”提交” onChange={this.props.handleChidValueChange}>```
* 父组件里的方法
```
handleChidValueChange(event) {
  ........
}
```
* 然后通过 `event.target.value` 取出传过来的值
* 父组件里 render 内的内容
```
render () {
  return(
   <BodyChild handleChidValueChange{this.handleChidValueChange.bind(this)}>
   )
}
```
*  子组件往父组件传递参数只能通过这种事件的方式传递
```
render() {
  return(<input type=”button” value=”提交” onClick={this.changeFunction.bind(this)}>
)}
```
* 给事件传参数就写在 this 后面就可以
  ```<input type=”button” value=”提交” onClick={this.changeFunction.bind(this,99)}```
* ```组件名.propTypes = {}``` 可以对传过来的参数类型进行显示

## react 样式

* 第一种生成时候是直接加在 Html 里，一般手机端可能会用到，网页端用的少，写在 `render` 里面 `return` 之前
```
const xxx = {
  Header:{
  “pading-top”:”15px”,
  //如果不想加双引号,第二个开头字母大写
  paddingBottom:”15px”
 }
}
```
* 然后 return 里面的内容是：
```
render(){
  return(
    <header style={xxx.header}></header>
  )
}
```
*  外部样式引用
  ```<header  className=”xxxx”></header>```
*  全局引用
  在 ```index.html``` 文件里 ```<head><link rel=”stylesheet” href=”.src/css/style.css”></head>```
*  用 `true` 或 `false` 控制样式
  `constructor` 里 `miniHeader：false`
  在一个点击事件里 : `miniHeader：!this.state.miniHeader`
  在 `render` 里面 `return` 之前的内容是 ：
```
const xxx = {
  Header:{
    “pading-top”:(this.state.miniHeader)?15px”:”3px”
  }
} 
```
* `css` 模块化，保证自己写的不影响其他模块，避免如果有一个模块有个叫 `aaa` 的样式，另一个模块也有一个叫 `aaa` 的样式互不影响
  ① 安装三个插件 `babel-plugin-react-html-attrs， style-loader， css-loader` 
  ② `className` 可以换成 `class` 
  ③ `webpack` 里配置 `plugins ：[‘react-html-attrs’]` 
  ④ 可以用 `var xxx = require(“../../css/footer”);` 把 `css` 引进来， 然后 `<footer class=”xxx .css文件里的样式名字”></footer>` 查看样式时候样式前面自动加了 `xxx-样式名字`， 所以不会影响同名的样式
  ⑤ `jsx`样式与`css`互转可以使用一个工具， `css to React` 

## 接收数据用 axios，跟 vue 里面用法一样

* ```xxxApi.js``` 文件里配置 ```import axios from 'axios';```
例如：
```javascript
const GET_URL = "/api-gateway/gfwkp-api/getSpecialPlanLayoutCtl";

export const getxxx = params => {
  return axios.get(GET_URL, {params: params});
};
```
`import` 引入需要的接口
```javascript
getxxx(params) {
  let imageListParams = {
    ........      
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

## State

* `State` 属性是页面组件自身的属性，`State` 对当前组件更新之后，他会立马反映到虚拟 `dom` 上，然后在更新到 `DOM`， 这个过程是自动更新的， 相当于 `vue` 里面 `data` 里定义的属性状态。
* 初始化放置在构造函数 `constructor` 里
* 用 `{this.state.xxxx}` 取到状态值显示在 `render` 的 `return` 里
* 修改状态用 `this.setState({username:”mmm”})`
* `state` 的作用域只属于当前类， 不污染其他模块
* `super`是固定写法必须得有的
```javascript
export default class BodyIndex extends React.Component {
  constructor () {
    //调用基类的所有的初始化方法
    super();
    this.state = {username:”ppp”};
  }
  render() {
    return (
      <div>{this.state.username}</div>
    )
  }
}
```

##  生命周期

组件初始化 ：`constructor`
组件马上要挂载了 ：`componentWillMount`
* 如果这个函数调用 `setState`，本次的 `render` 函数可以看到更新后的 `state` 并且只渲染一次
组件已经挂载 ： `componentDidMount`
* 去服务器取数据一般写在这个周期里
组件要接收父组件的值 ： `componentWillReceiveProps`
判断是不是要更新组件 ： `shouldComponentUpdate` 要返回 `true`，返回 `false` 的话就不会渲染 render
马上要更新组件了： `componentWillUpdate`
组件更新完毕 ： `componentDidUpdate`
组件卸载了： `componentWillUnmount`

## 组件 Refs

* 获取原生的 HTML 节点可以用 `refs`
* 在 HTML 中 `ref=”aaa”`， 然后通过 `this.refs.aaa` 获取这个 HTML 元素

## onclick 事件找不到 this 的解决方案

* 在 `constructor` 里绑定 `this.方法名 = this.方法名.bind(this)`， 强制的把方法名绑定在当前实例上
* `onclick` 里直接使用箭头函数 `<button onclick={()=>this.方法名()}></button>`
* 直接把方法变成箭头函数形式， `方法名 = ()=>{...}`

## 路由-react-router4

* 和之前的版本不兼容
* `npm install react-router-dom --save`
* `import` 引入 `import{BrowserRouter,Router,Link} from ’react-router-dom’`
* `BrowserRouter` 包裹整个应用， `Router` 路由对应渲染的组件， `Link` 跳转用

```javascript
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
* `this.props.history.push(‘/’)` 可以跳转路由
* `this.props.match.params.location` 可以取 `url` 后面参数
* `import`引入`Redirect`， 路由重定向
  ```<Redirect path=”/aaa”></Redirect>写在<Router path=”/” exact component={App}></Router>```
* ```<Switch></Switch>``` 包裹所有 ```Router```  只渲染命中的第一个 ```Router ``` 
* 每个 reducer 都有一个 state，所以需要合并所有 reducer
```import {conbineReducers} from ‘redux’```
```export default conbineReducers({xxx1,xxx2})```
## 引入 antdesign  的 css 按需加载方法
* `npm install babel-piugin-import --save`
* 把 `[‘import’,{libraryName:.....}]` 粘贴到 package.json 里的babel的 `”plugins”:[]`里
## redux
* `redux` 专注于状态管理和 `react` 解耦， `angular` 或者 `vue` 里都可以用 `redux`
* `store` 储存状态， `state` 用来记录，需要改变时候告诉专员 `（dispatch）` 要干什么 `（action）`， 处理变化的人 `（reducer`） 拿到 `state` 和 `action`， 生成新的 `state`
* `store.getState` 获取状态， 用 `store.subscribe` 监听每次修改
* `npm install redux --save` 安装
```javascript
//声明常量，保证下面可以调用
const ADD = ‘加’
const REMOVE= ‘减’

//新建 store， 根据老的 state 和 action， 生成新的 state
//定义一个 index.redux.js 文件存储 reducer 操作
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
* 新建store
  `import {createStore} from ‘redux’`
  `const store = createStore(counter) `
  组件里通过， `<App store={store}>`  往组件里传递 `store`， 写在一个函数里， 
  然后下面写函数名();
* 状态有任何变化 render 会重新执行
  `store.subscribe(函数名())`
* 派发事件，相当于传递 action
  `store.dispatch({type:’加’})`
  `store.dispatch({type:’减’})`
* 在 `<App/>` 组件里
  `import {add} from ‘...’`
* 获取store状态
  `const int = store.getState()`
  `<button onClick={()=>store.dispatch(add())}></button>`
* `redux`优化组件解耦
  `import {add} from ‘...’ ` 里的 `add` 最好以参数形式传到组件里
  `<App store={store} add={add}>`，  然后组件内部通过， `this.props.add` 获取
  然后通过`const = this.props.add`
  `<button onClick={()=>store.dispatch(add())}></button>`
* `redux`处理异步， 需要 `redux-thunk` 插件，调试工具用 `npm install redux-devtools-extension`， 还得使用 `react-redux` 库， 可以简化 `redux` 操作
* `npm install redux-thunk --save`
* 使用 `applyMiddleware` 开启 `thunk` 中间件
* 开启方法 `import{createStore} from’redux’` 
* `applyMiddleware,const store = createStore(counter)`里添加`applyMiddleware(thunk)`
* import {thunk} from ‘redux-thunk’` 引入 `redux-thunk`
* 之前是 return 个对象， 现在用是 return  一个函数,函数的参数是 dispatch
* 在 `index.redux.js` 文件里新添加
```javascript
export function addAsync() {
  return dispatch=>{
  //需要提交action的地方用dispatch执行一下
  setTimeout(()=>{dispatch(add())},2000)
  }
}
```
* 组件里调用方法跟之前一样

## Chrome中redux调试工具的使用

* 全局变量 `index.js` 里放 
```javascript
const reduxDevtools = window.devToolsExtension?
window.devToolsExtension():f=>f
const store = createStore(counter,compose(applyMiddleware(thunk),reduxDevtools ))
```

## 使用 react-redux

* `npm install react-redux --save`
* `Provider` 组件在应用最外层， 传入 `store` 就可以，只用一次
  `Import{Provider} from ‘react-redux’`
  把 `<App/>` 组件放到 `<Provider></Provider>` 里，`store={store}`放到  `<Provider></Provider>` 里，只传  `store` ，剩下其他参数都不传
* `connect` 负责从外部获取组件需要的参数
  组件里`import {connect} from’react-redux’`
* connect先执行，最后App 当成参数传进来
* connect需要传1.需要哪些数据 2.需要哪些动作
```javascript
const num = store.getState()变成const num = this.props.num
const mapStatetoProps=(state)=> {
  return {num:state}
}
```
组件最后写 `App = connect(mapStatetoProps,actionCreators )(App)`
* 把所有`action important`进来
* `const actionCreators = {action名字}` 这些`action` 都塞到 `props` 
* 可以直接使用` const add = this.props.action名`
* `<button onClick={()=>store.dispatch(add())}></button>` 变成 
  `<button onClick={add}></button>`
