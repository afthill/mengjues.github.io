title: 《Introduction to React》 阅读笔记
date: 2015-12-30 13:28:07
tags:
- React
- Javascript
categories:
- 程序语言

---


## Chapter 01: 什么是React

React诞生于Facebook的广告部门，开始的时候这些前端的程序依然使用的是MVC模式去渲染模板的方法构建，这些模式主要用来处理数据集的双向绑定的问题。后来演进为现在的React的全新的前端开发模式，包括使用virtual DOM，JSX，Flux概念来构建复杂的javascript程序和前端用户界面。再到后面，react通过提供react native延及到手机开发领域，使用与react同样的开发模式和理念。

<!--more-->

### 为什么要使用react？

react在被创立的时候，主要是facebook需要解决在一个特别的需要的时候面临的技术挑战。

> 《The Art of Unix Programming》 by Eric Raymond about the Rule of Modularity:
> The only way to write complex software that won't fall on its face is to hold its global complexity down -- to build it out of simple parts connected by well-defined interfaces -- so that most problems are local and you can have some hope of upgrading a part without breaking the whole.

这也是react在结果复杂的用户界面的时候使用的解决问题的方法。 react在facebook完美地解决了在large-scale UI上显示数据的问题。

### React解决了什么问题？

在现在的Client-Server框架设计中，往往把大部分UI的工作交给了浏览器和HTML、CSS和Javascript，这些设计往往使用的是“单页应用程序（SPA）”。但是当大量新的用户交互的需求加进来之后，代码往往变得难以维护，react主要解决的就是这些问题。

举例来说常见的客户端的MVC架构：应用程序必须包含views用来监听models，然后views可以独立的发生基于用户或者models的数据变化的更新。但是当应用程序需要scale的时候，越来越多的view和model加入了进来，在views和model之间的关系就会变得越来越错综复杂，在原来的应用程序中增加feature变得越来越困难。

React的诞生就是为了解决这些问题：首先facebook认为他们已经写好了初始的应用框架代码去描述应用程序能够做什么和看起来应该是什么样子，因此当后台的数据或者前端的状态（state）改变时可以只用再次运行一次layout启动代码。对于这样的想法的直接反应就是，这将会影响前端性能和用户体验。当你完整地替换在浏览器中运行的代码的时候，用户的屏幕将会闪烁和出现奇怪的样式，因此这看起来是低效的。facebook也看到了这个情况，他们的解决办法是创立一个新的机制去替换数据改变时的状态，这个替换的方法是可以被优化的。这就是react实际解决到的问题！

### React不只是另外一个Javascript前端框架

传统的前端框架使用的MVC模式如下：

<img src="/images/mvc.jpg">

#### model

model主要用来处理应用程序的状态（state），然后发送状态（state）改变的事件给view。

#### view

view主要是用户的界面和交互的接口。view可以发送事件给controller，有时也会直接发送给model。

### controller

controller是时间（events）调度器（dispatcher），可以发送指令给model去更新数据状态（state），或者发送界面变化给view去更新界面。

这个是一个基本的MVC的模式，在现存的前端MVC框架中可能基于这些基本的模式而衍生出来一些变种。

但是React与这些前端的框架都不同，react是描述应用程序UI的一种方式和当数据改变时UI更新的一种机制。如果真来对比的话，react类似于MVC模式中view。

react使用declarative的方式去定义用户的UI，并没有使用可见的数据绑定（data binding）技术，由于react的这种简单的组件定义方式，使你很容易的把这些不同的组件组合起来而使你的应用程序scalable。

```
<body>
    <section id="todoapp"></section>

    <script src="react.js"></script>
    <script src="JSXTransformer.js"></script>
    <script src="js/utils.js"></script>
    <script src="js/todoModel.js"></script>
    <script type="text/jsx" src="js/todoItem.jsx"></script>
    <script type="text/jsx" src="js/footer.jsx"></script>
    <script type="text/jsx" src="js/app.jsx"></script>
</body>
```

解析：

- `section`是用来放置渲染后的React组件
- `react.js`是react的基础库
- `JSXTransformer.js`是JSX transformer
- `utils.js`、`todoModel.js`是data model和基础的公用库
- `todoItem.jsx`、`footer.jsx`、`app.jsx`是application内容，application是由app.jsx中的组件渲染的

app.jsx

```
var model = new app.TodoModel('react-todos');

function render() {
    React.render(
        <TodoApp model={model}/>,
        document.getElementById('todoApp')
        );
}

model.subscribe(render);
render();
```

解析：

- `<TodoApp model={model}/>`，JSX，Javascript XML transpiler，用来与集成进去react。JSX不是react必须的，但是通过使用JSX可以使代码可读性更好。这个JSX转化为Javascript后类似于：`React.createElement(TodoApp, {model: model})`
- 当组件（component）创建完成后，如果想附加到DOM中时，可以把要附加的DOM放在render的第二个参数里，比如这里是`React.render(..., yourDomIdentifier)`


### React是如何创建组件（component）的

```
var TodoApp = React.createClass({
    render: function() {
        ...
    }
});
```

解析：

- 首先是调用`React.createClass()`, 然后这个方法接受的是一个Javascript对象，对象里定义了很多方法，但是最重要的是`render()`，被所有的react组件（component）所需要。

```
render: function() {
    var footer;
    var main;
    var todos = this.props.model.todos;

    var showTodos = todos.filter(function(todo) {
        switch (this.state.nowShowing) {
            case app.ACTIVE_TODOS:
                return !todo.completed;
            case app.COMPLETED_TODOS:
                return todo.completed;
            default:
                return true;
        }
    }, this);

    var todoItems = shownTodos.map(function (todo) {
        return (
            <TodoItems
                key={todo.id}
                todo={todo}
                onToggle={this.toggle.bind(this, todo)}
                onDestroy={this.toggle.bind(this, todo)}
                onEdit={this.edit.bind(this, todo)}
                editing={this.stat.editing === todo.id}
                onSave=(this.save.bind(this, todo))
                onCancel={this.cancel}
            />
        );
    }, this);

    var activeTodoCount = todos.reduce(function(accum, todo) {
        return todo.completed ? accum : accum + 1;
    }, 0);

    var completedCount = todos.length - activeTodoCount;

    if (activeTodoCount || completedCount) {
        footer =
            <TodoFooter
                count={activeTodoCount}
                completedCount={completedCount}
                nowShowing={this.state.nowShowing}
                onClearCompleted={this.clearCompleted}
            />;
    }

    if (todos.length) {
        main = (
            <section id="main">
                <input
                    id="toggle-all"
                    type="checkbox"
                    onChange={this.toggleAll}
                    checked={activeTodoCount === 0}
                />
                <ul id="todo-list">
                    {todoItems}
                </ul>
        );
    }

    return (
        <div>
            <header id="header">
                <h1>Todos</h1>
                <input
                    ref="newField"
                    id="new-todo"
                    placeholder="What needs to be done?"
                    onKeyDown={this.handleNewTodoKeyDown}
                    autoFocus={true}
                />
            </header>
            {main}
            {footer}
        </div>
    );
}
```


解析：

- `this.props.models.todos`，因为前面已经将`model={model}`传入了render函数，所以这里可以直接使用`this.props`访问
- subcomponent: 这里的todoItems引用了另外一个react组件叫做`TodoItems`,位于自己独立的JSX文件中。

TodoItems:

```
app.todoItem = React.createClass({
    render: function() {
        return (
            <li className={React.addons.classSet({
                completed: this.props.todo.completed,
                editing: this.props.editing
            })}>
                <div className="view">
                    <input
                        className="toggle"
                        type="checkbox"
                        checked={this.props.todo.completed}
                        onChange={this.props.onToggle}
                    />
                    <lable onDoubleClick={this.handleEdit}>
                        {this.props.todo.title}
                    </lable>
                    <button className="destroy" onClick={this.props.onDestory}/>
                </div>
                <input
                    ref="editField"
                    className="edit"
                    value={this.state.editText}
                    onBlur={this.handleSubmit}
                    onChange={this.handleChange}
                    onKeyDown={this.handleKeyDown}
                />
            </li>
        );
    }
});
```

解析：

- TodoItem组件，是TodoApp的子组件，用来处理单个的list item。
- TodoItem组件可以被包含在TodoApp中，之所以要被分离到不同的组件中，是因为子组件包含了它自己的与用户之间的交互逻辑。
- 因为子组件不需要知道或者与application中的组件交互，因此它被制作成一个标准的组件。
- React的功能就是子组件模块化，然后方便后期在scale时候的维护

### 如何组装react的子组件

- `TodoItems`作为一个子组件，被加入一个无序列表中，然后这个无需列表放在一个变量中，叫做main。
- footer变量包含了一个TodoFooter组件
- 然后main和foot变量被加入了TodoApp的返回值中

React与传统MVC客户端框架有着大大的不同，因为react框架只是利用javascript去合成复杂的用户界面，所有与用户之间的交互都在可声明的组件中，并没有直接可以看到其他框架中很常见的数据绑定。

## React 中的概念和术语

加载react的方法：

- 使用script标签（tag）
- 集成react进入Browserify或者WebPack工具
    - 允许你使用`require('React')`
    - 与CommonJS的模块加载系统兼容

#### Component    

组件是React的核心，是你的应用程序的视图，通过使用`React.createClass()`建立：

```
var MyClass = React.createClass({
    render: function () {
        return (
            <div>hello world</div>
        )
    }
})
```

或者使用ES6的class：

```
class MyClass extends React.Component {
    render() {
        return <div>hello world</div>
    }
}
```

### Virtual DOM

虚DOM可能是React中最重要的概念，正如之前所说的，facebook每次当有数据更新或者用户交互时，都重新build了它的interface，但是也会带来严重的性能问题，facebook然后通过改变DOM更新的方式去提高性能：

Reconciliation：意思是每次当有DOM更新的需求的，react通过建立自己的虚DOM去计算最小需要更新的实际的DOM。

### JSX

JSX是transform层，用来转化写React组件的XML语法为真实的Javascript。这不是React必须要求的元素，但是它可以使构建application时更加方便。JSX不仅可以接受自定义的React类，而且可以接受HTML标签（tag），然后它可以通过JSX transformer转化为合适的React元素。

```
React.render(
    <div>
        <h1>Header</h1>
    </div>
);
```

没有JSX的版本：

```
React.render(
    React.creatElement('div', null, React.creatElement('h1', null, 'Header'));
);
```

### Properties:

在React中，properties经常被引用为`this.props`去访问properties，properties是一个组件所拥有的选项集，this.props是一个javascript对象，在整个react的生命周期中都不会改变，如果你想改变一个组件的state，应该通过state对象去修改。

### State

每个组件都有state，通过react被初始化，而且在整个react的生命周期中都可以被修改。state不应该被外部访问，除非父组件初始化或者增加了子组件的状态。在使用state，应该尽量避免使用过多的state，将会给组件引来更高的复杂度。


### Flux

Flux是与react非常相关的一个项目。Flux不是传统意义上的MVC双向数据绑定的架构，而是提供一个单向的数据流，然后这些数据修主要进入Flux架构的三个主要的方向：dispatcher, stores, react views。

### Tools

其他一些React的辅助工具，比如JSX transformer：

```
npm install -g react-tools
```

### Add-Ons

可以通过`react-with-addon.js`被访问，如果使用Browserify或者WebPack，可以使用`require(react/addons)`去访问。

另外还有一些社区的addons，比如react-router：

```
var App = React.createClass({
    getInitialState: function () {
    },
    render: function () {
        return (
            <div>
                <ul>
                    <li><Link to="main">Demographics</Link></li>
                    <li><Link to="profile">Profile</Link></li>
                    <li><Link to="messages">messages</Link></li>
                </ul>
                <UserSelect />
            </div>
            <RouteHandler name={this.state.name}/>
        );
    }
});

var routes = (
    <Route name="main" path="/" handler={App}>
    <Route name="profile" handler={Profile}>
    <Route name="messages" handler={messages}>
    <DefaultRoute handler={Demographics}/>
    </Route>
);

Route.run(routes, function (Handler, state) {
    React.render(<Handler/>, document.getElementById("content"));
});
```

## Chapter02: The Core of React

### React.createClass

createClass方法用来创建一个react component，`React.createClass(specification)`的
specification需要传入一个Javascript对象，该对象必须包含`render()`方法。

```
var MyComponent = React.createClass({
    render: function () {
        return (
            <div>
                {this.props.name}
            </div>
        );
    }
});
```

```
React.render(<MyComponent name="frodo"/>, document.getElementById('container'));
```

也可以使用ES6的新class语法来写：

```
class MyComponent extends React.Component {
    render() {
        return (
            <div>
                {this.props.name}
            </div>
        );
    }
};
```

### React.Children.map

map是React.Children的一个方法，里面理工了几个helper函数。React.Children是一个包含了几个helper函数的对象，用来便于使用component的properties：

```
React.Children.map(children, myFn, [, context])
```

其中children是包含了children的目标对象，myFn是将要应用到每一个child的函数，context用来指定`this`。

示例：

```
var MyComponent = React.createClass({
    render: function () {
        React.Children.map(this.props.children, function(child) {
            console.log(child);
            });
        return (
            <div>
                {this.props.name}
            </div>
        );
    }
});


React.render(<MyComponent name="frodo"><p key="firsty">a child</p>
<p key="2">another</p></MyComponent>, document.getElementById('container'));
```

### React.Children.forEach

类似于React.Children.map，区别是这个函数不会返回对象：

```
var MyComponent = React.createClass({
    render: function () {
        React.Children.forEach(this.props.children, function(child) {
            console.log(child);
            });
        return (
            <div>
                {this.props.name}
            </div>
        );
    }
});

React.render(<MyComponent name="frodo">
<p key="firsty">a child</p>
<p key="2">another</p>
</MyComponent>, document.getElementById('container'));
```

### React.Children.count

返回`this.props.children`所包含的component的个数。

### React.Children.only

用来返回`this.props.children`所包含的唯一的component，不能用在多个component存在情况下。

### React.createElement

```
React.createElement(type, [props[, [children ...]]]);
```

如何不使用JSX的div标签，而使用createElement直接创建一例：

```
var MyComponent = React.createClass({
    displayName: "MyComponent",

    render: function render () {
        return React.createElement(
            "div",
            null,
            this.props.name
        );
    }
});

React.render(React.createElement(MyComponent, {name: 'frodo'}),
document.getElementById("container"));
```

### React.cloneElement

```
React.cloneElement(element, [props], [children ...]);
```

### React.DOM

用来在不适用JSX的标签的情况下，动态创建DOM elements：

```
React.DOM.div(null, "my div");
```

### React.createFactory

基于element type创建element：

```
React.createFactory(type);
```

### React.render

React.render主要用来接受一个ReactElement，并把它合并到DOM中，同时支持加入callback用来在ReactElement并入DOM成功后回调：

```
var MyComponent = React.createClass({
    render: function () {
        return (
            <div>
                {this.props.name}
            </div>
        );
    }
});

React.render(<MyComponent name="frodo"/>, document.getElementById("conainter"));

```

### React.renderToString

React.renderToString用来渲染一个ReactElement到它初始的HTML标签语言。

```
React.renderToString(ReactElement);
```

### React.findDOMNode

返回传入的React组件的DOM元素。


## Discovering React Components

React Components 的创建：

```
class MyComponent extends React.Component {
    render () {
        return (<div>Hello World</div>)
    }
}
```

```
var MyComponent = React.createClass({
    render: function () {
        return (<div>Hello World</div>);
    }
});
```

### component自己的api：

setState，可以传入一个function，或者JS对象：

```
setState(function (currState, currProps) {
    return {X: currState.X + "state changed"}
});

setState({X: "state changed"});
```

当setState被调用的时候，新的对象就会进入React的更新队列中，这个更新队列就是React用来维护对象的状态的改变。

一旦一个对象的状态准备要改变时，拥有这个新状态的对象或者部分状态更新的对象将会被合并入组件的状态的余部，实际的状态的更新过程是放在后台进行的，因此没有办法保证更新的顺序，因此在setState时，加入callback函数是非常必要的。

一个重要针对state的要点是：

用来不要直接改变对象的状态（`this.state`），而应该视该对象为immutable，只能通过React的setState方法加入React的队列来改变。

forceUpdate:

调用该方法，将强制React更新，依然使用的是React的队列系统，并强制组件更新自己的状态。它之所以可以做到强制更新是通过跳过react组件的一些lifecycle（ComponentShouldUpdate）。

```
foreceUpdate(callback);
```

## 理解React组件的方法和属性集

React.createElement的内部实现：

```
var ReactClassComponent = function () {};
assign(
    ReactClassComponent.prototype,
    ReactComponent.prototype,
    ReactClassMixin
);
```

这里就是把ReactClassMixin和ReactComponent的prototype链接给了ReactClassComponent。因此通过ReactClassMixin引入了一个新的方法：replaceState。

replaceState函数将会完全地覆盖当前组件里的任何状态：

```
replaceState(nextState, callback);
```

isMounted: 来自于ReactClassMixin，用来检查组件是否已经并入DOM。


setPros(next, callback)或者replaceProps，用来设定component的props。

### 组件的生命周期和渲染

Component specification functions:

render:

React的核心函数，接受一个ReactElement和一个DOM container；

getInitialState:

该函数只会被调用一次，在组件的render之前被调用，用来返回组件在被render时候的状态。

getDefaultProps:

当一个ReactClass第一次被创建的时候，getDefaultProps被调用一次，并且被缓存起来。该函数主要返回组件的`this.props`。

```
var GenericComponent = React.createClass({
    getInitialState: function () {
        return { thing: this.props.thingy};
    },

    getDefaultProps: function () {
        return {thingy: "cheese"};
    }
});

class GenericComponent extends React.Component {
    constructor(props) {
        super(props);
        this.state = {thing: props.thingy};
    }
}

GenericComponent.defaultProps = {thingy: "cheese"};
```

## Mixins

在组件的设计规范里，mixin是一个数组（array），一个mixins通过继承可以分享生命周期事件给主组件，示例：

```
var SetIntervalMixin = {
    componentWillMount: function () {
        this.intervals = [];
    },
    setInterval: function () {
        this.intervals.push(setInterval.apply(null, arguments));
    },
    componentWillUnmount: function () {
        this.intervals.map(clearInterval);
    }
};

var TickTock = React.createClass({
    mixins: [SetIntervalMixin],
    getInitialState: function () {
        return {seconds: 0};
    },
    componentDidMount: function () {
        this.setInterval(this.tick, 1000);
    },
    tick: function () {
        this.setState({seconds: this.state.seconds + 1});
    },
    render: function () {
        return (
            <p>
                React has been running for {this.state.seconds} seconds.
            </p>
        );
    }
});
```

## propTypes

propTypes是由React.PropTypes设定的：

PropTypes: {
    optionalBoolean: React.PropTypes.bool;
}

也可以应用其他的Javascript types：

- React.PropTypes.array
- React.PropTypes.bool
- React.PropTypes.func
- React.PropTypes.number
- React.PropTypes.object
- React.PropTypes.string
- React.PropTypes.any

还可以设定为required：

```
PropTypes: {
    requiredBoolean: React.PropTypes.bool.isRequired
}
```

除了Javascript的types，还可以应用到React自身的types：

```
myNodeProp: React.PropTypes.node
```

```
myNodeProp: React.PropTypes.element
```

propType helpers:

```
React.PropTypes.isinstanceOf(MyClass)
```

```
React.PropTypes.oneOf(['choose', 'cheese'])
```

```
React.PropTypes.onOfType([
    React.PropTypes.string,
    React.PropTypes.element,
    React.PropTypes.instanceOf(MyClass)
])
```

```
React.PropTypes.arrayOf(React.PropTypes.string)
```

```
React.PropTypes.objectOf(React.PropTypes.string)
```

### statics

通过设定statics属性，可以让function具有static属性，被调用时，不需要实例化function。

### displayName

用来显示在React app的debug信息中。

### componentWillMount

是React在把component往DOM中渲染的时候的生命周期的事件之一，componentWillMount将会在初始化渲染组件之前被执行，如果在这个事件内调用了setState方法，则不会触发组件的二次渲染，因为后面的render将会收到一个修改过的state对象。

### componentDidMount

是React把component成功地并入DOM后触发的事件，component已经变成了DOM的一部分，你可以访问自己的component在DOM中通过方法`React.findDOMNode()`

### componentWillReceiveProps

在component的prop有变化时，将会触发该事件。在该函数体内部依然可以使用setState，但是依然并不会触发二次的render。同时`this.props`并不会被覆盖，而且可以跟下一个即将传入的prop之间做对比：

```
componentWillReceiveProps(nextProps)
```

### shouldComponentUpdate

该函数将会在prop或者state有改变时触发，在component被第一次渲染之前或者forceUpdate被使用之前，该函数不会被调用。该函数的主要目的是：可以通过定义函数体的返回值false，决定component不需要被render，当你知道该state或者prop的改变并非真正地需要被渲染进入DOM的时候。如果决定不渲染不仅会跳过render（）步骤，而且下一步的componnentWillUpdate和componentDidUpdate也不会被执行。

```
shouldComponentUpdate(nextProps, nextState)
```

### componnentWillUpdate

在component被render之前被调用，在该函数中不能使用setState。

### componentDidUpdate

在update被渲染之后执行该函数，并不会在第一次render组件时候执行，而是在更新组件时执行。

```
componentDidUpdate(prevProps, prevState);
```

### componentWillUnmount

当React组件不再需要被渲染进入DOM时执行。


#### 完整的初始化渲染生命周期示例：

```
var GenericComponent = React.createClass({
    // Invoked first
    getInitialProps: function () {
        return {};
    },

    //Invoked second
    getInitialState: function () {
        return {};
    },

    //Third
    componentWillMount: function () {
    },

    //Render - Fourth
    render: function () {
        return (<h1>Hello World</h1>);
    }

    //Lastly
    componentDidMount: function () {
    }
});
```

![](/images/react1.PNG)

#### 完整的state改变时生命周期示例：

```
var GenericComponent = React.createClass({
    //First
    shouldComponentUpdate: function () {
    },

    //Next
    componnentWillUpdate: function () {
    }

    //Render
    render: function () {
        return (<h1>Hello World!</h1>);
    }

    //Finally
    componentDidUpdate: function () {
    }
})
```

![](/images/react2.PNG)


#### 完整的prop改变时生命周期示例：

```
var GenericComponent = React.createClass({
    //Invoke First
    componentWillReceiveProps: function (nextProp) {
    },

    //Second
    shouldComponentUpdate: function(nextProp, nextState) {
        // if you want to prevent the component updating
        // return false
        return true;
    },

    //Third
    componnentWillUpdate: function(nextProp, nextState) {
    },

    //Render
    render: function () {
        return (<h1>Hello World!</h1>);
    },

    //Finally
    componentDidUpdate: function () {
    }
});
```

![](/images/react3.PNG)

## React Element

- 可以使用JSX创建React Element
- 或者使用`React.createElement`

两者是同义的，但是如果使用JSX创建React Element，将会使用JSX transcompiler转化为JSX。

**注意**：通过`React.createElement`创建的element，并不被所有的浏览器所支持，现在支持的浏览器元素包括如下：

> a abbr address area article aside audio b base bdi bdo big blockquote body
br button canvas caption cite code col colgroup data datalist dd del details
dfn dialog div dl dt em embed fieldset figcaption figure footer form h1 h2
h3 h4 h5 h6 head header hr html i iframe img input ins kbd keygen label
legend li link main map mark menu menuitem meta meter nav noscript object
ol optgroup option output p param picture pre progress q rp rt ruby s samp
script section select small source span strong style sub summary sup table
tbody td textarea tfoot th thead time title tr track u ul var video wbr

除了创建单纯的element之外，还可以用`React.createElement`创建一个复合的ReactClass用来满足特别的需求。另外支持的HTML属性如下：

> accept acceptCharset accessKey action allowFullScreen allowTransparency alt
async autoComplete autoFocus autoPlay cellPadding cellSpacing charSet
checked classID className colSpan cols content contentEditable contextMenu
controls coords crossOrigin data dateTime defer dir disabled download
draggable encType form formAction formEncType formMethod formNoValidate
formTarget frameBorder headers height hidden high href hrefLang htmlFor
httpEquiv icon id label lang list loop low manifest marginHeight marginWidth
max maxLength media mediaGroup method min multiple muted name noValidate
open optimum pattern placeholder poster preload radioGroup readOnly rel
required role rowSpan rows sandbox scope scoped scrolling seamless selected
shape size sizes span spellCheck src srcDoc srcSet start step style tabIndex
target title type useMap value width wmode `data-*` `aria-*`

## React Factories

用来创建React所需要的HTML Element/Attribute, ReactClass等:

```
class Button {
    //class stuff
}

var Button = React.createFactory(require('Button'));

class App {
    render() {
        return Button({prop: 'foo'});
    }
}
```

# Chapter 03: JSX Fundermental

### 为啥要用JSX代替Javascript

React并不是必须依赖JSX，但是大部分人在写React还是使用的JSX，为啥呢？

- JSX是开发者和设计者更容易同时理解的语言
- XML类似的语法，与HTML相似，类似于之前用到的模板语言语法
- JSX依然是Javascript，在build时编译或者在浏览器中被编译
- Facebook正在编写specification把JSX推荐为ECMA的一个扩展（extension）

ECMA ES6已经实现了template literals:

```
var box = jsx`
    <${Box}>
    ${
        shouldShowAnswer(user)?
        jsx`<${Answer} value=${false}>no</${Answer}>` :
        jsx`
            <${Box.comment}>
            Text Content
            </${Box.comment}>
        `
    }
    </${Box}>
`;
```

JSX:

```
var box =
    <Box>
        {
            shouldShowAnswer(user)?
            <Answer value={false}>no</Answer>
            <Box.Comment>
                Text Content
            </Box.Comment>
        }
    </Box>;
```

明显JSX更加简洁可读。

### JSX是如何从XML转化为Javascript的？

```
var Hello = React.createElement({
    render: function () {
        return <div> Hello {this.props.name} </div>;
    }
});
```

```
React.render(<Hello name="World"/>, document.getElementById('container'));
```

Post JSX transformation:

```
var Hello = React.createClass({
    displayName: Hello,
    render: function () {
        return React.createElement("div", null, "Hello", this.props.name);
    }
});
```

```
React.render(React.createElement(Hello, {name: 'World'}), document.getElementById('container'));
```

更复杂的：

```
var GreetingComponent = React.createClass({
    render: function () {
        return <div>Hello {this.props.name}</div>;
    }
});
```

```
var GenericComponent = React.createClass({
    render: function () {
        return <GreetingComponent name={this.props.name}/>
    }
})
```

```
React.render(<GenericComponent name="World"/>, document.getElementById('container'));
```

解析：

- GenericComponent只是一个container。

JSX post transformation:

```
var GreetingComponent = React.createClass({
    displayName: 'GreetingComponent',
    render: function () {
        return React.createElement("div", null, "Hello", this.props.name);
    }
});
```

```
var GenericComponent = React.createClass({
    displayName: 'GenericComponent',
    render: function () {
        return React.createElement(GreetingComponent, {name: this.props.name});
    }
});
```

```
React.render(React.createElement('GenericComponent', {'name': 'World'}), document.getElementById('container'));
```

可以看到嵌套React Element跟HTML ELement是一样一样的。

**注意**：一般用首字母大写用来表示React Element，而小写用来表示HTML Element

### namespace and FormComponent

```
var React = require('react');

var FormComponent = React.createClass({
    render: function () {
        return <form>{this.props.children}</form>;
    }
});

FormComponent.Row = React.createClass({
    render: function () {
        return <fieldset>{this.props.children}</fieldset>;
    }
});

FormComponent.Label = React.createClass({
    render: function () {
        return <label htmlFor={this.props.for}>{this.props.text}{this.props.children}</label>;
    }
});

FormComponent.Input = React.createClass({
    render: function () {
        return <input type={this.props.type} id={this.props.id}/>;
    }
});

var Form = FormComponent;
var app = (
    <Form>
        <Form.Row>
            <Form.Label text="label" for="txt">
                <Form.Input id="txt" type="text"/>
            </FormLabel>
        </Form.Row>
        <Form.Row>
            <Form.Label text="label" for="chx">
                <Form.Input id="chx" type="checkbox"/>
            </FormLabel>
        </Form.Row>
    </Form>
);
```

```
React.render(App, document.getElementById('container'));
```

这里其实就是在前面声明一些React的虚拟元素，然后在后面的JSX里重用这些新声明的元素。

被transformer转化后的Javascript代码如下：

```
var React = require('React');

var FormComponent = React.createClass({
    displayName: "FormComponent",
    render: function () {
        return React.createElement(
            "form",
            null,
            this.props.children
            );
    }
});

var FormComponent.Row = React.createClass({
    displayName: "Row",
    render: function () {
        return React.createElement("fieldset", null, this.props.children);
    }
});

FormComponent.Label = React.createClass({
    displayName: "Label",
    render: function () {
        return React.createElement(
            "label",
            {htmlFor: this.props["for"]},
            this.props.text,
            this.props.children
            );
    }
});

FormComponent.Input = React.createClass({
    displayName: "Input",
    render: function () {
        return React.createElement("input", {type: this.props.type, id: this.props.id});
    }
});

var Form = FormComponent;
var App = React.createElement(
    Form,
    null,
    React.createElement(
        Form.Row,
        null,
        React.createElement(
            Form.Label,
            {text: "label", "for", "txt"},
            React.creatElement(
                Form.Input,
                {id: 'txt', type: 'text'}
            )
        )
    ),
    React.creatElement(
        Form.Row,
        null,
        React.createElement(
            Form.Label,
            {text: "label", "for": "chx"},
            React.createElement(
                Form.Input,
                {id: "chx", type: "checkbox"}
            )
        )
    )
);
```

```
React.render(App, document.getElementById("container"));
```

**解析：**

除了上例中的“嵌套”和”命名空间”之外，还有一个重要的问题是React传递子元素时是通过`this.props.children`来做的。

### JSX Property Spreading

Property Spreading是衍生自ES6和ES7早期spec里面的概念。

```
var greeting = {
    name: "World",
    message: "all your base are belong to us"
};

var Hello = React.createClass({
    render: function () {
        return <div> Hello {this.props.name}, {this.props.greeting}</div>;
    }
});

React.render(<Hello {...greeting}/>, document.getElementById("container"));
```

**解析：**

`...`表示的就是property spreading。

如果用ES6的语法，可以与JSX对比下：

```
var greeting = {};
greeting.name = "World";
greeting.message = "All your base are belong to us."

class Hello extends React.Component {
    render () {
        return (
            <div>Hello {this.props.name}, {this.props.message}</div>
        );
    }
}

React.render(<Hello {...greeting}/>, document.getElementById('container'))
```

下面是JSX被transform后的真实的Javascript代码：

```
var greeting = {};
greeting.name = "World";
greeting.message = "All your base are belong to us.";

var Hello = (function (_React$Component) {
    function Hello () {
        _classCallCheck(this, Hello);

        if (_React$Component != null) {
            _React$component.apply(this, arguments);
        }
    }

    _inherits(Hello, _React$Component);

    _createClass(Hello, [{
        key: "render",
        value: function render () {
            return React.createElement(
                "div",
                null,
                "Hello ",
                this.props.name,
                ", ",
                this.props.message
            );
        }
    }]);
    return Hello;
})(React.Component);
```

```
React.render(React.createElement(Hello, greeting), document.getElementById('container'));
```

**解析：**

这里并没有为property spreading使用专门额外的功能放在transform后的组件。通过使用property spreading，传入进来的JSON数据或者API数据，可以直接并入React的组件里。

```
var input1 = {
    "type": "text",
    "text": "label",
    "id": "txt"
};

var input2 = {
    "type": "checkbox",
    "text": "label",
    "id": "chx"
};

var Form = FormComponent;
var App = (
    <Form>
        <Form.Row>
            <Form.Label {...input1}>
                <Form.Input {...input1}/>
            </Form.Label>
        </Form.Row>
        <Form.Row>
            <Form.Label {...input2}>
                <Form.Input {...input2}/>
            </Form.Label>
        </Form.Row>
    </Form>
);
```

### Loop in JSX:

```
class ListItem extends React.Component {
    render () {
        return <li>{this.props.text}</li>;
    }
}

class BigList extends React.Component {
    render () {
        var items = ["item1", "item2", "item3", "item4"];
        var formattedItems = [];
        for (var i = 0, ii = items.length; i < ii; i++) {
            var textObj = { text: items[i] };
            formattedItems.push(<ListItem {...textObj}/>);
        }

        return <ul>{formattedItems}</ul>;
    }
}

React.render(<BigList/>, document.getElementById('container'));
```

**转化后的代码：**

```
var ListItem = (function (_React$Component) {
    function ListItem () {
        _classCallCheck(this, ListItem);

        if (_React$Component != null) {
            _React$Component.apply(this, arguments);
        }
    }

    _inherits(ListItem, _ReactComponent);

    _createClass(ListItem, [{
        key: "render",
        value: function render () {
            return React.createElement(
                "li",
                null,
                this.props.text
            );
        }
    }]);

    return ListItem;
})(React.Component);

var BigList = (function (_React$Component2) {
    function BigList () {
        _classCallCheck(this, BigList);

        if(_React$Component2 != null) {
            _React$Component2.apply(this, arguments);
        }
    }

    _inherits(BigList, _ReactComponent2);

    _createClass(BigList, [{
        key: "render",
        value: function render () {
            var items = ["item1", "item2", "item3", "item4"];
            var formattedItems = [];
            for (var i = 0, ii = items.length; i < ii; i++) {
                var textObj = {text: items[i]};
                formattedItems.push(React.createElement(ListItem, textObj));
            }

            return React.createElement(
                "ul",
                null,
                formattedItems
            );
        }
    }]);

    return BigList;
})(React.Component);

React.render(React.createElement(BigList, null), document.getElementById('container'));
```

### Conditional in JSX

```
var SignIn = React.createClass({
    render: function () {
        return <a href="/signin">Sign In</a>;
    }
});

var UserMenu = React.createClass({
    render: function () {
        return <ul className="usermenu"><li>Item</li><li>Another</li></ul>;
    }
});

var userIsSignedIn = false;
var MainAPp = React.createClass({
    render: function () {
        var navElement;
        if (userIsSignedIn) {
            navElement = <UserMenu/>;
        } else {
            navElement = <SignIn/>;
        }

        return <div>{navElement}</div>;
    }
});

React.render(<MainApp/>, document.getElementById("container"));
```

被transform后的代码：

```
var SignIn = React.createClass({
    displayName: "SignIn",

    render: function () {
        return React.createElement(
            "a",
            {href: '/signin'},
            "Sign In"
        );
    }
});

var UserMenu = React.createClass({
    displayName: "UserMenu",

    render: function () {
        return React.createElement(
            "ul",
            {className: "usermenu"},
            React.createElement(
                "li",
                null,
                "Item"
            ),
            React.createElement(
                "li"
                null,
                "another"
            )
        );
    }
});

var userIsSignedIn = false;
var mainAPp = React.createClass({
    displayName: "MainApp",

    render: function () {
        var navElement;
        if (userIsSignedIn) {
            navElement = React.createElement(UserMenu, null);
        } else {
            navElement = React.createElement(SignIn, null);
        }

        return React.createElement(
            "div",
            null,
            navElement
        )
    }
});

React.render(React.createElement(MainApp, null), document.getElementById("container"));
```

### JSX的三元操作符（Ternary Operator）

```
var SignIn = React.createClass({
    render: function () {
        return <a href="/signin">Sign In</a>
    }
});

var UserMenu = React.createClass({
    render: function () {
        return <ul className="usermenu"><li>Item</li><li>Another</li></ul>;
    }
});

var userIsSignedIn = true;

var mainApp = React.createClass({
    render: function () {
        return <div>{userIsSignedIn ? <UserMenu/> : <SignIn/>}</div>;
    }
});
```

## Chapter 04: 用React创建一个React网络应用程序

使用React去创建一个跟踪团队每个人effort的应用程序。

### Thinking in terms of Component

根据原始的想法和需求创建实际的应用程序有两种场景：

（一） 基于需求的大纲画出来线框图
（二） 基于已经存在的应用程序的架构，切分为不同React组件（component）

**WireFrames**

Authentication 组件：

![](/images/auth_component.png)

Registration 组件：

![](/images/register_component.png)

Workout Log Define 组件：

![](/images/workout_log.png)

Workout Log Record 组件：

![](/images/workout_record.png)

Workout Log History 组件：

![](/images/workout_history.png)

**Rewrite an existing Application**

比如通常用到的常见的authentication前端：

```
<div id="signInForm" class="notSignIn">
    <label for="username">Username:</label>
    <input type="text" id="username">
    <label for="password">Password</label>
    <input type="text" id="password">
    <button id="signIn">Sign In</button>
</div>

<div id="createAccount" class="notSignIn">
    <label for="username">Username:</label>
    <input type="text" id="username">
    <label for="password">Password:</label>
    <input type="text" id="password">
    <label for="password">Confirm Password:</label>
    <input type="text" id="confpassword">
    <button id="signIn">Create Account</button>
</div>
```

```
$('#signIn').on('click', function () {
    $('.notSignIn').hide();
    $('.signIn').show();
});
```

这段代码可以抽象成的React组件如下：

```
<Authentication>
    <SignIn />
    <CreateAccount />
</Authentication>
```

Navigation Menu的前端代码：

```
<ul id="navMenu">
    <li><a href="#defineWorkouts">Define Workouts</a></li>
    <li><a href="#logWorkout">Log Workouts</a></li>
    <li><a href="#viewHistory">View History</a></li>
    <li><a href="#logout" id="logout">Logout</a></li>
</ul>
```

Save Workout Definition代码：

```
<div id="defineWorkouts" class="tabview">
    <label for="defineName">Define Name</label>
    <input type="text" id="defineName">
    <label for="defineType">Define Type</label>
    <input type="text" id="defineType">
    <label for="defineDesc">Description</label>
    <textarea id="defineDesc"></textarea>
    <button id="saveDefinition">Save Definition</button>
</div>
```

Record Workout 代码：

```
<div id="logWorkout" class="tabview">
    <label for="chooseWorkout">Workout:</label>
    <select name="" id="chooseWorkout">
    </select>
    <label for="workoutResult">Result:</label>
    <input id="workoutResult" type="text"/>
    <input id="workoutDate" type="date"/>
    <label for="notes">Notes</label>
    <textarea id="notes"></textarea>
</div>
```

Workout History 代码：

```
<div id="viewHistory" class="tabview">
    <ul id="history">
    </ul>
</div>
```

然后可以基于这些基本的HTML组件创建React的组件。

### 创建必须的组件

**SignIn Component**:

```
var React = require("react");

var SignIn = React.createClass({
    return function () {
        return (
            <div>
                <label htmlFor="username">Username
                    <input type="text" id="username"/>
                </label>
                <label htmlFor="password">Password
                    <input type="text" id="password"/>
                </label>
                <button id="signIn" onClick={this.props.onAuthComplete.bind(null, this._doAuth)} Sign In </button>
            </div>
        );
    },
    _doAuth: function () {
        true;
    }
});

module.exports = SignIn;
```

**CreateAccount Component**

```
var React = require("react");
var CreateAcccount = React.createClass({
    render: function () {
        return(
            <div>
                <label htmlFor="username">Username:
                    <input type="text" id="username"/>
                </label>
                <label htmlFor="password">Password:
                    <input type="text" id="password"/>
                </label>
                <button id="signIn" onClick={this.props.onAuthComplete.bind(null, this._doAuth)}>Sign In</button>
            </div>
        );
    },

    _doAuth: function () {
        return true;
    }
});

modul.exports = SignIn;
```

**CreateAccount Component**

```
var React = require("react");

var CreateAccount = React.createClass({
    render: function () {
        return (
            <div>
            <label htmlFor="username">Username:
                <input type="text" id="username"/>
            </label>
            <label htmlFor="password">Password:
                <input type="text" id="password"
            </label>
            <label htmlFor="password">Confirm Password:
                <input type="text" id="confpassword"/>
            </label>
            <button id="signIn" onClick={this.props.onAuthComplete.bind(null, this._createAccount)}Create Account</button>
            </div>
        );
    },

    _createAccount: function () {
        return true;
    }
});
module.exports = CreateAccount;
```

**Auth Component**

```
var React = require("react");
var SignIn = require("./signin.jsx");
var CreateAccount = require("./createaccount.jsx");

var Authentication = React.createClass({
    render: function () {
        return (
            <div>
                <SignIn onAuthComplete={this.props.onAuthComplete}/>
                <CreateAccount onAuthComplete={this.props.onAuthComplete}/>
            </div>
        );
    }
})
module.exports = Authentication;
```

**Navigation Component**

```
var React = require("react");

var Navigation = React.createClass({
    render: function () {
        return (
            <ul>
                <li><a href="#" onClick={this.props.onNav.bind(null, this._nav("define"))}Define A Workout</a></li>
                <li><a href="#" onClick={this.props.onNav.bind(null, this._nav("store"))}Record A Workout</a></li>
                <li><a href="#" onClick={this.props.onNav.bind(null, this._nav("history"))}View History</a></li>
                <li><a href="#" onClick={this.props.onLogout}>Logout</a></lib>
            </ul>
        );
    },
    _nav: function (view) {
        return view;
    }
});

module.exports = Navigation;
```

**Define Component**

```
var React = require("react");

var defineWorkout = React.createClass({
    render: function () {
        return (
            <div id="defineWorkouts">
                <h2>Define Workout</h2>
                <label htmlFor="defineName">Define Name
                    <input type="text" id="defineName"/>
                </label>
                <label htmlFor="defineType">Define Type
                    <input type="text" id="defineType"/>
                </label>
                <label htmlFor="defineDesc">Description
                    <textarea type="text" id="defineDesc"></textarea>
                </label>
                <button id="saveDefinition">Save Definition</button>
            </div>
        );
    }
});

module.exports = DefineWorkout;
```

**Store Component**

```
var React = require("react");

var Option = React.createClass({
    render: function () {
        return <option>{this.props.value}</option>
    }
});

var StoreWorkout = React.createClass({
    _mockWorkouts: [
        {
            "name": "Murph",
            "type": "fortime",
            "description": ""Run 1 Mile \n 100 pull-ups \n 200
            push-ups \n 300 squats \n Run 1 Mile"
        },
        {
            "name": "Tabata Something Else",
            "type": "reps",
            "description": "4 x 20 seconds on 10 seconds off for
            4 minutes \n pull-ups, push-ups, sit-ups, squats"
        }
    ],
    render: function () {
        var opts = [];
        for(var i = 0; i < this._mockWorkouts.length; i++) {
            opts.push(<Option value={this._mockWorkouts[i].name}/>);
        }

        return (
            <div id="logWorkout" class="tabview">
                <h2>Record Workout</h2>
                <label htmlFor="chooseWorkout">Workout:</label>
                <select name="" id="chooseWorkout">
                    {opts}
                </select>
                <label htmlFor="workoutResult">Result:</label>
                <input id="workoutResult" type="text" />
                    78
                <input id="workoutDate" type="date" />
                <label htmlFor="notes">Notes:</label>
                <textarea id="notes"></textarea>
                <button>Store</button>
            </div>
        );
    }
});

module.exports = StoreWorkout;
```

**History Component**

```
var React = require("react");

var ListItem = React.createClass({
    render: function () {
        return <li>{this.props.name} - {this.props.result}</li>;
    }
});

var History = React.createClass({
    _mockHistory: [
        {
            "name": "Murph",
            "result": "32:18",
            "notes": "painful, but fun"
        },
        {
            "name": "Tabata Something Else",
            "type": "reps",
            "result": "421",
            "notes": ""
        }
    ],
    render: function () {
        var hist = this._mockHistory;
        var formatedLi = [];
        for(var i = 0; i < hist.length; i++) {
            var histObj = {name: hist[i].name, result: hist[i].result};
            formatedLi.push(<ListItem {...histObj}/>);
        }

        return (
            <div>
                <h2>History</h2>
                <ul>
                    {formatedLi}
                </ul>
            </div>
        );
    }
});
module.exports = History;
```

----------------------

## 测试你的React应用程序

React addon `testUtils`中包含了大量的方法：

**Simulate**

```
React.addons.testUtils.Simulate.{eventName}(DOMElement, eventData);
```

```
var node = React.findDOMNode(this.refs.input);
React.addons.testUtils.Simulate.click(node);
```

**renderIntoDocument**
