# React 框架学习总结

* React 不是一个 MVC 框架，它是构建易于可重复调用的 web 组件，侧重于 UI，也就是 view 层。
* 其次 React 是单向的从数据到视图的渲染，非双向数据绑定。
* 不直接操作 DOM 对象，而是通过虚拟 DOM 通过 diff 算法以最小的步骤作用到真实的 DOM 上。
* 不便于直接操作 DOM，大多数时间只是对 virtual DOM 进行编程。

## JSX

```
const element = <h1>Hello, world!</h1>;
```
HTML 语言直接写在 JavaScript 语言之中，这就是 JSX 的语法，它允许 HTML 与 JavaScript 的混写；它是 JavaScript 的一种扩展语法。
JSX 书写：遇到 HTML 标签以 “<” 开头书写，就用 HTML 规则解析；遇到代码块，以 “{” 开头书写，就用 JavaScript 规则解析。
你可以用花括号把任意的 JavaScript 表达式 嵌入到 JSX 中。例如，2 + 2， user.firstName， 和 formatName(user)，这些都是可用的表达式。
比起 HTML， JSX 更接近于 JavaScript， 所以 React DOM 使用驼峰 (camelCase) 属性命名约定, 而不是 HTML 属性名称。例如，class 在 JSX 中变为className，tabindex 变为 tabIndex。

## 如何渲染 DOM

ReactDOM.render() 是 React 的最基本方法，用于将模板转化为 HTML 语言，并将其插入到指定的 DOM 节点；
例如：

```
const element = <h1>Hello, world</h1>;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

这段代码是将一个 h1 标题插入到 id 为 root 的 DOM 节点；
React 只更新必须要更新的部分；React DOM 会将元素及其子元素与之前版本逐一对比，并只对有必要更新的 DOM 进行更新, 以达到 DOM 所需的状态。

## 组件和属性

React 通过组件的思想，将界面拆分成一个个可以复用的模块，每一个模块就是一个 React 组件。一个 React 应用由若干组件组合而成，一个复杂组件也可以由若干简单组件组合而成。
组件名称总是以大写字母开头，组件必须返回一个单独的根元素，组件可以在它们的输出中引用其它组件。
在 React 中组件分为函数式组件、类组件两种；

### 1，函数式组件

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

### 2，类组件

```
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```
这个是类组件，用类定义的组件有一些类专有的特性，指的是局部状态，类允许我们在其中添加本地状态 state 和生命周期钩子。

### 3，参数

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```
Props 是只读的。
无论你用函数或类的方法来声明组件, 它都无法修改其自身 props。所有 React 组件都必须是纯函数，并禁止修改其自身 props。

### 4，状态 state (在类组件中才可以定义 state)

```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```
state 和 props 类似，但是它是私有的，并且由组件本身完全控制；
组件免不了要与用户互动，React 就是将组件看成是一个状态机，一开始有一个初始状态，然后用户互动，导致状态变化，从而触发重新渲染 UI。

#### ①不要直接修改 state；用 setState() 代替：

```
this.setState({comment: 'Hello'});
```
#### ② state 更新可能是异步的；

React 为了优化性能，有可能将多个 setState() 调用合并为一次更新；因为 this.props 和 this.state 可能是异步更新的，你不能依赖他们的值计算下一个 state。
```
// 错误
this.setState({
  counter: this.state.counter + this.props.increment,
});
```
要弥补这个问题，使用另一种 setState() 的形式，它接受一个函数而不是一个对象。这个函数将接收前一个状态作为第一个参数，应用更新时的 props 作为第二个参数：
```
// 正确
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));
```

## 在类中添加生命周期方法

    * componentWillMount ：在渲染前调用。
    * componentDidMount ：在第一次渲染后调用，之后组件已经生成了对应的 DOM 结构，可以在这个方法中调用 setTimeout，setInterval 或者发送 AJAX 请求等操作。
    * componentWillReceiveProps ：在组件接收到一个新的 prop (更新后)时被调用，这个方法在初始化的 render 时不会被调用。
    * shouldComponentUpdate ：返回一个布尔值。在组件接收到新的 props 或者 state 时被调用。在初始化时或者 forceUpdate 时不会被调用，可以在你确认不需要更新组件时使用。
    * componentWillUpdate ：在组件接收到新的 props 或者 state 但还没有 render 时被调用，在初始化时不会被调用。
    * componentDidUpdate ：在组件完成更新后立即调用，在初始化时不会被调用。
    * componentWillUnmount ：在组件从 DOM 中移除的时候调用。

## 处理事件

* 在 React 中绑定点击事件：
```
<button onClick={activateLasers}>
  Activate Lasers
</button>
```
* 在 React 中你不能通过返回 false 来阻止默认行为。必须明确调用 preventDefault ：
```
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```

## 获取真实的DOM节点

* 组件并不是真实的 DOM 节点，而是存在于内存之中的一种数据结构，叫做虚拟 DOM(virtual DOM)。只有当它插入文档以后，才会变成真实的 DOM。但是，有时需要从组件获取真实 DOM 的节点，这时就要用到 ref 属性。由于 refs 属性获取的是真实 DOM，所以必须等到虚拟 DOM 插入文档以后，才能使用这个属性，否则会报错。下面代码中，通过为组件指定 Click 事件的回调函数，确保了只有等到真实 DOM 发生 Click 事件之后，才会读取 refs 属性。
```
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.focus = this.focus.bind(this);
  }

  focus() {
    // 通过使用原生 API ，显式地聚焦 text 输入框
    this.textInput.focus();
  }

  render() {
    // 在实例中通过使用 ref 回调函数来存储 text 输入框的 DOM 元素引用(例如：this.textInput)
    return (
      <div>
        <input
          type="text"
          ref={(input) => { this.textInput = input; }} />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focus}
        />

      </div>
    );
  }
}

```
* Refs 与函数式组件：
你不能在函数式组件上使用 ref 属性，因为它们没有实例，如果你需要使用 ref，你需要将组件转化成类组件，就像需要生命周期方法或者 state 一样。

```

function MyFunctionalComponent() {
  return <input />;
}

class Parent extends React.Component {
  render() {
    // 这里不会执行！
    return (
      <MyFunctionalComponent
        ref={(input) => { this.textInput = input; }} />
    );
  }
}

```

## 七，PropTypes

* 组件的属性可以接受任意值，字符串、对象、函数等等都可以。有时，我们需要一种机制，验证别人使用组件时，提供的参数是否符合要求。组件类的 PropTypes 属性，就是用来验证组件实例的属性是否符合要求。下面的 Greeting 组件有一个 name 属性。PropTypes 告诉 React，这个 name 属性是必须的，而且它的值必须是字符串。现在，如果我们设置 name 属性的值是一个数值；name 属性就通不过验证了。控制台会显示一行错误信息。
```
import PropTypes from 'prop-types';

class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

Greeting.propTypes = {
  name: PropTypes.string
};
```
* 默认的 prop 值，我们可以通过赋值特定的 defaultProps 属性为 props 定义默认值：
```
class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

// 指定 props 的默认值：
Greeting.defaultProps = {
  name: 'Stranger'
};

// 渲染为 "Hello, Stranger":
ReactDOM.render(
  <Greeting />,
  document.getElementById('example')
);
```
* 如果父组件没有为 this.props.name 传值，defaultProps 会给其一个默认值。propTypes 的类型检测是在 defaultProps 解析之后发生的，因此也会对默认属性 defaultProps 进行类型检测。

