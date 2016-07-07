layout: post
title: "阅读笔记：OReilly 《AngularJS: up and running》"
date: 2015-11-27 13:25:00
tags:
- AngularJS
categories:
- 笔记
---

### AngularJS存在的意义

- 传统的HTML+jQuery/Javascript框架开发的前端逻辑是放在Javascript脚本里，HTML只有展示元素的作用。

- AngularJS可以把前端逻辑放在HTML中，通过阅读HTML直接可以得到内在的前端业务逻辑

### AngularJS的基本MVC架构

- Model
    - 是前端框架所使用到的数据
    - 数据一般来自于后台的服务

- View
    - 是用户看到的UI
    - 用户通过View与前端程序交互

- Controller
    - 业务逻辑放在这里
    - 是一个呈现层（Presentation Layer），用来驱动View（UI Layer）的展示与变化
    - 某些实现把这个称为*viewmodel*，或者*representer*

### Angular的特点：

1. Data-driven

    - 通过data-binding实现
    - data在服务器端和客户端双向自由流动，并且自我呈现，不需要人为干预

2. Declarative

    - 与declarative相对应的是imperative paradigm
    - declarative的模式不需要详细定义每个step的细节
    - declarative在HTML中声明所需要的逻辑，实现由angular的内部去实现
    - 示例:

            <tabs>
                <tab title="Home">Some content here</tab>
                <tab title="Profile">
                    <input type="text"
                        datepicker
                        ng-model="startDate"/>
                </tab>
            </tabs>

3. Separate your concerns

    - 使用MVC或者MVC-like结构去重新定义前端的设计
        - 涉及数据部分？放在model里
        - 涉及UI部分？放在view里
        - 涉及业务逻辑部分？放在controller里

    - Angular的MVC架构并不是纯粹意义的MVC，Angular的Controller并不直接与View交互

4. Dependency Injection

    - AngularJS完整地支持DI
    - DI是一个Java中非常常见的设计模式：
        - 通过接口约束依赖的的controller或者service的规格
        - 在服务的调用端，不需要通过new关键词或者function去实例化所依赖的对象

5. Extensible

    - AngularJS通过提供builtin的directives去实现它的行为
    - 这些指令集（directive）是放在HTML标签声明
    - 后台的Javascript脚本通过阅读和翻译directive，去生成可以在浏览器执行的代码
    - 用户可以提供自己的directive扩展

6. Test first, test again, keep testing

    - controller, services, directives, views, routes均被设计的容易测试
    - fast test runner: Karma
    - Protractor, WebDriver based UI testing runner


<!--more-->


### 如何开始

#### About ng-app

ng-app是angular用来确定它所使用的框架的工作范围，比如如果想在整个的HTML的页面中使用，我可以规定`<html ng-app>`，如果只在部分标签中使用，我们可以`<div ng-app>`

#### 项目结构

```
D:\restangular>tree /A /F
Folder PATH listing
Volume serial number is 1406-1BE6
D:.
|   basic-angularjs-app.html
|
\---lib
    \---vendor
        \---angular1.2.19
                angular.js
```

#### 第一个Angular程序

```html
<!DOCTYPE html>
<html ng-app>
    <head>
        <meta charset="utf-8">
        <title></title>
    </head>
    <body>
        <h1>Hello {{ 1 + 2}}</h1>
    </body>
    <script type="text/javascript" src="lib/vendor/angular1.2.19/angular.js">
    </script>
</html>
```

##### 关键点：

- ng-app directive，这是angular最重要的directive，用来控制angular的工作的范围（scope），如果放在`<html>`节点，则表示Angular可以控制整个页面

- `{% raw %}{{ }}{% endraw %}`                   
Angular的data binding，有的时候我们也可以放`angular expression`在里面

##### 第一个Hello World

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title></title>
    </head>
    <body ng-app>
        <input type="text" ng-model="name" placeholder="Enter your name"/>
        <h1>Hello <span ng-bind="name"></span></h1>
    </body>
    <script type="text/javascript" src="lib/vendor/angular1.2.19/angular.js">
    </script>
</html>
```

### AngularJS Modules

- module类似于Java中的package，是一个container
- module内部可以包含自己的controller, services, factories, and directives，这些内部的功能可以通过module去访问
- module可以有依赖（dependencies）

#### 如何声明module

```
angular.module('notesApp', [])
```

第一个参数是module名字，第二个`[]`是该module所需要的依赖（dependencies）

#### 如果加载存在的angular module

```
angular.module('notesApp');
```

可能存在的错误：

- 忘记使用`angular.module('moduleName', [dependencies,])`函数签名，改函数是用来的创建新的module的，如果使用`angular.module('moduleName')`则是直接加载存在的module
- 在module的文件没有被加载前而直接加载module，当然当前angular系统是查找不到该module
- bootstrap module是用来启动angular程序，需要在ng-app中作为可选参数声明

```html
<html ng-app="notesApp">
    <body>
        <h1>Hello {{ 1 + 2}}nd time AngularJS
    </body>
    <script type="text/javascript" src="lib/vendor/angular1.2.19/angular.js">
    <script type="text/javascript">
        angular.module('nnoteApp', []);
    </script>
</html>
```

### Controller

Controller是angular种workhorse（工马），用来做大部分UI控制方面的工作：

- 为UI去获得从server端传送的数据
- 决定那些数据要展示给User
- 如何展示元素，那部分元素需要被展示
- 用户交互方面控制（click listener）

#### 如何创建controller

```html
<!DOCTYPE html>
<html ng-app="notesApp">
    <head>
        <meta charset="utf-8">
        <title></title>
    </head>
    <body ng-controller="MainCtrl">
        <input type="text" ng-model="name" placeholder="Enter your name"/>
        <h1>Hello <span ng-bind="name"></span></h1>
    </body>
    <script type="text/javascript" src="lib/vendor/angular1.2.19/angular.js">
    </script>
    <script type="text/javascript">
        angular.module("notesApp", []).controller("MainCtrl", [
            function () {
                console.log("MainCtrl has been created");
            }
        ]);
    </script>
</html>
```

我们这里引入一个新的angular directive，`ng-controller`，ng-controller后面有一个参数，是用来告诉angular可以用所给的controller的名字去初始化一个controller对象，并把这个controller绑定到DOM的元素上。

我们新创建的controler，使用了`angular.module(...).controller("controllerName", [dependencies, function])`的签名：
- 第一个参数是controller的名字
- 第二个参数是一个list`[]`，里面有一个可选参数，controller的所有dependencies的列表，而最后一个参数是function，用来处理controller实际要做的工作

然后进一步来看一个实例：

```html
<html ng-app="notesApp">
<head><title>Notes App</title></head>
<body ng-controller="MainCtrl as ctrl">
{{ ctrl.helloMsg }} AngularJS.
<br/>
{{ ctrl.goodbyeMsg }} AngularJS.
</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module("notesApp", [])
            .controller("MainCtrl", [function () {
                this.helloMsg = "Hello ";
                var goodbyeMsg = "Goodbye ";
            }])
</script>
</html>
```

解析一下上面的实例：

- 创建了一个notesApp module
- 创建了一个“MainCtrl” controller，并且使用了ctrl作为实例名
- 定义了一个本地变量goodbyeMsg，通过关键词var，这个变量不会在页面中显示
- 定义了一个实例变量helloMsg
- 通过ng-controller directive绑定要HTML的元素body上
- 使用`{% raw %}{{ }}{% endraw %}`去refer（引用）controller实例中的变量

#### Scope

上面的实例实际有2个scope：
- 一个是controller自身的scope，这个scope里用var声明的变量只能在controller里使用，不会逃逸（escape）到controller外面
- 一个是DOM里面的scope，由于controller自身被angular初始化之后，被绑定到DOM上，所以DOM中的节点就有一个对controller的实例访问的能力，比如上例中的this所带的state就可以被DOM访问到

进一步复杂的实例：

```html
<html ng-app="notesApp">
<head><title>Notes App</title></head>
<body ng-controller="MainCtrl as ctrl">
{{ ctrl.message }} AngularJS.

<button ng-click="ctrl.changeMessage()">
    Change Message
</button>

</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module("notesApp", [])
            .controller("MainCtrl", [function () {
                var self = this;
                self.message = "Hello ";
                self.count = 0;
                self.changeMessage = function () {
                    console.log(self.count);
                    if (self.count % 2 == 0) {
                        self.message = "Goodbye ";
                    } else {
                        self.message = "Hello ";
                    }
                    self.count++;
                };
            }]);
</script>
</html>
```

解析如下：

- 只有一个binding，就是ctrl.message变量
- 使用了一个内建的（built-in）directive，`ng-click`，`ng-click`directive将会在任何button被click的时候执行传入的expression，我们这里是调用了一个`changeMessage`的function。
- changeMessage被执行时，改变了实例的message变量（goodbye）
- `self=this`是一个复杂的Javascript绑定context技巧，因为this是在运行时被动态判定，所以这里在第一次被DOM调用时，就用self限定了context，后面通过closure，可以把DOM的context传给后面执行的函数

### 结论

Angular应用程序是一个数据驱动的app，“model”是angular开发中唯一关注的真相，而涉及到更新UI等类似的底层的工作，是由angular帮开发者自动完成的。

#### declarative: ng-repeat

```html
<html ng-app="notesApp">
<head><title>Notes App</title></head>
<body ng-controller="MainCtrl as ctrl">
    <div ng-repeat="note in ctrl.notes">
        <span class="label">{{ note.label }}</span>
        <span class="status" ng-bind="note.done"></span>
    </div>
</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module("notesApp", [])
            .controller("MainCtrl", [function () {
                var self = this;
                self.notes = [
                    {id: 1, label: 'First Note', done: false},
                    {id: 2, label: 'Second Note', done: false},
                    {id: 3, label: 'Done Note', done: true},
                    {id: 4, label: 'Last Note', done: false}
                ]
            }]);
</script>
</html>
```

解析如下：

- 我们引入了一组array的JSON对象
- 我们把引入的array放在controller的实例对象里
- 我们使用ng-repeat去展示array里的数据
- 使用ng-repeat时所绑定的HTML DOM实际上ng-repeat的template
- ng-repeat类似于`foo each`LOOP
- 展示内容使用了2种技术：
    - ng-bind
    - `{% raw %}{{ }}{% endraw %}`expression
    - 没有真正的区别，都是有angular来管理数据的更新
    - 唯一的区别是：
        - ng-bind在angular bootstrap时被执行
        - 而`{% raw %}{{}}{% endraw %}`在第一次页面载入时会显示，然后顺便被替换，所以第一次load的时候，可能会看到`{% raw %}{{}}{% endraw %}`显示在页面上

#### `ng-cloak`

由于`{% raw %}{{}}{% endraw %}`第一次load会闪现展示的原因，可以使用`ng-cloak`去保证所有DOM的元素在angular没有处理完成数据更新之前不会显示在前段，大概实现方式如下：

1. 第一次加载`ng-cloak`时，使用如下的css rule去保证所有的angular里面的DOM不会显示：

```css
[ng\:cloak], [ng-cloak], [data-ng-cloak], [x-ng-cloak], .ng-cloak, .x-ng-cloak {
    display: none !important;
}
```

### More Directives

```html
<!DOCTYPE html>
<html lang="en" ng-app="notesApp">
<head>
    <meta charset="UTF-8">
    <title>Notes App</title>
    <style>
        .done {
            background-color: green;
        }

        .pending {
            background-color: red;
        }
    </style>
</head>
<body ng-controller="MainCtrl as ctrl">
<div ng-repeat="note in ctrl.notes" ng-class="ctrl.getNoteClass(note.done)">
    <span class="label">{{ note.label }}</span>
    <span class="asignee" ng-show="note.assignee" ng-bind="note.assignee"></span>
</div>

</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module("notesApp", [])
            .controller("MainCtrl", [
                function () {
                    var self = this;
                    self.notes = [
                        {label: 'First Note', done: false, assignee: 'Spears'},
                        {label: 'Second Note', done: false},
                        {label: 'Done Note', done: true},
                        {label: 'FirstNote', done: false, assignee: 'Brad'}
                    ];

                    self.getNoteClass = function (status) {
                        return {
                            done: status,
                            pending: !status
                        };
                    };
                }
            ]);
</script>
</html>
```

#### ng-show vs ng-hide

- `ng-show` 用来控制HTML元素的显示
- `ng-hide` 用来控制HTML元素的隐藏

如果传给ng-show的参数为truly，则该HTML的元素会显示，angularJS把一下的全部识别为truly：

- true
- non-empty string
- non-zero number
- non-null JS object

#### ng-class

用来给HTML的页面元素增加或者移除CSS，它可以接受string或者object作为参数：

- 如果是字符串，将会简单的应用该string对应的css
- 如果是object，则会determine每一个对象里的key，然后evalute该key对应的value，如果value是true，则会应用该css，如果为false，则会移除该css


### ng-repeat over object

```html
<!DOCTYPE html>
<html lang="en" ng-app="notesApp">
<head>
    <meta charset="UTF-8">
    <title>Notes App</title>
    <style>
        .label {
            background-color: green;
        }
        .author {
            background-color: purple;
        }
        div {
            border: 1px solid #666;
        }
    </style>
</head>
<body ng-controller="MainCtrl as ctrl">
<div ng-repeat="(author, note) in ctrl.notes">
    <span class="label">{{ note.label }}</span>
    <span class="author" ng-bind="author"></span>
</div>

</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module("notesApp", [])
            .controller("MainCtrl", [
                function () {
                    var self = this;
                    self.notes = {
                        Shyam: {
                            id: 1,
                            label: 'First Note',
                            done: false
                        },
                        Misko: {
                            id: 3,
                            label: 'Finished third notes',
                            done: true
                        },
                        Brad: {
                            id: 2,
                            label: 'Second Note',
                            done: false
                        }
                    };
                }
            ]);
</script>
</html>
```

解析：这里使用了`(key, value)`去unpack JS里面的对象，基本的用法跟python一样一样的。

### Helper Variables in ng-repeat

```html
<!DOCTYPE html>
<html lang="en" ng-app="notesApp">
<head>
    <meta charset="UTF-8">
    <title>Notes App</title>
</head>
<body ng-controller="MainCtrl as ctrl">
<div ng-repeat="note in ctrl.notes">
    <div>First Element: {{ $first }}</div>
    <div>Middle Element: {{ $middle }}</div>
    <div>Last Element: {{ $last }}</div>
    <div>Index of Element: {{ $index }}</div>
    <div>At Even Position: {{ $even }}</div>
    <div>At Odd Position: {{ $odd }}</div>
    <span class="label">{{ note.label }}</span>
    <span class="status" ng-bind="note.done"></span>
</div>

</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module("notesApp", [])
            .controller("MainCtrl", [
                function () {
                    var self = this;
                    self.notes = [
                        {id: 1, label: 'First Note', done: false},
                        {id: 2, label: 'Second Note', done: false},
                        {id: 3, label: 'Done Note', done: true},
                        {id: 4, label: 'Last Note', done: false}
                    ];
                }
            ]);
</script>
</html>
```

解析：基本是一些预定义的`FOR-LOOP`中的基本中间临时变量


### ng-repeat track by id

```html
<!DOCTYPE html>
<html lang="en" ng-app="notesApp">
<head>
    <meta charset="UTF-8">
    <title>Notes App</title>
</head>
<body ng-controller="MainCtrl as ctrl">
<button ng-click="ctrl.changeNote()">Change Notes</button>
<br/>
DOM Elements change every time some clicks
<div ng-repeat="note in ctrl.note1">
    {{ note.$$hashKey }}
    <span class="label">{{ note.label }}</span>
    <span class="author" ng-bind="note.done"></span>
</div>
<br/>

DOM Elements are reused every time some clicks
<div ng-repeat="note in ctrl.note2 track by note.id">
    {{ note.$$hashKey }}
    <span class="label">{{ note.label }}</span>
    <span class="status" ng-bind="note.done"></span>
</div>

</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module("notesApp", [])
            .controller("MainCtrl", [
                function () {
                    var self = this;
                    var notes = [
                        {
                            id: 1,
                            label: 'First Note',
                            done: false,
                            someRandom: 31431
                        },
                        {
                            id: 2,
                            label: 'Some Note',
                            done: false
                        },
                        {
                            id: 3,
                            label: 'Finished Third Note',
                            done: true
                        }
                    ];
                    self.note1 = angular.copy(notes);
                    self.note2 = angular.copy(notes);
                    self.changeNote = function () {
                        notes = [
                            {
                                id: 1,
                                label: 'Changed Note',
                                done: false,
                                someRandom: 4242
                            },
                            {
                                id: 2,
                                label: 'Second Note',
                                done: false
                            },
                            {
                                id: 3,
                                label: 'Finished Third Note',
                                done: true
                            }
                        ];
                        self.note1 = angular.copy(notes);
                        self.note2 = angular.copy(notes);
                    };
                }
            ]);
</script>
</html>
```

解析：ng-repeat在使用模板创建HTML DOM的时候，使用cache技术去重复使用DOM元素对象，如果两个对象的hash值的计算是一样的话，该hash值是有angular计算的，但是具体的算法可以使用`track by`去覆盖angular使用的hash算法，比如上例中，使用了`note.id`作为hash的identifer，那么每次ng-repat在iterate对象的时候，都会重用已经创建的DOM元素。

### ng-repeat across multiple HTML elements

```html
<!DOCTYPE html>
<html lang="en" ng-app="notesApp">
<head>
    <meta charset="UTF-8">
    <title>Notes App</title>
</head>
<body ng-controller="MainCtrl as ctrl">
<table>
    <tr ng-repeat-start="note in ctrl.notes">
        <td>{{ note.label }}</td>
    </tr>
    <tr ng-repeat-end>
        <td>Done: {{ note.done }}</td>
    </tr>
</table>

</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module("notesApp", [])
            .controller("MainCtrl", [
                function () {
                    var self = this;
                    self.notes = [
                        {
                            id: 1,
                            label: 'First Note',
                            done: false,
                            someRandom: 31431
                        },
                        {
                            id: 2,
                            label: 'Some Note',
                            done: false
                        },
                        {
                            id: 3,
                            label: 'Finished Third Note',
                            done: true
                        }
                    ];
                }
            ]);
</script>
</html>
```

解析：这里使用ng-repeat-start和ng-repeat-end去一个interation生成两个tr，之所以要用这种麻烦的方式是因为，ng-repeat必须声明在HTML的element中，但是这里有2个`<tr>`是兄弟关系，而不是父子关系，如果要在两个兄弟元素上使用ng-repeat中的本地变量，则必须把ng-repeat安装到父辈的元素上，但是这里不想再table中安装ng-repeat，则可以使用partial ng-repeat，分别安装到两个不同的元素里，这个start和end相当于scope的开始和结束，比如Javascript中的scope开始符号是`{`，而结束符号是`}`。

### Unit Test in Javascript

#### Test Runner vs Test Framework

大部分library提供了2个功能在一起：

- test runner
    - Finding the tests
    - Open browser to execute the test script
    - capture the results

- test framework
    - define the syntax
    - API
    - assert

Angular中，两个是分开的，使用karma作为test runner，而Jasmine作为test framework。

#### karma的架构

karma使用插件方式管理它需要的每个模块，比如我们如果要使用jasmine测试框架，那么我们可以安装`npm install karma-jasmine`，而如果我们要使用chrome去执行我们的测试脚本，我们可以使用`npm install karma-chrome-launcher`

##### Karma Plugins

- Browser launcher
- Testing Framework
- Reporters
- Integrations

##### Karma Config

```javascript
// Karma configuration

module.exports = function (config) {
    config.set({
        basePath: '',
        frameworks: ['jasmine'],
        files: [
            'angular.min.js',
            'angular-mock.js',
            'controller.js',
            'simpleSpec.js',
            'controllerSpec.js'
        ],
        excludes: [],
        port: 8080,
        logLevel: config.LOG_INFO,
        autoWatch: true,
        browser: ['Chrome'],
        singleRun: false
    });
};
```

可以通过`karma init`交互式完成karma的配置，最后生成karma.conf.js。

##### Jasmine：Spec

```javascript
describe('Controller: ListCtrl', function () {
    beforeEach(
        module('notesApp')
    );

    var ctrl;

    beforeEach(
        inject(
            function ($controller) {
                ctrl = $controller('ListCtrl');
            }
        )
    );

    it('should have items available on load', function () {
        expect(ctrl.items).toEqual(
            [
                {'id': 1, label: 'First', done: true},
                {'id': 2, label: 'Second', done: false}
            ]
        )
    });

    it('should have highlight items based on state', function () {
        var item = {id: 1, label: 'First', done: true};
        var actualClass = ctrl.getDoneClass(item);
        expect(actualClass.finished).toBeTruthy();
        expect(actualClass.unfinished).toBeFalsy();

        item.done = false;
        actualClass = ctrl.getDoneClass(item);
        expect(actualClass.finished).toBeFalsy();
        expect(actualClass.unfinished).toBeTruthy();
    });
});
```

### Angular Service

- one-way data binding
    - `{% raw %}{{}}{% endraw %}` notation
    - ng-bind directive
- two-way data binding
    - ng-model

```html
<!DOCTYPE html>
<html lang="en" ng-app="notesApp">
<head>
    <meta charset="UTF-8">
    <title>Notes App</title>
</head>
<body ng-controller="MainCtrl as ctrl">

<label>
    <input type="text" ng-model="ctrl.username">
</label>
You typed {{ctrl.username}}

</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module('notesApp', [])
            .controller('MainCtrl', [
                function () {
                    this.username = 'nothing';
                }
            ]);
</script>
</html>
```

解析：

- 这里使用了ng-model directive
- 如果要更新UI里面的值，只需要更新controller里的值


稍微复杂点的例子：

```html
<!DOCTYPE html>
<html lang="en" ng-app="notesApp">
<head>
    <meta charset="UTF-8">
    <title>Notes App</title>
</head>
<body ng-controller="MainCtrl as ctrl">
<input type="text" ng-model="ctrl.username" title="Username"/>
<input type="password" ng-model="ctrl.password" title="Password"/>

<button ng-click="ctrl.change()">Change Value</button>
<button ng-click="ctrl.submit()">Submit</button>

</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module('notesApp', [])
            .controller('MainCtrl', [
                function () {
                    var self = this;
                    self.change = function () {
                        self.username = 'changed';
                        self.password = 'password';
                    };
                    self.submit = function () {
                        console.log('User clicked submit with', self.username, self.password);
                    };
                }
            ]);
</script>
</html>
```

解析：这里我们更加能够详细看清楚UI的更新实际是全在controller中间完成的，通过controller更新model里面的值，而具体更新的执行时不需要用户参与的，用户只关心model。

#### Forms

```html
<!DOCTYPE html>
<html lang="en" ng-app="notesApp">
<head>
    <meta charset="UTF-8">
    <title>Notes App</title>
</head>
<body ng-controller="MainCtrl as ctrl">
<form ng-submit="ctrl.submit()">
    <input type="text" ng-model="ctrl.user.username" title="User Name"/>
    <input type="text" ng-model="ctrl.user.password" title="User Password"/>
    <input type="submit" value="Submit"/>
</form>
</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module('notesApp', [])
            .controller('MainCtrl', [
                function () {
                    var self = this;
                    self.submit = function () {
                        console.log('User clicked submit with', self.user);
                    };
                }
            ]);
</script>
</html>
```

解析：

- 使用了ng-submit directive在form标签里
- controller里面不存在的对象，由angular在需要时动态创建

#### Form Validation and States

```html
<!DOCTYPE html>
<html lang="en" ng-app="notesApp">
<head>
    <meta charset="UTF-8">
    <title>Notes App</title>
</head>
<body ng-controller="MainCtrl as ctrl">
<form ng-submit="ctrl.submit()" name="myForm">
    <input type="text" ng-model="ctrl.user.username" title="User Name"
           required
           ng-minlength="4"/>
    <input type="text" ng-model="ctrl.user.password" title="User Password" required/>
    <input type="submit" value="Submit" ng-disabled="myForm.$invalid"/>
</form>
</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module('notesApp', [])
            .controller('MainCtrl', [
                function () {
                    var self = this;
                    self.submit = function () {
                        console.log('User clicked submit with', self.user);
                    };
                }
            ]);
</script>
</html>
```

解析：

- 引用了一个form的名字“myForm”
- HTML5的validation tag，`required`
- validator: `ng-minlength`
- ng-disabled directive，基于后面的参数（`form state`）的返回值

##### Form State

- $invalid
- $valid
- $pristine，angular的初始状态，用以判定用户是否有输入动作
- $dirty，$pristine反向，用户已经修改了某些状态，但是没有提交
- $error

##### Built-in validators

- required
- ng-required，后面跟参数可以动态required
- ng-minlength
- ng-maxlength
- ng-pattern
- type=`"email"`
- type=`"date"`
- type=`"url"`


##### Display Error Message

```html
<!DOCTYPE html>
<html lang="en" ng-app="notesApp">
<head>
    <meta charset="UTF-8">
    <title>Notes App</title>
    <style>
        input {
            display: block;
        }
    </style>
</head>
<body ng-controller="MainCtrl as ctrl">
<form ng-submit="ctrl.submit()" name="myForm">
    <input type="text"
           ng-model="ctrl.user.username"
           title="User Name"
           name="uname"
           required
           ng-minlength="4"/>
    <!--<span ng-show="myForm.uname.$error.required">This is required field</span>-->
    <span ng-show="myForm.uname.$error.required">
 This is a required field
 </span>
    <span ng-show="myForm.uname.$error.minlength">Minimum length required is 4</span>
    <span ng-show="myForm.uname.$invalid">This field is invalid</span>

    <input type="text"
           ng-model="ctrl.user.password"
           title="User Password"
           required
           name="pwd"/>
    <span ng-show="myForm.pwd.$error.required">This is required field</span>

    <input type="submit" value="Submit" ng-disabled="myForm.$invalid"/>
</form>
</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module('notesApp', [])
            .controller('MainCtrl', [
                function () {
                    var self = this;
                    self.submit = function () {
                        console.log('User clicked submit with', self.user);
                    };
                }
            ]);
</script>
</html>
```

解析：

- 使用了ng-show directive
- ng-show directive用来显示DOM基于后面的参数的返回值，其他跟前面是一样样的

##### Form Styling and States

| state     | css         |
|:----------|:------------|
| $invalid  | ng-invalid  |
| $valid    | ng-valid    |
| $pristine | ng-pristine |
| $dirty    | ng-dirty    |



| state     | css                | invalid css          |
|:----------|:-------------------|:---------------------|
| required  | ng-valid-required  | ng-invalid-required  |
| min       | ng-valid-min       | ng-invalid-min       |
| max       | ng-valid-max       | ng-valid-max         |
| minlength | ng-valid-minlength | ng-invalid-minlength |
| maxlength | ng-valid-maxlength | ng-invalid-maxlength |
| pattern   | ng-valid-pattern   | ng-invalid-pattern   |
| url       | ng-valid-url       | ng-invalid-url       |
| email     | ng-email-valid     | ng-invalid-email     |
| date      | ng-date-valid      | ng-invalid-date      |
| number    | ng-number-valid    | ng-invalid-number    |


```html
<!DOCTYPE html>
<html lang="en" ng-app="notesApp">
<head>
    <meta charset="UTF-8">
    <title>Notes App</title>
    <style>
        input {
            display: block;
        }

        .username.ng-valid {
            background-color: green;
        }

        .username.ng-dirty.ng-invalid-required {
            background-color: red;
        }

        .username.ng-dirty.ng-invalid-minlength {
            background-color: lightpink;
        }
    </style>
</head>
<body ng-controller="MainCtrl as ctrl">
<form ng-submit="ctrl.submit()" name="myForm">
    <input type="text"
           ng-model="ctrl.user.username"
           title="User Name"
           name="uname"
           class="username"
           required
           ng-minlength="4"/>
    <!--<span ng-show="myForm.uname.$error.required">This is required field</span>-->
    <span ng-show="myForm.uname.$error.required">
 This is a required field
 </span>
    <span ng-show="myForm.uname.$error.minlength">Minimum length required is 4</span>
    <span ng-show="myForm.uname.$invalid">This field is invalid</span>

    <input type="text"
           ng-model="ctrl.user.password"
           title="User Password"
           required
           name="pwd"/>
    <span ng-show="myForm.pwd.$error.required">This is required field</span>

    <input type="submit" value="Submit" ng-disabled="myForm.$invalid"/>
</form>
</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module('notesApp', [])
            .controller('MainCtrl', [
                function () {
                    var self = this;
                    self.submit = function () {
                        console.log('User clicked submit with', self.user);
                    };
                }
            ]);
</script>
</html>
```

解析：

这里对input标签增加了class，然后在style块中定义具体的与ng的state有关的次级样式，很简单

#### Nested Forms

```html
<!DOCTYPE html>
<html lang="en" ng-app="notesApp">
<head>
    <meta charset="UTF-8">
    <title>Notes App</title>
</head>
<body ng-controller="MainCtrl as ctrl">
<form ng-submit="ctrl.submit()" name="myForm">
    <input type="text"
           ng-model="ctrl.user.username"
           placeholder="User Name"
           name="uname"
           class="username"
           required
           ng-minlength="4"/>

    <input type="text"
           ng-model="ctrl.user.password"
           title="User Password"
           class="password"
           required
           name="pwd"/>

    <ng-form name="profile">
        <input type="text"
               name="firstName"
               ng-model="ctrl.user.profile.firstName"
               placeholder="First Name"
               required>
        <input type="text"
               name="middleName"
               ng-model="ctrl.user.profile.middleName"
               placeholder="Middle Name"
               required>
        <input type="text"
               name="lastName"
               ng-model="ctrl.user.profile.lastName"
               placeholder="Last Name"
               required>
        <input type="date" name="dob" placeholder="Date of Birth" ng-model="ctrl.user.profile.lastName">
    </ng-form>

    <span ng-show="myForm.profile.$invalid">
        Please fill out the profile information
    </span>

    <input type="submit" value="Submit" ng-disabled="myForm.$invalid"/>
</form>
</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module('notesApp', [])
            .controller('MainCtrl', [
                function () {
                    var self = this;
                    self.submit = function () {
                        console.log('User clicked submit with', self.user);
                    };
                }
            ]);
</script>
</html>
```

解析：这里使用了`ng-form` directive，后面的validator的验证是基于内嵌的form整体的state

#### Other Form Controls

- TextArea
    - `<textarea ng-model="ctrl.user.address" required></textarea>`

- CheckBox
    - `<input type="checkbox" ng-model="ctrl.user.agree">`
    - `<input type="checkbox" ng-model="ctrl.user.agree" ng-true-value="YES" ng-false-value="NO">`

- RadioButton

```html
<div ng-init="user = {gender: 'female'}">
    <input type="radio"
            name="gender"
            ng-model="user.gener"
            value="male">
    <input type="radio"
            name="gender"
            ng-model="user.gender"
            value="female">
</div>
```

解析： 这里就是使用传统的HTML对radiobutton的处理方法

如果我们要想使用dynamic的raidobutton值，那么可以使用：

```html
<input type="radio"
        name="gender"
        ng-model="user.gender"
        ng-value="otherGender">
```

解析：这里使用了ng-value directive，对应的值是angular的expression，可以从controller中拿到，然后用在radiobutton的值中

- Combo Box vs Dropdown

```html
<div ng-init="location = 'India'">
    <select ng-model="location">
        <option value="USA">USA</option>
        <option value="India">India</option>
        <option value="Other">None of the above</option>
    </select>
</div>
```

解析：没有特别之处，就是传统的HTML

```html
<!DOCTYPE html>
<html lang="en" ng-app="notesApp">
<head>
    <meta charset="UTF-8">
    <title>Notes App</title>
</head>
<html ng-app="notesApp">
<head><title>Notes App</title></head>
<body ng-controller="MainCtrl as ctrl">
<div>
    <select ng-model="ctrl.selectedCountryId"
            ng-options="c.id as c.label for c in ctrl.countries" title="country ID">
    </select>
    Selected Country ID: {{ctrl.selectedCountryId}}
</div>

<div>
    <select ng-model="ctrl.selectedCountry"
            ng-options="c.label for c in ctrl.countries" title="country">
    </select>
    Selected Country: {{ctrl.selectedCountry.label}}
</div>
</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module("notesApp", [])
            .controller("MainCtrl", [
                function () {
                    this.countries = [
                        {label: 'USA', id: 1},
                        {label: 'India', id: 2},
                        {label: 'Other', id: 3}
                    ];
                    this.selectedCountryId = 2;
                    this.selectedCountry = this.countries[1];
                }
            ]);
</script>
</html>
```

解析：使用了动态的值去填充selection options，这里的取值方法类似于ng-repeat或者python中的unpack expression。有一个特别的地方是`c.id as c.label`，这里的意思是显示在UI中的值是`c.label`，而实际存储的值是`c.id`

### Services

- Angular Service 是javascript对象或者function
- Angular Service提供跨应用的状态和行为
- Angular Service只会被初始化一次，单例的
- 主要提供的功能包括：
    - repeated behaviour
    - shared state
    - cache
    - factories
    - provider

```html
<!DOCTYPE html>
<!--suppress HtmlUnknownTarget -->
<html lang="en" ng-app="notesApp">
<head>
    <script src="../../bower_components/angular/angular.js"></script>
    <script src="app.js"></script>
    <meta charset="UTF-8">
    <title>Notes App</title>
</head>
<body ng-controller="MainCtrl as mainCtrl">
<h1>Hello controllers!</h1>
<button ng-click="mainCtrl.open('first')">Open First</button>
<button ng-click="mainCtrl.open('second')">Open Second</button>
<div ng-switch on="mainCtrl.tab">
    <div ng-switch-when="first">
        <div ng-controller="SubCtrl as ctrl">
            <h3>First Tab</h3>
            <ul>
                <li ng-repeat="item in ctrl.list">
                    <span ng-bind="item.label"></span>
                </li>
            </ul>

            <button ng-click="ctrl.add()">Add More Items</button>
        </div>
    </div>
    <div ng-switch-when="second">
        <div ng-controller="SubCtrl as ctrl">
            <h3>Second Tab</h3>
            <ul>
                <li ng-repeat="item in ctrl.list">
                    <span ng-bind="item.label"></span>
                </li>
            </ul>
            <button ng-click="ctrl.add()">Add More Items</button>
        </div>
    </div>
</div>
</body>
</html>
```

解析：

- 一个新的angular directive，`ng-switch`和`ng-switch-when`
    - 类似于通常编程语言的switch遍历结构
- 使用了2个controller，一个scope实在body元素里，而另外一个子controller是在一个div元素里，负责item的展示与增添
- 由于两个div初始化了2个不同的controller，所以这里每次切换tab时，原来的数据都会丢失，因为controller对象是不同的


#### Services vs Controller

angular里面的service跟通常意义上的service不同，angular的service包括factories、services和providers。

| controller                                                                                                                          | Service                                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------|
| Presentation Logic                                                                                                                  | Business Logic                                                                                                     |
| Directly linked to a view                                                                                                           | Independent of View                                                                                                |
| Driver the UI                                                                                                                       | Drive the application                                                                                              |
| One-off,specific                                                                                                                    | Reusable                                                                                                           |
| Responsible for decisions like what data to fetch, what data to show, how to handle user interaction, and styling and display of UI | Responsible for making server call, common validation logic, application-level stores, and reusable business logic |


#### Dependency Injection in AngularJS

Without dependency injection:

```javascript
function fetchDashboardData() {
    var $http = new HttpService();
    return $http.get('my/url');
}
```

With dependency injection:

```javascript
function fetchDashboardData($http) {
    return $http.get('my/url');
}
```

依赖注射的一个重要的准则就是：依赖的初始化是由自己负责还是外部负责

angular的DI机制保证：

- 我们提供给service declaration的function只会被执行一次（lazily）
- angular service在整个application的scope内都是单例的（signleton）
- 两个不同的service或者controller请求serviceA时，将会永远得到同样的实例

#### Built in AngularJS Service

angular自己提供的一个命名规范：

- 所有built-in的angular service都会以$开头
- 用户自定义的service不要用$开头

```html
<html ng-app="notesApp">
<body ng-controller="MainCtrl as mainCtrl">
<h1>Hello Service</h1>
<button ng-click="mainCtrl.logStuff()">Log Something</button>
</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module("notesApp", [])
            .controller("MainCtrl", ['$log', function ($log) {
                var self = this;
                self.logStuff = function () {
                    $log.log("The button was pressed!");
                };
            }
            ]);
</script>
</html>
```

解析：

- 这里的controller使用了依赖，放在`controller("controllerName", [...,function])`中
- 注入的service是`"$log"`，是一个字符串，然后angular将根据该字符串，寻找对应的service并初始化
- 后面function在argument中引用了注入的service，`$log`
- function中引入的`$log`，可以不用在controller的dependencies中声明，而直接在function中声明时，angular也能够通过DI找到对应的正确的服务，但是这里的问题是，当使用一些`minifyjavascript`服务时，所有的javascript的变量可能被截断，如果`$log`被截断，则angular就不能有效找到真正需要的`$log`服务

##### Order of Injection

```javascript
myModule.controller("MainCtrl",
    ["$log", "$window", function($l, $w) {}]);
```

- 这里的`$l``$w`是与前面的依赖的注射的模块的顺序有关的
- funtion的arguments[0]，代表的是第0个注入的模块，arguments[1]，代表的是第1个注入的模块

##### Common AngularJS Services

- $window
    - global window object wrapper

- $location
    - interact with URL
        - absUrl, `$location.absUrl()`
        - url, getter and setter of URL
        - path, getter and setter the path of URL
        - search
            - $loaction.search()
            - $location.search("test"), remove
            - $location.search("test", "abc"), setter of parameter

- $http
    - XHR, AJAX

#### Create an Own AngularJS Service

AngularJS service criteria:

- It needs to be reusable
- Application level state
- It is independent of the View
- It integrates with 3rd service
- Caching/factories

```html
<html ng-app="notesApp">
<body ng-controller="MainCtrl as mainCtrl">
<h1>Hello Controllers</h1>
<button ng-click="mainCtrl.open('first')">Open First</button>
<button ng-click="mainCtrl.open('second')">Open Second</button>
<div ng-switch on="mainCtrl.tab">
    <div ng-switch-when="first">
        <div ng-controller="SubCtrl as ctrl">
            <h3>First Tab</h3>
            <ul>
                <li ng-repeat="item in ctrl.list()"><span ng-bind="item.label"></span></li>
            </ul>
            <button ng-click="ctrl.add()">Add More Items</button>
        </div>
    </div>
    <div ng-switch-when="second">
        <div ng-controller="SubCtrl as ctrl">
            <h3>Second Tab</h3>
            <ul>
                <li ng-repeat="item in ctrl.list()"><span ng-bind="item.label"></span></li>
            </ul>
            <button ng-click="ctrl.add()">Add More Items</button>
        </div>
    </div>
</div>
</body>
<script src="../../bower_components/angular/angular.js"></script>
<script src="app.js"></script>
</html>
```                                  

app.js

```javascript
angular.module("notesApp", [])
    .controller("MainCtrl", [
        function () {
            var self = this;
            self.tab = "first";
            self.open = function (tab) {
                self.tab = tab;
            };
        }
    ])
    .controller("SubCtrl", ['ItemService', function (ItemService) {
        var self = this;
        self.list = function () {
            return ItemService.list();
        };
        self.add = function () {
            ItemService.add({
                id: self.list().length + 1,
                label: 'Item ' + self.list().length
            });
        };
    }])
    .factory("ItemService", [
        function () {
            var items = [
                {id: 1, label: 'Item 0'},
                {id: 2, label: 'Item 1'}
            ];

            return {
                list: function () {
                    return items;
                },
                add: function (item) {
                    items.push(item);
                }
            }
        }
    ]);
```

解析：

- 使用angular.module().factory去声明service的名字和dependencies
- 具体的实现是放在`[..., function]`的function中，与controller的函数签名类似
- 返回的是一个object或者function，这个对象或者函数变成了service的public API


angular保证：

- service将会被lazily初始化一次
- service是单例的（singleton）

### Difference between Factory, Service, and Provider

当遇到以下情况时使用factory：

- follow function style of programming
- prefer to return function or object

以下情况可以使用service：

- follow OO style programming

```javascript
function ItemService() {
    var items = [
        {id: 1, label: 'Item 0'},
        {id: 2, label: 'Item 1'}
    ];
    this.list = function () {
        return items;
    };
    this.add = function (item) {
        items.push(item);
    };
}


angular.module("notesApp", [])
    .service('ItemService', [ItemService])
    .controller("MainCtrl", [
        function () {
            var self = this;
            self.tab = "first";
            self.open = function (tab) {
                self.tab = tab;
            };
        }
    ])
    .controller("SubCtrl", ['ItemService', function (ItemService) {
        var self = this;
        self.list = function () {
            return ItemService.list();
        };
        self.add = function () {
            ItemService.add({
                id: self.list().length + 1,
                label: 'Item ' + self.list().length
            });
        };
    }]);
```

解析：

- 与factory在格式上差不多
- 具体的实现也是放在`[..., function]`的function中
- 不需要return function或者object
- angular DI在初始化service时，使用了`new`关键词，用来把`this`绑定给初始化后的实例，所以这里不用`self=this`去closure中传递正确的context


以下情况使用provider：

- set up some configuration for our service before application loads
- have function to be called to set up how our service works based on the language, environment, or other things

```javascript
function ItemService(opt_items) {
    var items = opt_items || [];

    this.list = function () {
        return items;
    };

    this.add = function (item) {
        items.push(item);
    };
}


angular.module("notesApp", [])
    .provider('ItemService', function () {
        var haveDefaultItems = true;

        this.disableDefaultItems = function () {
            haveDefaultItems = false;
        };

        this.$get = [function () {
            var optItems = [];
            if (haveDefaultItems) {
                optItems = [
                    {id: 1, label: 'Item 0'},
                    {id: 2, label: 'Item 1'}
                ];
            }
            return new ItemService(optItems);
        }];
    })
    .config(['ItemServiceProvider', function (ItemServiceProvider) {
        var shouldHaveDefaults = true;

        if (!shouldHaveDefaults) {
            ItemServiceProvider.disableDefaultItems();
        }
    }])
    .controller("MainCtrl", [
        function () {
            var self = this;
            self.tab = "first";
            self.open = function (tab) {
                self.tab = tab;
            };
        }
    ])
    .controller("SubCtrl", ['ItemService', function (ItemService) {
        var self = this;
        self.list = function () {
            return ItemService.list();
        };
        self.add = function () {
            ItemService.add({
                id: self.list().length + 1,
                label: 'Item ' + self.list().length
            });
        };
    }]);
```

解析：

- `provider(..., [])`的函数签名跟service/factory是一样的
- `config`通过provider提供的public API对`provider`进一步配置
- 后边controller对provider的引用跟其他factory/service一样


### Server communication via $http

传统的未封装的AJAX：

```javascript
var xmlhttp = new XMLHttpRequest();

xmlhttp.onreadystatechange = function () {
    if (xmlhttp.readystate == 4 && xmlhttp.status == 200) {
        var response = xmlhttp.responseText;
    } else if (xmlhttp.status == 400) {
        //handle error
    }
};

xmlhttp.open("GET", "http://myserver/api", true);
xmlhttp.send();
```                    

关于$http

- core angular service
- communicate with server endpoints via XHR
- Promise interface
    - 保证异步的请求的response一定会被处理
    - 允许promise consumer使用一种可预测的方式使用response

ngResource模块

```javascript
angular.module('resourceApp', ['ngResource'])
    .factory('ProjectService', ['$resource', function($resource) {
        return $resource('/api/project/:id');
    }]);
```

- 调用ProjectService

```javascript
ProjectService.query();
ProjectService.save({id: 15}, projectObj);
ProjectService.get({id: 19});
```

```html
<html ng-app="notesApp">
<head>
    <title>$http get example</title>
    <style>
        .item {
            padding: 10px;
        }
    </style>
</head>
<body ng-controller="MainCtrl as mainCtrl">
<h1>Hello Server</h1>

<div ng-repeat="todo in mainCtrl.items" class="item">
    <div><span ng-bind="todo.label"></span></div>
    <div>- by <span ng-bind="todo.author"></span></div>
</div>
</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module('notesApp', [])
            .controller("MainCtrl", ['$http', function ($http) {
                var self = this;
                self.items = [];
                $http.get('http://localhost:8000/api/note').then(function (response) {
                    self.items = response.data;
                }, function (errResponse) {
                    console.error('Error while fetching notes');
                });
            }]);
</script>
</html>
```

解析：

- controller依赖了$http
- use promise interface
    - successful handler
    - error handler
- response data:
    - headers
    - status
    - config
    - data

### Quick Promise interface

- angularJS are based on `Kris Kowal's Q proposal`
- 传统的javascript支持异步的方式是使用无穷嵌套的callbacks

```javascript
xhrGET('/api/server-config', function(config) {
    xhrGET('/api/' + config.USER_END_POINT, function(user) {
        xhrGET('/api/' + user.id + '/items', function(items) {
            //display the items here
        });
    });
});
```

 The promise API design proposal:

 - each asynchronous task will return a promise object
 - each promise object have a then function which can take two handler(success, fail)
 - success and fail handler will be called only once after the asynchronouse task finished
 - `then` return a promise which can be chained together
 - each (success/fail) handler can return a value, which will be passed to next function in the chain
 - if handler return a promise (make another asynchronouse request), then the next handler will be called only after the previouse request is finished

 ```javascript
 $http.get('api/server-config').then(function(configResponse) {
     return $http.get('/api/' + configResponse.data.USER_END_POINT);
 }).then(function(userResponse) {
     return $http.get('/api/' + userResponse.data.id + '/items');
 }).then(function(itemResponse) {
     //display the items here
 }, function(err) {
     //error handling
 });
 ```

### Angular Propagating Success and Error

假设下面的假想的promise chain:

```javascript
xhrCall()
    .then(S1, E1)
    .then(S2, E2)
    .then(S3, E3)
```    

如果是happy path，那么正确的handler的执行顺序是：

```javascript
S1 -> S2 -> S3
```

Angular基于前面的handler的返回值，决定下一步要执行的handler，因为所有的handler都是由开发者所写，所以可以通过handler的返回值控制下一个handler的执行

- 如果我们想trigger下一个promise的success handler，那么我们可以从当前的success handler或者error handler中返回一个值，然后Angular就会认为前面的promise已经完整地处理了error

- 如果我们想trigger下一个promise的error handler，我们可以使用`$q`service。
    - 需要注册$q为我们的controller或者service的依赖
    - 然后在前一个的handler之一中，返回`$q.reject(data)`     

#### $q service

- $q.defer()
    - 创建一个deferred对象，该deffered对象有一个promise属性，可以用来返回一个promise

- deferredObject.resolve
    - defferred对象可以被resolve（解析），通过在success handler利用传入的argument.resolve()

- deferredObject.reject
    - defferred对象可以被reject（拒绝），然后传入到error handler中去

- $q.reject
    - 可以在error 或者 success的handler中调用，拥有一个可选的参数，该参数是promise chain中的返回的数据，返回值可以保证下一个promise一定会触发error handler


```html
<!DOCTYPE html>
<html lang="en" ng-app="notesApp">
<head>
    <meta charset="UTF-8">
</head>
<body ng-controller="MainCtrl as mainCtrl">
<h1>Hello Server</h1>
<div ng-repeat='todo in mainCtrl.items' class='item'>
    <div><span ng-bind="todo.label"></span></div>
    <div>- by <span ng-bind="todo.author"></span></div>
</div>

<div>
    <form name="addForm" ng-submit="mainCtrl.add()">
        <input type="text"
               placeholder="Label"
               ng-model="mainCtrl.newTodo.label"
               required/>
        <input type="text"
               placeholder="Author"
               ng-model="mainCtrl.newTodo.author"
               required/>
        <input type="submit"
               value="Add"
               ng-disabled="addForm.$invalid"/>
    </form>
</div>
</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module("notesApp", [])
            .controller("MainCtrl", ['$http', function ($http) {
                var self = this;
                self.items = [];
                self.newTodo = {};

                var fetchTodos = function () {
                    return $http.get('http://localhost:8000/api/note').then(
                            function (response) {
                                self.items = response.data;
                            },
                            function (errorResponse) {
                                console.error("Error while fetching notes")
                            }
                    );
                };

                fetchTodos();

                self.add = function () {
                    $http.post('http://localhost:8000/api/note', self.newTodo)
                            .then(fetchTodos)
                            .then(function (response) {
                                self.newTodo = {};
                            });
                };
            }]);
</script>
</html>
```

解析：

- $http.post，传入了一个额外的参数`self.newTodo`，postData


#### $http API

- GET
- HEAD
- POST
- DELETE
- PUT
- JSONP

$http.get(url, config)可以表示为$http(config)

#### Configuration

```html
    method: string,
    url: string,
    params: object,
    data: string or object,
    headers: object,
    xsrfHeaderName: string,
    xsrfCookieName: string,
    transformRequest: function transform(data, headersGetter) or an array of functions,
    transformResponse: function transform(data, headersGetter) or an array of functions,
    cache: boolean or Cache object,
    timeout: number,
    withCredentials: boolean
}
```

#### Advanced $http

Configuring $http Defaults

```javascript
angular.module('notesApp', [])
    .controller('LogicCtrl', ['$http', function ($http) {
        var self = this;
        self.user = {};
        self.message = 'Please login';
        self.login = function () {
            $http.post('/api/login', self.user)
                .then(
                    function (resp) {
                        self.message = resp.data.msg;
                    }
                );
        };
    }])
    .config(['$httpProvider', function ($httpProvider) {
        $httpProvider.defaults.transformRequest.push(
            function (data) {
                var requestStr;
                if (data) {
                    data = JSON.parse(data);
                    for (var key in data) {
                        if (requestStr) {
                            requestStr += '&' + key + '=' + data[key];
                        } else {
                            requestStr = key + '=' + data[key];
                        }
                    }
                }
                return requestStr;
            }
        );

        $httpProvider.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
    }]);
```

解析：

- application-level configuration
- config section for module
- injecting $httpProvider
- global request transformer
    - change all of outbound request post data from JSON into jQuery post string format
    - then push the transformer into default transformers
    - 这里的transformer类似于django里面的middleware


##### $httpProvider.defaults

    - headers.common
    - headers.get
    - headers.put
    - headers.post
    - transformRequest
    - transformResponse
    - xsrfHeaderName
    - xsrfCookieName

### Interceptors

经典的Java编程中的纵切（SOA）概念，主要用来处理：

- request level actions
    - logging
    - authentication check
    - handling certain response

AngularJS使用$httpProvider去提供interceptor：

- hook and check each request and response
- handle certain events (403 for authentication issue)

```javascript
angular.module('notesApp', [])
    .controller('MainCtrl', ['$http', function ($http) {
        var self = this;
        self.items = [];
        self.newTodo = {};

        var fetchTodos = function () {
            return $http.get('http://localhost:8000/api/note').then(function (response) {
                self.items = response.data;
            }, function (errResponse) {
                console.log('Error while fetching notes');
            });
        };

        fetchTodos();

        self.add = function () {
            $http.post('http://localhost:8000/api/note', self.newTodo)
                .then(fetchTodos)
                .then(function (response) {
                    self.newTodo = {};
                });
        };
    }])
    .factory('MyLoggingInterceptor', ['$q', function ($q) {
        return {
            request: function (config) {
                console.log('Request made with ', config);
                return config;
            },
            requestError: function (rejection) {
                console.log('Request error due to ', rejection);
                return $q.reject(rejection);
            },
            response: function (response) {
                console.log('Response from server', response);
                return response || $q.when(response);
            },
            responseError: function (rejection) {
                console.log('Error in response ', rejection);
                return $q.reject(rejection);
            }
        };
    }])
    .config(['$httpProvider', function ($httpProvider) {
        $httpProvider.interceptors.push('MyLoggingInterceptor');
    }]);
```


解析：

- implement the interceptor with factory
- request
    - any outgoing request passes through the request function
- requestError
    - triggered if multiple interceptors and one of them rejected the request going out
- response
    - server eventually returns
- responseError
    - server return with non-200 series status code

#### Best Practices for $http

- wrap $http in service

```javascript
angular.module('notesApp', [])
    .factory('NoteService', ['$http', function ($http) {
        return {
            query: function () {
                return $http.get('/api/notes');
            }
        };
    }]);
```

- use interceptor

```javascript
angular.module('notesApp', [])
    .factory('AuthInterceptor', ['AuthInterceptor', '$q', function(AuthInfoService, $q) {
        return {
            request: function (config) {
                if (AuthInfoService.hasAuthHeader()) {
                    config.headers['Authorization'] = AuthInfoService.getAuthHeader();
                }
                return config;
            },
            responseError: function (responseRejection) {
                if (responseError.status == 403) {
                    AuthInfoService.redirectToLogin();
                }
                return $q.reject(responseRejection);
            }
        };
    }])
    .config(['$httpProvider', function ($httpProvider) {
        $httpProvider.interceptors.push('AuthInterceptor');
    }]);
```

- chain interceptor
    - 创建多个小的interceptor，每个interceptor只有单一的职责

- leverage default
    - 如果endpoint返回的xml，我们可以增加一个缺省的transformResponse到$httpProvider，然后这个transformResponse可以转化xml为json。


###Unit Testing Service and XHRs

controller:

```javascript
angular.module('notesApp', [])
    .controller('SimpleCtrl', ['$location', function ($location) {
        var self = this;
        self.navigate = function () {
            $location.path('/some/where/else');
        }
    }]);
```

conf:

```javascript
module.exports = function (config) {
    config.set({
        basePath: '',
        frameworks: ['jasmine'],
        files: [
            '../bower_components/angular/angular.min.js',
            '../bower_components/angular-mocks/angular-mocks.js',
            '*.js'
        ],
        exclude: [],
        port: 8080,
        logLevel: config.LOG_INFO,
        autoWatch: true,
        browsers: ['Chrome'],
        singleRun: false
    });
};
```

spec:

```javascript
describe('SimpleCtrl', function () {
    beforeEach(module('notesApp'));

    var ctrl, $loc;

    beforeEach(inject(function($controller, $location) {
        ctrl = $controller('SimpleCtrl');
        $loc = $location;
    }));

    it('should navigate away from the current page', function () {
        $loc.path('/here');
        ctrl.navigate();
        expect($loc.path()).toEqual('/some/where/else');
    });
});
```

解析：

我们这里使用angular-mocks模块去模拟$location service

#### State across Unit Tests

simpleCtrl2.js:

```javascript
angular.module('simpleCtrl2App', [])
    .controller('SimpleCtrl2', ['$location', '$window', function ($location, $window) {
        var self = this;
        self.navigate1 = function () {
            $location.path('/some/where');
        };

        self.navigate2 = function () {
            $location.path('/some/where/else');
        };
    }]);

```

simpleCtrl2Spec.js:

```javascript
describe('SimpleCtrl2', function () {
    beforeEach(module('simpleCtrl2App'));

    var ctrl, $loc;

    beforeEach(inject(function ($controller, $location) {
        ctrl = $controller('SimpleCtrl2');
        $loc = $location;
    }));

    it('should navigate away from the current page', function () {
        expect($loc.path()).toEqual('');
        $loc.path('/here');
        ctrl.navigate1();
        expect($loc.path()).toEqual('/some/where');
    });

    it('should navigate away from the current page', function () {
        expect($loc.path()).toEqual('');
        $loc.path('/there');
        ctrl.navigate2();
        expect($loc.path()).toEqual('/some/where/else');
    });

});
```

解析：

- 在一个真实的live的浏览器中，$location是全局可见的，后面的navigation将会看到前面的path
- 在angularJS的unit testing中，$location在每一个测试方法（test method）中都会被destory，然后重新mock一个新的

#### Mock Out Service

noteApp1.js

```javascript
angular.module('notesApp1', [])
    .factory('ItemService', [function () {
        var items = [
            {id: 1, label: 'Item 0'},
            {id: 2, label: 'Item1'}
        ];

        return {
            list: function () {
                return items;
            },
            add: function () {
                items.push(item);
            }
        };
    }])
    .controller('ItemCtrl', ['ItemService', function (ItemService) {
        var self = this;
        self.items = ItemService.list();
    }]);
```

notesApp1Spec.js

```javascript
describe('ItemCtrl with inline mock', function () {
    beforeEach(module('notesApp1'));

    var ctrl, mockService;

    beforeEach(module(function($provide) {
        mockService = {
            list: function () {
                return [{id: 1, label: 'Mock'}];
            }
        };
        $provide.value('ItemService', mockService);
    }));

    beforeEach(inject(function($controller) {
        ctrl = $controller('ItemCtrl');
    }));

    it('should load mocked out items', function () {
        expect(ctrl.items).toEqual([{id: 1, label: 'Mock'}]);
    });
});
```

解析：

- with our own mock to override ItemService
- use module function to mock service
- we pass a function to `module function`
- the passed function is injected with $provide
- use provider to overrid the original service with mocked service

如果我们需要reuse mocked service，我们可以在global level的mock：

```javascript
angular.module('notesApp1Mocks', [])
.factory('ItemService', [function () {
    return {
        list: function () {
            return [{id: 1, label: 'Mock'}];
        }
    }
}]);
```

```javascript
describe('ItemCtrl with global mock', function () {
    var ctrl;
    beforeEach(module('notesApp1'));
    beforeEach(module('notesApp1Mocks'));

    beforeEach(inject(function($controller) {
        ctrl = $controller('ItemCtrl');
    }));

    it('should load mocked out items', function () {
        expect(ctrl.items).toEqual([{id: 1, label: 'Mock'}]);
    });
});
```

### Jasmine Spies

这个库类似于Python中的inspect，用来窥视函数在运行时内部的状态统计：

- 是否被call
- 被call多少次
- 用到了什么参数

```javascript
describe('ItemCtrl with spies', function () {
    beforeEach(module('notesApp1'));

    var ctrl, itemService;

    beforeEach(inject(function($controller, ItemService) {
        spyOn(ItemService, 'list').and.callThrough();
        itemService = ItemService;
        ctrl = $controller('ItemCtrl');
    }));

    it('should load mocked out items', function () {
        expect(itemService.list).toHaveBeenCalled();
        expect(itemService.list.calls.count()).toEqual(1);
        expect(ctrl.items).toEqual([
            {id: 1, label: 'Item 0'},
            {id: 2, label: 'Item 1'}
        ]);
    });
});
```

解析： 这个jasmine spies其实就是angular的instrumentation，然后在assert中调用instrumentation中采集到的特性。

```javascript
describe('ItemCtrl with SpyReturn', function () {
    beforeEach(module('notesApp1'));

    var ctrl, itemService;

    beforeEach(inject(function ($controller, ItemService) {
        spyOn(ItemService, 'list')
            .and.returnValue([{id: 1, label: 'Mock'}]);

        itemService = ItemService;
        ctrl = $controller('ItemCtrl');
    }));

    it('should load mocked out items', function () {
        expect(itemService.list).toHaveBeenCalled();
        expect(itemService.list.calls.count()).toEqual(1);
        expect(ctrl.items).toEqual([{id: 1, label: 'Mock'}]);
    });
});
```

解析：

- 这里通过Jasmine Spies提供了mock库里最常见的功能，就是模拟返回值

#### Unit Testing Server Call

angular的mock库中，所有$http服务都是local，context都是unit test自己的环境，而不会发生任何server call，所有的server call都会被intercepted。

```javascript
angular.module('serverApp', [])
.controller('MainCtrl', ['$http', function($http) {
    var self = this;
    self.items = [];
    self.errorMessage = "";

    $http.get('/api/note').then(function(response) {
        self.items = response.data;
    }, function(errResponse) {
        self.errorMessage = errResponse.data.msg;
    });
}]);
```

```javascript
describe('MainCtrl Server Call', function () {
    beforeEach(module('serverApp'));

    var ctrl, mockBackend;

    beforeEach(inject(function ($controller, $httpBackend) {
        mockBackend = $httpBackend;
        mockBackend.when('GET', '/api/note').respond([{id: 1, label: 'Mock'}]);

        ctrl = $controller('MainCtrl');
    }));

    it('should load items from server', function () {
        expect(ctrl.items).toEqual([]);
        mockBackend.flush();
        expect(ctrl.items).toEqual([{id: 1, label: 'Mock'}]);
    });

    afterEach(function () {
        mockBackend.verifyNoOutstandingExpectation();
        mockBackend.verifyNoOutstandingRequest();
    })
});
```

解析：

- 使用了angular-mocks.js中的$httpBackend
    - expect
        - it is used when we want to control exactly how many requests, what URLs and then control the response
        - fine-grained
    - when
        - when does not care about the order of request, or how many times
        - coarse-grained

- verifyNoOutstandingExpectations()，检查是否在$httpBackend中设定了某些expect，并没有被test所满足
- verifyNoOutstandingRequest()，检查所有的server call都被flush了


### Integration Level Unit Tests

基于良好的习惯，我们没有把$http call放在controller中，而是放在服务中（NoteService），我们的controller然后delegate到服务中去获取服务器内容，我们有两个选项：

- mock out NoteService，然后保证我们的controller对service的调用被delegate到mock到NoteService中去
- integration level unit testing，这里主要是使用$httpBackend去mock out整个后端

```javascript
describe('Server App Integration', function () {
    beforeEach(module('serverApp2'));

    var ctrl, mockBackend;

    beforeEach(inject(function($controller, $httpBackend) {
        mockBackend = $httpBackend;
        mockBackend.expect('GET', '/api/note')
            .respond(404, {msg: 'Not Found'});
        ctrl = $controller('MainCtrl');
    }));

    it('should handle error while loading items', function () {
        expect(ctrl.items).toEqual([]);
        mockBackend.flush();
        expect(ctrl.items).toEqual([]);
        expect(ctrl.errorMessage).toEqual('Not Found');
    });

    afterEach(function () {
        mockBackend.verifyNoOutstandingExpectation();
        mockBackend.verifyNoOutstandingRequest();
    });
});
```

解析：

easy case。就是模拟了一个服务的返回值。


----------------------------------------------

### Working with Filters

AngularJS 的 filter 是用来处理数据，然后格式化数据后呈现给用户。Filter可以被用在HTML的标签上，或者直接用在controller或者service上。大多数情况，filter是最后的一层，在把数据最终展示在用户面前。

Filter经常出现的情景：

- 格式化显示一个timestamp
- 增加货币符号在结算货币的数字前面
- 在view层提供动态生成的数据，对这些数据的修改不会原始值

```html
<html>
<head><title>Filter in Action</title></head>
<body ng-app="filterApp">
<div ng-controller="FilterCtrl as ctrl">
    <div>Amount as a number: {{ ctrl.amount | number}}</div>
    <div>Total cost as a currency: {{ctrl.totalCost | currency}}</div>
    <div>Total cost in INR: {{ctrl.totalCost | currency:'INR'}}</div>
    <div>Shouting the name: {{ctrl.name | uppercase}}</div>
    <div>Whispering the name: {{ctrl.name | lowercase}}</div>
    <div>Start Time: {{ctrl.startTime | date:'medium'}}</div>
</div>
</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module('filterApp', [])
            .controller('FilterCtrl', [function () {
                this.amount = 1024;
                this.totalCost = 4906;
                this.name = 'Shyam Seshadri';
                this.startTime = new Date().getTime();
            }]);
</script>
</html>
```

解析：这里的filter主要用在HTML的element中，很简单，跟django的template里的filter用法差不多，`{%raw%}{{expression | filter}}{%endraw%}`

Filter用法：

```
{{expression | filter}}
```

```
{{expression | filter1 | filter2}}
```

```
filterLiteral:argument
```

Common Filters:

- currency
- number
- lowercase
- uppercase
- json
- date

```html
<html>
<head><title>Filter in Action</title></head>
<body ng-app="filterApp">
<div ng-controller="FilterCtrl as ctrl">
    <div>Amount - {{ctrl.amount}}</div>
    <div>Amount - default currency: {{ctrl.amount | currency}}</div>
    <div>Amount - INR currency {{ctrl.amount | currency:'INR'}}</div>
    <div>Amount - number {{ctrl.amount | number}}</div>
    <div>Amount - number with 4 decimals: {{ctrl.amount | number:4}}</div>
    <div>Name without filters: {{ctrl.name}}</div>
    <div>Name - lowercase filter: {{ctrl.name | lowercase}}</div>
    <div>Name - uppercase filter: {{ctrl.name | uppercase}}</div>
    <div>The JSON Filter: {{ctrl.obj | json}}</div>
    <div>Timestamp: {{ctrl.startTime | date}}</div>
    <div>Timestamp: {{ctrl.startTime | date:'medium'}}</div>
    <div>Timestamp: {{ctrl.startTime | date: 'M/dd, yyyy'}}</div>
</div>
</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module('filterApp', [])
            .controller('FilterCtrl', [function () {
                this.amount = 1024;
                this.totalCost = 4906;
                this.obj = {test: 'value', number: 123};
                this.startTime = new Date().getTime();
            }]);
</script>
</html>
```

- limitTo
    - return subarray or substring

- orderBy
    - take an array ordered by a predicate expression
    - second optional argument determine whether the result should be reversed
    - `+`, `-` sign before the field name(key) to sort ascending or descending
    - pass a function as argument, the value return by function will be used for comparation

- filter
    - filter an array by predicate or functions
    - used along with the ng-repeat
    - can be applied to:
        - string
        - object
        - function

```html
<html>
<head><title>Filters in Action</title></head>
<body ng-app="filterApp">
<div ng-controller="FilterCtrl as ctrl">
    <button ng-click="ctrl.currentFilter = 'string'">Filter with String</button>
    <button ng-click="ctrl.currentFilter = 'object'">Filter with Object</button>
    <button ng-click="ctrl.currentFilter = 'function'">Filter with Function</button>
    Filter Text
    <input type="text" ng-model="ctrl.filterOptions['string']" placeholder=""/>
    Show Done Only
    <input type="checkbox" ng-model="ctrl.filterOptions['object'].done" placeholder=""/>

    <ul>
        <li ng-repeat="note in ctrl.notes | filter:ctrl.filterOptions[ctrl.currentFilter] | orderBy:ctrl.sortOrder | limitTo:5">
            {{note.label}} - {{note.type}} - {{note.done}}
        </li>
    </ul>
</div>
</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module("filterApp", [])
            .controller('FilterCtrl', [function () {
                this.notes = [
                    {label: 'FC todo', type: 'chore', done: false},
                    {label: 'FT todo', type: 'task', done: true},
                    {label: 'FF todo', type: 'fun', done: true},
                    {label: 'SC todo', type: 'chore', done: false},
                    {label: 'ST todo', type: 'task', done: true},
                    {label: 'SF todo', type: 'fun', done: true},
                    {label: 'TC todo', type: 'chore', done: true},
                    {label: 'TT todo', type: 'task', done: false},
                    {label: 'TF todo', type: 'fun', done: false}
                ];
                this.sortOrder = ['+type', '-label'];

                this.filterOptions = {
                    "string": '',
                    "object": {done: false, label: 'C'},
                    "function": function (note) {
                        return note.type === 'task' && note.done === false;
                    }
                };

                this.currentFilter = 'string';
            }]);
</script>
</html>
```

解析：

- string: 基于object的key
- object: more fine-grained criteria
- function: based on return value: true | false

#### Use Filter in Controller and Services

- 任何filter（内生或者自建），都可以注入service或者controller
- 需要增加一个affix

```
angular.module('myModule', [])
    .controller('MyCtrl', ['currencyFilter',
        function(currencyFilter) {
            // do stuff
        }
    ]);
```

示例：

```
self.filterArray = filterFilter(self.notes, 'ch');
```

- filterFilter表明要使用的filter是filter函数, 其中`Filter`是后缀`suffix`
- 1st argument is the data act upon
- further argument are filter needs
- return value is the filter result that we need

#### Create own filter

```html
<html>
<head><title>Custom Filters in Action</title></head>
<body ng-app="filterApp">
<div ng-controller="FilterCtrl as ctrl">
    <div>Start Time (TimeStamp): {{ctrl.startTime}}</div>
    <div>Start Time (DateTime): {{ctrl.startTime | date: 'medium'}}</div>
    <div>Start Time (Our Filter): {{ctrl.startTime | timeAgo}}</div>
    <div>someTimeAgo: {{ctrl.someTimeAgo | date:'short'}}</div>
    <div>someTimeAgo (Our Filter): {{ctrl.someTimeAgo | timeAgo}}</div>
</div>
</body>
<script src="../bower_components/angular/angular.js"></script>
<script>
    angular.module('filterApp', [])
            .controller('FilterCtrl', [function () {
                this.startTime = new Date().getTime();
                this.someTimeAgo = new Date().getTime() - (1000 * 60 * 60 * 4);
            }])
            .filter('timeAgo', [function () {
                var ONE_MINUTE = 1000 * 60;
                var ONE_HOUR = ONE_MINUTE * 60;
                var ONE_DAY = ONE_HOUR * 24;
                var ONE_MONTH = ONE_DAY * 30;

                return function (ts) {
                    var currentTime = new Date().getTime();
                    var diff = currentTime - ts;
                    if (diff < ONE_MINUTE) {
                        return 'seconds ago';
                    } else if (diff < ONE_HOUR) {
                        return 'minutes ago'
                    } else if (diff < ONE_DAY) {
                        return 'hours ago';
                    } else if (diff < ONE_MONTH) {
                        return 'days ago';
                    } else {
                        return 'months ago';
                    }
                };
            }]);
</script>
</html>
```

解析：

- 我们创建的filter返回了一个function，这个function将会在每一次filter被应用时调用
- 返回的函数的参数默认是从pipe来的上游的数据，如果我们需要额外的参数可以更改为:

```
return function(ts, otherArgument) { ... };
```

- 然后在使用filter时，我们可以`filter:argument`


### Things to Remember about Filters

- View中的filter被执行的非常频繁，需要考虑filter带来的额外的性能问题
- Filter应该被设计的比较快速
- 优先在service或者controller中使用filter         

-----------------------------

### Unit Testing Filters

示例程序：

```javascript
angular.module('filterApp', [])
    .filter('timeAgo', [function () {
        var ONE_MINUTE = 1000 * 60;
        var ONE_HOUR = ONE_MINUTE * 60;
        var ONE_DAY = ONE_HOUR * 24;
        var ONE_MONTH = ONE_DAY * 30;

        return function (ts, optShowSecondMessage) {
            if (optShowSecondMessage !== false) {
                optShowSecondMessage = true;
            }

            var currentTime = new Date().getTime();
            var diff = currentTime - ts;

            if (diff < ONE_MINUTE && optShowSecondMessage) {
                return 'seconds ago';
            } else if (diff < ONE_HOUR) {
                return 'minutes ago';
            } else if (diff < ONE_DAY) {
                return 'hours ago';
            } else if (diff < ONE_MONTH) {
                return 'days ago';
            } else {
                return 'months ago';
            }
        };
    }]);
```

Spec测试文件：

```javascript
describe('timeAgo Filter', function () {
    beforeEach(module('filterApp'));

    var filter;

    beforeEach(inject(function(timeAgoFilter) {
        filter = timeAgoFilter;
    }));

    it('should respond based on timestamp', function () {
        var currentTime = new Date().getTime();
        currentTime -= 10000;
        expect(filter(currentTime, false)).toEqual('minutes ago');
        var fewMinutesAgo = currentTime - 1000 * 60;
        expect(filter(fewMinutesAgo, false)).toEqual('minutes ago');
        var fewHoursAgo = currentTime - 1000 * 60 * 68;
        expect(filter(fewHoursAgo, false)).toEqual('hours ago');
        var fewDaysAgo = currentTime - 1000 * 60 * 60 * 26;
        expect(filter(fewDaysAgo, false)).toEqual('days ago');
        var fewMonthsAgo = currentTime - 1000 * 60 * 60 * 24 * 32;
        expect(filter(fewMonthsAgo, false)).toEqual('months ago');
    });
});
```

解析：很简单，没啥好说的。

------------------------------------

### Routing Using ngRoute

- ngRoute是angular的可选模块
- do routing task in angular application
- routing in angular is declarative


routing 在angular中不是标准的URLs，而不是被称作为hashbang（#!）的URLs，比如下面：

- <http://my.app.com/#/first/page>
- <http://my.app.com/#!/first/page>

这里之所以用hashbang，是因为浏览器对hashbang的特殊的处理：

- 在angular中我们不想遇到full page载入
- 我们只想载入relevant的部分
- 浏览器不会把#或者#!后面的内容加入到请求的URL中去
  - 比如，`http://www.app.com/#/first/page`，浏览器的header只有`http://www.app.com/`
- angular利用hashbang去做页面的navigation


```html
<html>
<head>
    <title>AngularJS Routing</title>
    <script src="../bower_components/angular/angular.js"></script>
    <script src="../bower_components/angular-route/angular-route.js"></script>
</head>
<body ng-app="routingApp">
<h2>Angular Routing Application</h2>
<ul>
    <li><a href="#/">Default Route</a></li>
    <li><a href="#/second">Second Route</a></li>
    <li><a href="#/asdf">Nonexisting Route</a></li>
</ul>
<div ng-view></div>
</body>
<script>
    angular.module('routingApp', ['ngRoute'])
            .config(['$routeProvider', function ($routeProvider) {
                $routeProvider.when('/', {
                            template: '<h5>This is the default route</h5>'
                        })
                        .when('/second', {
                            template: '<h5>This is the second route</h5>'
                        })
                        .otherwise({redirectTo: '/'});
            }]);
</script>
</html>
```

解析：

- Include angular-route.js载入ngRoute
- ng-view directive where routing is to take effect
- 多加了一个依赖`angular.module('routingApp', ['ngRoute'])`
- 使用`$routeProvider`这个angular内生的服务去定义route
- 页面内的navigation是通过a tag去实现的，当然也可以通过操作browser URL
- 由于angular利用了浏览器的history去做navigation，因此是可以前进后退的
- $routeProvider定义routes用`when()`函数，有两个参数：
    - 第一个是URL或者URL regex
    - 第二个是routing对应的行为
- `otherwise()`定义了默认的行为


##### Routing Options

```javascript
$routeProvider.when(url, {
    template: string,
    tempateUrl: string,
    controller: string, function or array,
    controllerAs: string,
    resolve: object<key, function>
});
```

解析：

- url, URL mapping to views
- template: 与ng-view配合，直接替换ng-view所在元素的子元素
- templateUrl:

```javascript
$routeProvider.when('/test', {
    templateUrl: 'views/test.html',
});
```        

- controller，routing所使用到的controller，可以有两种方式定义：
    - 直接用字符串引用定义在html中的controller，通过ng-controller directive
    - inline实现

```javascript
$routeProvider.when('/test', {
    template: '<h1>Test Route</h1>',
    controller: ['$window', function($windwow) {
        $window.alert('Test route has been loaded');
    }]
});
```

- controllerAs，很简单

```javascript
$routeProvider.when('/test', {
    template: '<h1>Test Route</h1>',
    controller: 'MyCtrl as ctrl',
});

$routeProvider.when('/test', {
    template: '<h1>Test Route</h1>',
    controller: 'MyCtrl',
    controllerAs: 'ctrl',
});
```

- redirectTo

```javascript
$routeProvider.when('/new', {
    template: '<h1>Test Route</h1>',
});

$routeProvider.when('/old', {
    redirectTo: '/new',
});
```

- resolve:
    - executing or finishing some asynchronouse tasks before route works
        - authentication, permission
    - preload some data before controller and route

```
angular.module('resolveApp, ['ngRoute'])
    .value('Constant', {MAGIC_NUMBER: 42})
    .config(['$routeProvider', function($routeProvider) {
        $routeProvider.when('/', {
            template: '<h1>Main Page, no resolves</h1>'
        }).when('/protected', {
            template: '<h2>Protected Page</h2>',
            resolve: {
                immediate: ['Constant', function(Constant) {
                    return Constant.MAGIC_NUMBER * 4;
                }],
                async: ['$http', function($http) {
                    return $http.get('/api/hasAccess');
                }]
            },
            controller: ['$log', immediate, async, function($log, immediate, async) {
                $log.log('Immediate is ', immediate);
                $log.log('Server returned for async', async);
            }]
        });
    }]);
```

解析：

resolve定义了2个key：

    - immediate: 每个key都返回一个array，array里面遵循Angular DI语法
        - 依赖Constant，然后利用注入的Constant，返回一个常量*number
    - async: 注入了$http依赖，然后调用server的get方法，返回一个promise

- angular对resolve的保证：
    - 如果resolve返回一个常量，angular立即结束resolve执行，并返回一个成功的resolve
    - 如果resolve返回一个promise，则angular则一直阻塞到promise返回，如果返回的是successful，则resolve被当作success，否则fail
    - 如果有resolve存在，则angular保证所有的routing不会执行，直到resolve返回
    - 如果resolve返回错误或者异常，则angular不会执行route

- resolve中的key可以被注入angular的controller:
    - 如果是值，则直接解析为值
    - 如果是promise，则返回promise的解析值


### $routeParams 服务

```javascript
angular.module('resolveApp', ['ngRoute'])
    .config(['$routeProvider', function($routeProvider) {
        $routeProvider.when('/', {
            template: '<h1>Main Page</h1>'
        }).when('/detail/:detId', {
            template: '<h2>Loaded {{myCtrl.detailId}}' +
                ' and query string is {{myCtrl.qStr}}</h2>',
            controller: ['$routeParams', function($routeParams) {
                this.detaildId = $routeParams.detId;
                this.qStr = $routeParams.q;
            }],
            controllerAs: 'myCtrl'
        });
    }]);
```

解析：

- `/detail/:detId` 表明在`/detail`的后面将会有一个value，并会被存储为detId
    - 这个其实就是regex中的named group

- 这些被存储到的name group的值，会被放在$routeParams中

`/detail/123?q=MySearchParam`将会被存储为：

```
{
    detId: '123',
    q: 'MySearchParam'
}
```

### One more thing

- Empty template

angularJS需要每一个route都有一个nonempty的template或者templateUrl，如果没有这些，angular将会静默地丢掉route

- Resolve injection into controller

如果需要resolve的值注入controller，那么就需要把controller定义在route中，而不是使用ng-controller directive，因为没有上下文，angular不知道哪个controller需要注入

- $routeParam vaiable type

$routeParam的key返回的值默认都是string，所以`===`比较会出问题，需要转化为合适类型

- One ng-view per application

ng-view不能多个，不能嵌套，用来展示route返回的内容    
