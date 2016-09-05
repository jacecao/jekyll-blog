---
layout: post
title:  "React入门笔记"
categories: React
---

最近工作较忙也没来得及更新博客，今天主要贴上我入门React的笔记，主要还是跟着阮一峰前辈的[React入门](http://www.ruanyifeng.com/blog/2015/03/react.html)来学习，我的笔记很多代码还是来源该教程，但作了一些更新，目前更新主要是2个方面：

```  
1.更新一些方法在ES6中的使用，和ES6代码的书写方式 
 
2.对一些比较常用的知识点参照官方说明文档作了一些补充和说明
```

对于刚入手React的朋友，我非常推荐阅读阮一峰前辈的这篇长文，下载代码练习很容易上手。

***

**环境准备**  
我这里是使用浏览器环境来练习的，所以我贴出`HTML`文档，其中React文件是来自[react-demo](https://github.com/ruanyf/react-demos),请去该地址下载，全文的`HTML`结构都是这样的：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta content="text/html; charset=utf-8">
    <title>React-demo</title>
    <script src="../build/react.js"></script>
    <script src="../build/react-dom.js"></script>
    <script src="../build/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">

    </script>
  </body>
</html>

```

**PropTypes的使用**  
《React入门》前面几个例子我略过，从propTypes说起啦， 
 组件的属性`this.props`可以接受任意值，字符串、对象、函数等等都可以。有时，我们需要一种机制，验证别人使用组件时，提供的参数是否符合要求。组件类的PropTypes属性，就是用来验证组件实例的属性是否符合要求注意这里组件创建的方式，必须这样才能生效使用上面的方式创建就不会生效。

```javascript
var Myprotypes = React.createClass({
        
    propTypes: {
      title: React.PropTypes.string.isRequired,
      //这里要求title必须是字符串
    },
     
    render() {
    return <h1> {this.props.title }</h1>;
  }

});

var data = 123;

ReactDOM.render(
  <Myprotypes title={data} /> ,document.getElementById('example')
);

//代码能正常运行，但在后台就会打印一个错误提示

```

ES6代码书写方式

```javascript
class Myprotypes extends React.Component{
   
  render() {
    return <h1> {this.props.title }</h1>;
  }

}

Myprotypes.propTypes = { title: React.PropTypes.string.isRequired};

var data = 123;

ReactDOM.render(
  <Myprotypes title={data} /> ,document.getElementById('example')
);
```
【注意】关于propTypes的使用，即使你的参数给的不符合我们设定的要求，代码回照样运行下去（只要代码本身没有错误），所以在官方文档里说了使用propTypes只是为了在开发阶段我们能对参数做一些限制，当参数不是我们当初要求时会在后台打印一个提示，这是为了更加方便的做调试。

**getDefaultProps设置组件属性默认值**  

```javascript
var Mydefault = React.createClass({
  getDefaultProps() {
    return {
      title: 'hello jace'
    };
  },
  render() {
    return (<h1>{this.props.title}</h1>);
  }
});

ReactDOM.render(<Mydefault />,document.getElementById('example'));
```

ES6中默认值的设定

```javascript
class Mydefault extends React.Component{
  render() {
    return (<h1>{this.props.title}</h1>);
  }
};

Mydefault.defaultProps = {title: 'hello Jace'};

ReactDOM.render(<Mydefault />,document.getElementById('example'));
```

**获取真实的DOM节点【ref属性的使用】**

```javascript
var Myinput = React.createClass({
  handleClick: function() {
    this.refs.myTextInput.focus();
  },
  render: function() {
    return (
      <div>
        <input type="text" ref="myTextInput" />
        <input type="button" value="Focus the input" onClick={this.handleClick} />
      </div>
    );
  }
});

ReactDOM.render(
  <Myinput />,
  document.getElementById('example')
);
```
由于 this.refs.[refName] 属性获取的是真实 DOM ，所以必须等到虚拟 DOM 插入文档以后，才能使用这个属性，否则会报错。所以一般都是通过事件回调来操作真实DOM。

【`ES6中操作DOM`】

```javascript
class Myinput extends React.Component{
  handleClick() {
    this.refs.myTextInput.focus();
  }
  render() {
    return (
      <div>
        <input type="text" ref="myTextInput" />
        <input type="button" value="Focus the input" onClick={this.handleClick.bind(this)} />
      </div>
    );
  }
}

ReactDOM.render(<Myinput/>,document.getElementById('example'));

```
【`注意`】在ES6编写中，需要注意回调函数中的this指向，这里在官方文档中也有也特别指出，这里我将原文放在这里：

```
 Methods follow the same semantics as regular ES6 classes,  
 meaning that they don't automatically bind this to the instance.   
 You'll have to explicitly use .bind(this) or arrow functions =>

意思是这里同样遵循ES6中Class的规则,this不会自动的指向实例本身，  
需要我们显示的为其绑定，可以通过2中方法，  
一种是bind(),一种是箭头函数，官方的例子：

// You can use bind() to preserve `this`
<div onClick={this.tick.bind(this)}>

// Or you can use arrow functions
<div onClick={() => this.tick()}>

```

**this.state 状态机**  
状态机是React的一大创新，就是将组件看成是一个状态机，一开始有一个初始状态，然后用户互动，导致状态变化，从而触发重新渲染UI。

```javascript
var Like = React.createClass({
  //初始化状态机
  getInitialState: function() {
    return {liked: false};
  },
  //通过交互回调来重设状态机
  handleClick: function() {
    this.setState({ liked: !this.state.liked});
  },
  render: function() {
    //获取状态机
    var text = this.state.liked ? 'like' : 'haven\'t liked';
    return (
      <p onClick = {this.handleClick}>
        you {text} this.click to toggle.
      </p>
    );
  }
});

ReactDOM.render(<Like />,document.getElementById('example'));
``` 

ES6中状态机的使用

```javascript
class Like extends React.Component {
  //注意这里是状态机的初始化，这里代码一个都不能缺
  constructor(props) {
    super(props);
    this.state = { liked: false };
  }
  handleClick() {
    this.setState({ liked: !this.state.liked});
  }
  render() {
  var text = this.state.liked? 'like' : 'haven\'t liked';
    return (
      <div>
        <p>you {text} this.click to toggle.</p>
        <input type="button" value="click me" onClick={this.handleClick.bind(this)}/>
      </div>
    );
  }
}
ReactDOM.render(<Like />,document.getElementById('example'));
```

**表单元素的取值和交互**  

```javascript

var InputV = React.createClass({
  getInitialState: function() {
    return {value: 'Hello!'};
  },
  handleChange: function(event) {
    this.setState({ value: event.target.value.substr(0,10)});
  },
  render: function() {
    var value = this.state.value;
    return (
      <div>
        <input type="text" value={value} onChange={this.handleChange}/>
        <p>{value}</p>
      </div>
    );
  }
});

ReactDOM.render(<InputV/>,document.getElementById('example'));

```
ES6的使用

```javascript

class InputV extends React.Component{
  constructor(props) {
    super(props);
    this.state = {value: 'Hello!'}
  }
  //注意这里需要传入event
  handleChange(event) {
    this.setState({ value: event.target.value});
  }
  render() {
    var value = this.state.value;
    return (
      <div>
        <input 
          type="text" value={value}
          //这里使用第二种绑定this的方式
          //需要注意的是需要箭头函数传递参数event 
          onChange={(event)=>this.handleChange(event)}
        />
        <p>{value}</p>
      </div>
    );
  }
};

ReactDOM.render(<InputV/>,document.getElementById('example'));

```

【注意】在React的输入组件的value不能直接赋值即`<input value='这里不能直接赋值'/>`一旦直接赋值就是给组件写死一个默认值,这里必须使用这里的方法来给表单组件赋值,这也是符合Reactr通过用户的操作来改变动态机触发DOM渲染的原理。  
同时React里还给我们一些方便的表单限制操作，这里我们使用了一个字符长度控制的方法`event.target.value.substr(0,10)`，当用户输入长度超过10个字符串后就不会再录入后面的字符串