## 1. 起步

### 1.1 基本概念

#### 1.1.1 AJAX

AJAX 全称 `Asynchronous Javascript And XML` ， 即异步的 JS 和 XML。

通过 AJAX 可以在浏览器中向服务器发送异步请求，**可以无刷新获取数据**。

AJAX 是一种将现有的标准组合在一起使用的新方式。

#### 1.1.2 XML

XML ：`eXtensible Markup Language` 即可扩展标记语言。被设计于传输和存储数据

于 HTML 类似，不同的是 HTML 中都是预定义标签，而 XML 中没有预定义标签，全都是自定义标签，用来表示以一些数据

~~~xml
<student>
    <name>王或或</name>
    <age>21</age>
    <gender>女</gender>
</student>
~~~

可以简单理解为：

HTML：呈现数据
XML：存储和传输数据

#### 1.1.3 JSON

json：`JavaScript Object Notation` ，即对象简谱，是一种轻量级的数据交换格式。

~~~json
{"name":"王或或","age":21,"gender":"女"}
~~~





### 1.2 特点

#### 1.2.1 优点

- 可以无需刷新页面而与服务器端进行通信
- 允许根据用户事件来更新部分页面内容



#### 1.2.2 缺点

- 没有浏览历史，不能回退
- 存在跨域问题（同源）
- SEO 不友好

