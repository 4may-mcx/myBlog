## 类

语法糖，本质是个函数。简化很多类似于设置原型链的操作，使书写对象更加清晰，内部工作机制就是原型操作。

### 1.1 基本使用

~~~js
function User1(name) {
    this.name = name;
}
User1.prototype.getName = function(){
    return this.name;
}
let stu1 = new User1('xmc');
console.log(stu1.name);
console.log(stu1.getName());
---------------------------------
class User2{
    constructor(name){
        this.name = name;
    }
    // 会自动把方法放到原型中
    getName(){
        return this.name;
    }
}
let stu2 = new User2('wy')
console.log(stu2.name);
console.log(stu2.getName());
~~~

### 1.2 声明对象属性的方式

~~~js
class User{
    // constructor 用于接受参数，也可以在里面定义变量。类的内部本身就可以定义变量
    // 这里的 name1 和 name2 本质相同，可以用 this.<name> 操作修改
    name1 = '1111';
    constructor(){
        this.name2 = '2222';
        this.arr = [...arguments];
    }
}
let xmc= new User('a','b','c');
console.log(xmc.name1, xmc.name2);
// 1111 2222
// [ 'a', 'b', 'c' ]
~~~

### 1.3 对象的原型默认不可被遍历

类中默认设置`"enumerable":false` ，而函数为 `true` 

### 1.4 静态属性 & 静态方法

静态属性：只在类中保存一份

静态方法：只能使用函数进行调用，不能通过实例（对象）进行调用

~~~js
function Web(url){
    this.url = url;
}
// es5 定义静态属性的方式
Web.url = 'niming.com';
Web.show = function(){
    console.log('im web')
}
let xmc = new Web('xumuchao.com');

console.log(xmc.url); // xumuchao.com
console.log(Web.url); // niming.com
Web.show(); // im web
// xmc.show() // err
------------------------------------------
class Web{
    // class 中定义静态属性的方式
    static url = 'niming.com';
	static show(){
        console.log('im web')
    }
    constructor(url){
        this.url = url;
    }
}
let xmc = new Web('xumuchao.com');
console.log(xmc.url); // xumuchao.com
console.log(Web.url); // niming.com
Web.show(); // im web
// xmc.show() // err
~~~

静态属性简单应用：

~~~js
class Web{
    static url = 'niming.com';
    constructor(url){
        this.url = url;
    }
    getCommonHost(port){
        return `${Web.url}:${port}`
    }
    getMyHost(port){
        return `${this.url}:${port}`
    }
}
let xmc = new Web('xumuchao.com');

console.log(xmc.getCommonHost(8000), xmc.getMyHost(8000));
//niming.com:8000 xumuchao.com:8000
~~~



