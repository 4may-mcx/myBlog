# ES 模块化



`<script>` 里面如果使用模块化功能，要加 `type = "module"` 。

-  `module` 类型的 script 标签中，默认使用严格模式，

- 该类型的标签在文件运行前预解析，即 `script` 可以放在所有标签之前

- 每个 module都 有独立的作用域，没有 export 外部不能访问



`import = "<url>"` 后面的 `url` 后缀名 `.js` **不能漏**

存在跨域问题，写 demo 的时候用 vscode 的 Live Server 插件解决

关键字 `import` & `export`

~~~js
// 暴露模块中的变量, 这里的{}不是对象(不能写键值对)，是一个语法(被用于解析，放置要导出的变量的引用列表),即导出变量的地址
export {num, fun, str}
...
~~~

~~~js
// 获取模块中的变量，这里的{}也不是一个对象
import {num, fun, str} from "utils.js"
import * as moduleA from "utils.js"
// 在script中使用
<script src="./src/index.js" type="module"></script>
~~~



## 别名

`export { moduleName1 as moduleName2} `

`import { moduleName1 as moduleName2} from 'common.js' `

moduleName1 为原本变量名，moduleName2 为别名



## default 导出

如果 module 中只有一个要传出的变量

~~~js
export default let num = 134;
----------
let num = 134;
export { num as default}
~~~



引用这个 module 的文件可以用任意名称接收  `import a from 'common.js'`

~~~js
import anyName from 'common.js'
~~~



## 混合导入导出

~~~js
// common.js
export { arg1 as default, arg2}

--------
import anyName, {arg2} from "common.js"
~~~



## 结合使用

~~~js
export {arg1, arg2, ...} from "common.js"
~~~

用于统一导出，方便管理



## import() 函数

由于 `import` 关键字建立模块之间的依赖关系，是在 `parsing` （解析）的阶段加载的，而业务逻辑代码是在运行阶段进行的。

所以`import`需要在模块的开头使用，不能在业务代码中使用，即

~~~js
setTimeout(() => {
    import * as moduleA from 'common.js'
}, 1000);
~~~

这么使用的违法的。



因此，需要通过 `import()` 函数进行按需导入

该函数返回一个 `promise`

~~~js
setTimeout(() => {
    import('./a.mjs').then(res => {
        console.log(res.name);
        console.log(res.age);
        console.log(res.sayHi('xumuchao'));
    }).catch(err => {
        console.log(err);
    })
}, 1000);
~~~



## 加载过程

- ES module 的加载是异步的（不会阻塞代码的执行，为了浏览网页的速度），CJS 是同步的（存在阻塞问题）