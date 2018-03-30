# React 学习笔记
## 组建
React可以将代码封装成组建，像插入 HTML 一样插入到网页中。

注意：
1. 关于报错 Adjacent JSX elements must be wrapped in an enclosing tag (10:6) 

  ```Java
  class App extends Component {
    render() {
      return (
        <div className="App">
          text
        </div>
        <div>
         <MyComponent />
        </div>
      );
    }
  }
  ```

  ​
  原因是在同时 return 两个 div 元素。 
  具体解释：
  普通代码长这样：

  ```javascript
  class App extends React.Component {
    render(){    
      return (      
        <div>          
          <h1>Welcome to React</h1>      
        </div>    
      );  
    }
  }
  ```

  ​
  但是实际上内部是：

  ```Ja
  _createClass(App, [{
      key: 'render',
      value: function render() {
        return React.createElement(
          'div',
          null,
          React.createElement(
            'h1',
            null,
            'Welcome to React'
          )
        );
      }
    }]);
  ```

  我们可以看到这里返回的是一个 react element 内部有一个element
  但如果像最上面错误写法那样就是两个 return 每个 return 里面有一个 react element，显然是错误的。

* 凡是使用 JSX 的地方都要加上 type=“text/babel” 

* ReactDOM.render 是 React 最基本方法， 用于将模版转为 HTML 语言，并插入指定 DOM 节点。

* JSX 基本语法规则：遇到 HTML 标签 (以 < 开头 ) 用HTML解析，遇到代码块 (以 { 开头)，用 js 解析。

* Js map() 函数可以遍历 array 每个元素

* 每个 component 有自己 render 方法

**this.prop.children** 表示组建所有子节点。
有三种可能类型：
* undefined 没有子节点
* object 只有一个子节点
* array 多子节点
  因为类型不同所以处理 this.prop.children 要注意。不过 React.Children 可以用来处理这个问题并不用担心具体数据类型。

* 因为组建属性可以接受任意值， 字符串对象函数都可以。检验参数是否符合要求就需要用 **PropTypes** 。

## 获取真实 DOM 节点
组建只是存在于内存之中的数据结构，虚拟 DOM 。只有插入文档以后才会变成真实的 DOM。所有的 DOM 变动都先在虚拟 DOM 上发生，再将实际发生变动的部分反映到真实 DOM 上，这样可以极大提高网页性能表现。需要用到 ref 属性。

例如获取用户输入，需要通过 this.refs.[ refName ]  获取真实 DOM 节点，但必须等到虚拟 DOM 插入文档之后才有真实节点，所以可以通过为组建指定回调函数 例如发生 Click 事件之后才会读取 this.refs.[ refName ]  属性。

## state 
React 将组建看成状态机，在与用户互动过程中状态变化触发重新渲染 UI 

每次修改状态值后会自动调用 this.render 方法。

## 表单
获取文本输入框的值，不能用 this.props.value 读取，需要定义一个 onChange 事件的回调函数， 通过 event.target.value 读取用户输入的值。

## 组建生命周期  
分为三种状态：
Mounting 已插入真实 DOM
Updating 正在重新被渲染
Unmounting 已移出真实 DOM

React 为每个状态都提供了两种处理函数, will - 进入状态前调用, did 进入状态后调用，还有两种特殊状态处理函数 componentWillReceiveProps() 已加载的组建收到新参数时调用，shouldComponentUpdate() 判断组建是否重新渲染

## Ajax 
组建数据通常是通过 Ajax 请求从服务器获取，可以使用 componentDidMount 设置 Ajax 请求，等到请求成功用 this.setState 方法重新渲染 UI

## State vs Props
state 的主要作用是用于组件保存、控制、修改自己的可变状态。在组建内部初始化，可被自身修改，外部不能访问也不能修改。

props 的主要作用是让使用该组件的父组件可以传入参数来配置该组件。是外部传进来的配置参数，组建内部无法控制修改。

一个组件的 state 中的数据可以通过 props 传给子组件，一个组件可以使用外部传入的 props 来初始化自己的 state。

组建的 state 会成为子组建的 Props。Props 在 render 方法中作为React.createElement() 第二参数或像这样：
`<MyChild name={this.state.childsName} />`
这里父辈 state 的 childsName 值变成了子组建 this.props.name。从子组建的角度来看，name prop 是不可改的。如果需要改变，父辈应该直接改变内部 state ：`this.setState({ childsName: 'New name' });`
那么，如果子组建想改变他自己的 name prop呢，这通常需要通过 child event & parent callbacks。例如，子组建可能有事件 onNameChanged, 父辈通过回调函数监听事件：
`<MyChild name={this.state.childsName} onNameChanged={this.handleName} />`
子组建将通过 `this.props.onNameChanged('New name')` 将新的名字作为参数传递给回调函数，然后父辈可通过 event handler 来更新自己的 state，例如：

```javascript
handleName: function(newName) {
   this.setState({ childsName: newName });
}
```

**简单来说**：
state 是让组件控制自己的状态，props 是让外部对组件自己进行配置。

### Props
* **immutable**
  * which lets React do fast reference checks
* are used to **pass data down** from your view-controller
  * your top level component
* have **better performance**
  * use this to pass data to child components
### State
* should be managed in your view-controller
  * your top level component
* **mutable**
* has **worse performance**
* **should not be accessed** from child components
  * pass it down with props instead


参考链接： http://huziketang.com/books/react/lesson12