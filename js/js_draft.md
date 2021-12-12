#### TDC临时性死区：

​		如果变量的定义在调用之后，且声明方式没有变量提升的特性，定义与调用之间存在临时性死区。

```javascript
let web = 'xmc.com';
function run(){
    console.log(web);
    // TDC临时性死区, 如果不将变量进行重新定义,则不会产生死区,会使用块之前定义过的变量.  即现在块中查找(整个块,而不是使用变量前的部分),若块中没有,则在上一级查找
    let web = 'hello.world.com'
} 
run();
```

#### 全局污染 : 

​		如果在定义变量时没有使用 let 等声明变量, 则这个变量会污染全局

```javascript
//文件1:
function show(){
	web = 'xmc.com';	//case 1
	var web = 'xmc.com'; //case 2 防止全局污染
}
//文件2:
web = 'helloworld';
show();
console.log(web)	//case1:'helloworld', case2:'helloworld'
```

#### 块作用域:

​		使用 / 开发库

```javascript
//文件1(base.js):
	let = 'base.com'
//文件1.1(base1.js):
{
	let $ = (window.$ = {});
	let web = 'wy';
    $.getWeb = function(){
        return web;
    }
}
//文件2:
<script>
    let web = 'xmc.com'
</script>
<script src='base.js'></script>
<script src='base1.js'></script>
<script>
    console.log(web)	//base.com
	// base.js 中的内容与主体内容平级, 可以将文件1里面的内容用立即执行函数(把函数作用域当作块作用域) 或者 直接用块作用域 
	console.log($.getWeb()) //wy
</script>

```

#### 传值（基本类型）与传址（引用类型）：

​		在数据较大（对象）的情况下，通常使用传址，传值则涉及浅拷贝、深拷贝



#### null与undefined

​		初始化引用类型用 null（可以理解为对象里面还没有元素，因此为null）

​		初始化基本类型用 undefined （数值未定义），函数无返回值、未定义变量都为 undefined

#### == N ===

​		"=="：仅值相等; 在比较时会进行类型转换，等式的两边都可能会被转换

​		"==="：值与类型都相等

```javascript
let a = 1;
let b = "1";
let c = "1";
console.log(a == b)	// true
console.log(a === b)// false
```



#### 模板字面量的使用

*字面量可以嵌套，${变量、有返回值的函数...}

```javascript
let year = "2001";
let name = "xmc";
document.write(`<h1>${name} 
was born in 
${year}</h1>`);	//可随意换行，插入变量，使用html的标签
```



