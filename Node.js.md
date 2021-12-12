## Node.js

### 1. 起步

#### 1.1 规范

- node.js 使用 commonjs 的规范，因为早期 js 没有模块化规范，webpack默认使用 commonjs 同理（浏览器不支持commonjs）

#### 1.2 CJS 的模块化

`module.exports` & `exports` & `require()`

##### 基本使用

~~~js
// 模块的导出
exports.arg = arg
module.exports.arg = arg
module.exports = {}

// 模块的引用
require('moduleA.js')	// 返回的是一个地址，指向模块的 module.exports
~~~



##### 原理分析

1. 每个 .js 文件都是一个原型为 module 的实例

2. 每个 .js 文件可以通过 `require('<module_name>') `函数获得其它模块（实例）的 `module.exports` 这个对象的地址

~~~js
const moduleA = require('<module_name>')
// 简单来说，modulaA 是 <module_name> 中 module.exports 的浅拷贝
~~~

3. 由于 `require` 返回的是一个对象，所以可以将其解构

~~~js
const {name1, name2, ...} = require('<module_name>')
~~~



##### exports & module.exports

1. `exports` 是多余的，只是作为一个妥协与中转
2. 在 `module.exports` 的**地址** 与 `require()` 返回的**地址** 相同。
3. 在模块启动之后，先自动执行 `module.exports = exports` ， 即 此后若改变 exports 指向的地址，则无法再通过`exports`导出模块数据， 若再次 `module.exports = exports` ，则可以再次通过 `exports` 输出模块数据

#### 1.3 require 查找规则

`require(X)`：

**情况一**：X是一个核心模块，比如path、http

直接返回核心模块，并且停止查找

**情况二**：X是以 ./ 或 ../ 或 /（根目录）开头的

第一步：将X当做一个文件在对应的目录下查找

1. 如果有后缀名，按照后缀名的格式查找对应的文件 p 

2. 如果没有后缀名，会按照如下顺序： 

   1> 直接查找文件X 

   2> 查找X.js文件 

   3> 查找X.json文件

   4> 查找X.node文件

第二步：没有找到对应的文件，将X作为一个目录 p 查找目录下面的index文件

   	1> 查找X/index.js文件

​	   2> 查找X/index.json文件

​	   3> 查找X/index.node文件

如果没有找到，那么报错：not found



**情况三**：直接是一个X（没有路径），并且X不是一个核心模块

会遍历 `module.paths` 的路径 

~~~bash
Module {
....
  paths: [
    'C:\\Users\\mayo\\Desktop\\plan_web\\node_learning\\01-cjs规范\\node_modules',
    'C:\\Users\\mayo\\Desktop\\plan_web\\node_learning\\node_modules',
    'C:\\Users\\mayo\\Desktop\\plan_web\\node_modules',
    'C:\\Users\\mayo\\Desktop\\node_modules',
    'C:\\Users\\mayo\\node_modules',
    'C:\\Users\\node_modules',
    'C:\\node_modules'
  ]
}
~~~

#### 1.4 模块加载过程

- 模块在被第一次引入时，模块中的js代码会被运行一次。即若只是单纯的 `require` 也会运行一遍模块中的代码
- 模块被多次引入时，会缓存，最终只加载（运行）一次。因为每个模块对象module都有一个属性：`loaded` ，被加载过一次之后会被赋值为 `ture` ，表示已经被加载。下次再被其他模块调用时不会再加载（运行一次）。

~~~js
// moduleA.js
console.log('AAA');

// moduleB
require('./moduleA.js')
console.log('BBB')

// main.js
require('./moduleA.js')
require('./moduleB.js')
console.log('main')

// result
AAA
BBB
main
~~~

- 如果有循环引入，Node采用的是深度优先算法（DFS）,在搜索的同时，将被调用到的每个 `module.loaded = true` （加载）（同上）



### 2.内置模块



### 3. express

#### 3.1 中间件

Express 是一个路由和中间件的Web框架，它本身的功能非常少：

Express 应用程序本质上是一系列中间件函数的调用

中间件的**本质**是传递给 express 的一个**回调函数**

这个回调函数接受三个参数：

- 请求对象（request对象）

- 响应对象（response对象）

- next函数（在express中定义的用于执行下一个中间件的函数）；