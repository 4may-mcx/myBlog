## 1. 基本概念：

**文件获取过程**：域名 -> DNS解析 -> ip地址 -> 返回html文件

### 1.1 浏览器内核

可以将 html 文件转化为网页页面。（eg: Blink/Webkit/Gecko...）

别名：layout engine、browser engine、rendering engine

**浏览器渲染过程：**

​	解析HTML文件为（DOM树），遇到 js 标签就会**停止**解析 HTML 文件，而去加载和执行 js 代码（js 可以操作DOM）

**文件解析过程：**

​	浏览器内核中含有 **Parser**（HTMLParser、CSSParser）

- DOMTree = HTML + HTMLParser （+ js 产生的操作）

- Style Rules = StyleSheets + CSSParser

- RenderTree  = DOMTree + StyleRules

- Display = RenderTree + Layout（根据设备的具体情况进行布局）+ Painting（绘制）



而这之中的 js 代码需要由浏览器内核中的 JavaScript引擎 执行。**JavaScript引擎**：将 js 代码翻译为 CPU 指令。

（eg: V8 / SpiderMonkey /  JavaScriptCore...）

**简单来说：**

​	**浏览器内核**由 **Parser** 和 **js引擎** 构成（eg: WebKit = WebCore + JavaScriptCore）



### 1.2 V8 引擎

主要由 C++ 编写

**运行过程：**

- **Blink** 将 js 源码交给 V8 引擎，**Stream** 获取到源码并且进行编码转换。
- **Scanner** 会进行词法分析，将代码转换成 tokens。

- **Parser** 模块会将 tokens 转换成AST（抽象语法树），这是因为解释器并不直接认识 JavaScript 代码。如果函数没有被调用，那么是不会被转换成AST的，因此需要一个 **PreParser** 将不需要马上执行的 js 代码进行预解析（**lazy parsing**），只解析需要的内容。

- **Ignition** 是一个解释器，会将AST转换成ByteCode（字节码）。同时会收集 TurboFan 优化所需要的信息（比如函数参数的类型信息，有了类型才能进行真实的运算）。如果函数**只调用一次**，Ignition会执行解释执行ByteCode

- **TurboFan** 是一个**编译器**，可以将字节码编译为CPU可以直接执行的机器码。如果一个函数**被多次调用**，那么就会被标记为热点函数，那么就会经过  TurboFan 转换成优化的机器码，提高代码的执行性能。但是，机器码实际上也会被还原为 ByteCode，这是因为如果后续执行函数的过程中，类型发生了变化（比如sum函数原来执行的是 number类型，后来执行变成了string类型），之前优化的机器码并不能正确的处理运算，就会逆向的转换成字节码（deoptimization）。因为 typescript 规定了数据类型，所以在运行上会比 js 更快。优化过的机器码不需要 **deoptimization**（反优化）

- **字节码 bytecode**：不同机器架构的 CPU 可执行的机器码不同，转为 bytecode 算是对转化为机器码之前的预处理，即 bytecode 可以跨平台（优点）。

参考文档：

Parse的V8官方文档：https://v8.dev/blog/scanner 

Ignition的V8官方文档：https://v8.dev/blog/ignition-interpreter 

TurboFan的V8官方文档：https://v8.dev/blog/turbofan-jit

<img src="https://raw.githubusercontent.com/4may-mcx/myBlog/master/images/js/V8engineExplainJs.png" alt="V8engineExplainJs.png (1060×570) (raw.githubusercontent.com)" style="zoom:67%;" />





**AST树样例：**

<img src="https://raw.githubusercontent.com/4may-mcx/myBlog/master/images/js/astTree.png" alt="astTree.png) (raw.githubusercontent.com)" style="zoom:60%;" />

### 1.3 作用域

##### 官方规范

**早期** ECMA 版本规范：

> Every execution context has associated with it a variable object (VO). Variable and functions declared in the source text are added properties of the variable object. For function code, parameters are added as properties of the variable object.
>
> 每一个执行上下文（execution context）会被关联到一个**变量对象**（VO），在源代码中的变量和函数声明会被作为属性添加到 VO 中。对于函数来说，参数也会被添加到 VO 中。

**最新** ECMA 版本规范

> Every execution context has an associated VariableEnvironment (VE). Variables and functions declared in ECMAScript code evaluated in an execution context are added as bindings in that VariableEnvironment's Record. For function code, parameters are also added as bindings to that Environment Record.
>
> 每一个执行上下文会关联到一个**变量环境**（VariableEnvironment ）中，再执行代码中变量和函数的声明会作为环境记录（VariableEnvironment's Record）添加到变量环境中。对于函数来说，参数也会作为环境记录添加到变量环境中。

在新版本的规范中，将 variable object 抽象为 variable environment 。VE 可以用包括对象（object）在内的其它数据存储方式来表示，具体表现方式取决于解析源代码的 js 引擎。

#### 1.3.1 基本概念


##### Global Object

初始化全局对象：js引擎在**执行代码之前**，会在堆内存中创建一个 **Global Object** (GO)，GO 中包含 Array，String，Date，setTimeout...

**Activation Object**：在函数被调用时创建，临时存放函数内部声明的变量和信息，可以理解为阉割版 GO。

在node中，每个 `.js` 文件可以作为一个函数处理，此时每个 `.js` 文件也可以相当于一个局部函数。

因此可以把 GO 理解为一个不会被销毁、含有更多东西的 AO



##### Execution Context

当 JavaScript 代码执行一段**可执行代码**(executable code)时，会创建对应的**执行上下文**(execution context)

**三种类型：**

- **全局执行上下文（ GEC）**：不在任何函数中的代码都位于全局执行上下文中，只有一个.浏览器中的全局对象就是 window 对象，`this` 指向这个全局对象。
- **函数执行上下文（ FEC）**：**只有调用**函数时，才会为该函数创建一个新的执行上下文，可以存在无数个，每当一个新的执行上下文被创建，它都会按照特定的顺序执行一系列步骤。
- **Eval 函数执行上下文（eval代码）**： 指的是运行在 `eval` 函数中的代码，很少使用。

执行上下文又包括三个生命周期阶段：**创建阶段 --> 执行阶段 --> 回收阶段**



##### Execution Context Stack

ECS：Execution Context Stack，执行上下文栈（ECStack）

是 js 引擎内部的一个**执行上下文栈** ，是用于执行代码的**调用栈**，用于存储在代码执行期间创建的所有执行上下文。

- 对于全局的代码块，会先构建一个 **Global Execution Context**（GEC），GEC会被放到 ECStack 中执行
- 对于将要执行的函数来说，会先构建一个 **Functional Execution Context**（FEC），同时压入 ECStack



#### 1.3.2 代码执行流程

##### 文字描述

执行上下文（execution context）包含两个部分：

- 第一部分：
	- 变量对象(Variable object，VO)：首先初始化函数的参数arguments，提升函数声明和变量声明。是 GO / AO。
	- 作用域链（Scope Chain）：在创建变量对象之后创建。作用域链本身包含变量对象。作用域链用于解析变量。当被要求解析变量时，JavaScript 始终从代码嵌套的最内层开始，如果最内层没有找到变量，就会跳转到上一层父作用域中查找，直到找到该变量。**可以简单理解为 scope chain = VO + parentVO（parent scope）**
	- this的指向
- 第二部分：将要执行的代码

**基于上述概念**：

GEC的创建：

1. 在代码执行前，在**parser转成AST的过程中**，会将**全局定义的变量、函数（堆内存地址）** 等加入到 GlobalObject中，但是并不会赋值，这个过程也称之**为变量的作用域提升（hoisting）**。

   此时的变量为 `undefined` ，函数变量指向函数对象（堆内存地址）

2. 在代码执行时，会对变量进行赋值，或者执行其他的函数

   

FEC的创建：

1. 在解析函数成为AST树结构时，会先创建一个 Activation Object（AO）。AO中包含形参、arguments、函数定义和指向函数对象、定义的变量。
2. 作用域链：由VO和父级VO组成，查找是会一层层查找。
3. this绑定的值
4. 执行代码，对变量赋值，或者执行其它函数



**hoisting 时期的变量**：

~~~js
var num = 1
var foo = function(){
    var a = 1
    console.log(a)
}
--------------------
hosting时期:
{
    num: undefined,
    foo: 0xa00
}
~~~



**细节：**

- 在 GEC 中，VO = GO； FEC 中， VO = AO。前文提到过，GO与AO性质相同，VO（VE）是一个抽象化的概念，只要某个环境可以储存变量信息，就可以作为变量环境

- 函数在执行完之后，FEC 会被栈弹出并销毁，而这个 FEC 对应的 VO(AO) 是否销毁，要看这个 AO 中的数据是否被其它作用域引用（闭包的自由变量）

- 函数在**编译**时，父级作用域（parent scope）就已经确定，所以与调用时的位置无关。

~~~js
var message = 'hello global'

var foo = function(){
    console.log(message)
}

var bar = function(){
    var message = 'hello bar'
    foo()	// 此时 foo 的父级作用域仍然是全局，而不是 bar
}

bar()
// hello global
~~~

- 在编译时，`return` 关键字不会终止编译，即使 `return` 就在代码的第一行，也会编译完全部的代码



##### 图解

下次再画






## 2. js 内存管理
#### 空间分配

基本数据类型：放在栈空间

复杂数据类型（引用类型）：放在堆空间，变量本身保存的是堆空间的地址（指针）

#### GC 垃圾回收

**garbage collection**： 简称 GC

垃圾回收算法：引用计数，标记清除(大多数js引擎使用的方法)

**引用计数**：每个对象内部都有一个 **保留计数（retain count）**，无论是在栈空间中被指针指向，还是在堆空间中被其它数据引用地址，都会让 count 增加，当 count == 0 时，这个数据就会被回收。

但存在内存泄漏的可能，因为存在循环引用

~~~js
let obj1 = {point: obj2}
let obj2 = {point: obj1}
~~~

两个数据互相引用，内存就永远不会被释放，所以存在内存泄漏的可能性。

**标记清除**：设置一个根对象（root object）（eg：global object），垃圾回收器会定期从这个根开始，找所有从根开始有引用的对象（有用的对象），对那些没有被引用到的对象进行清除（没用的对象）。

即使多个对象之间存在无意义的相互引用，也会被清除。即解决了循环引用的问题。



## 3. 闭包



闭包的定义

闭包存在的原理

闭包的内存泄漏

闭包中没有被引用到的自由变量（AO中不需要用到的属性） V8 引擎会删除掉



## 4. this的绑定规则

对象内部不是块级作用域，更不是作用域



> 普通函数：
>
> ​	默认规则：谁调用就指向谁，与如何定义无关

> 箭头函数：箭头函数体内 的`this`对象，就是定义该函数时所在的  ***作用域指向的***  对象，而不是使用时所在的作用域指向的对象。（箭头函数的this，等价于他爹的this不就完了）



箭头函数的 this 指向：看这个箭头函数所在作用域（上下文）的外层作用域

~~~js
var name = 'global'
const obj = {
    name: 'obj'
    foo: () => {
        console.log(this) // obj
	}
}
// 这个例子里面，箭头函数本身所在的作用域是对象obj，而obj在全局的环境中(obj指向的对象是全局)。所以箭头函数的this指向全局
~~~

