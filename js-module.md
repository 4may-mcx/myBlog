# js 模块化



`<script>` 里面如果使用模块化功能，要加 `type = "module"` 。

-  `module` 类型的 script 标签中，默认使用严格模式，

- 该类型的标签在文件运行前预解析，即 `script` 可以放在所有标签之前

- 每个module都有独立的作用域，没有 export 外部不能访问




`import = "<url>"` 后面的 `url` 后缀名 `.js` 不能漏

存在跨域问题，写 demo 的时候用 vscode 的 Live Server 插件解决



~~~js
// 暴露模块中的变量
export {num, fun, str}
...
~~~

~~~js
// 获取模块中的变量
import {num, fun, str} from "utils.js"

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





