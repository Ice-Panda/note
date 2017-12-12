# 高性能

## 加载和运行

大多数浏览 器使用单进程处理 UI 更新和 JavaScript 运行多个任务，而同一时间只能有一个任务被执行.JavaScript 运行了多长时间，那么在浏览器空闲下来响应用户输入之前的等待时间就有多长.**(如果一个任务运行很长时间,那么用户交互就会很卡)**

`<script>`标签的出现使整个页面因脚本解析、运行而出现等待.因为脚本可能在运行过程中修改页面内容.而下载js文件又需要一定的时间,所以必须等到文件下载好后,然后执行js脚本.浏览器才能继续执行页面解析和响应用户交互.此外脚本下载还会阻塞图片加载.

**`将script标签放到body尾部**

每个script标签都会在下载时阻塞页面解析.(建立http连接,执行下载好的脚本.)

**限制script标签个数,将多个文件合并为一个**

script标签和其他元素一样,所以可以**动态创建script元素**,这样的话当script标签添加到页面中,脚本的源文件立即开始下载,并且不会阻塞其他页面处理过程
```
var script = document.createElement ("script");
script.type = "text/javascript";
script.src = "file1.js"; 
document.getElementsByTagName_r("head")[0].appendChild(script);
```
**还可以使用xhr下载js文件内容,然后修改script标签的text**
```javascript
var xhr = new XMLHttpRequest(); 
xhr.open("get", "file1.js", true); 
xhr.onreadystatechange = function(){
if (xhr.readyState == 4){
    if (xhr.status >= 200 && xhr.status < 300 || xhr.status == 304){
        var script = document.createElement ("script"); 
        script.type = "text/javascript";
        script.text = xhr.responseText; 
        document.body.appendChild(script);
    } 
}
};
xhr.send(null);
```
可以通过window.onload来得知页面是否已经准备好,如果准备好,那么此时可以动态的加载js文件(注意js文件数量,因为每个文件下载都要建立http链接)

## 数据访问

- 直接量
- 变量
- 数组:具有数字索引,存储数组对象
- 对象:具有字符串索引,存储js对象

直接量和局部变量访问性能几乎无差异,但是数组和对象成员访问代价较高

### 管理作用域

作用域链和标识符解析

**函数是对象**拥有变成可以访问的属性和一些不能被程序访问的属性,其中一个内部属性就是`Scope`

内部属性包含该函数被创建的作用域对象的集合.此集合被称为函数的作用域链,它决定哪些数据可以被函数访问.此函数作用域中的每个对象被称为一个可变对象,每个可变对象都以"键值对"的形式存在,当一个函数创建后,他的作用域就载入那些可以被访问的数据对象.

**每次调用函数时,就会建立一个内部对象,称为运行期上下文,所以多次调用同一个函数就会导致多次建立上下文,函数执行完毕,运行期上下文就被销毁**

一个运行期上下文有它自己的作用域链，用于标识符解析。当运行期上下文被创建时，它的作用域链被初始化，连同运行函数的[[Scope]]属性中所包含的对象。这些值按照它们出现在函数中的顺序，被复制到运行期上下文的作用域链中。这项工作一旦完成，一个被称作“激活对象”的新对象就为运行期上下文创建好了。此激活对象作为函数执行期的一个可变对象，包含访问所有局部变量，命名参数，参数集合，和 this 的接口。然后，此对象被推入作用域链的前端。当作用域链被销毁时，激活对象也一同销毁。

![img](./src/高性能/001.png)

在函数运行过程中，每遇到一个变量，标识符识别过程要决定从哪里获得或者存储数据。此过程搜索运 行期上下文的作用域链，查找同名的标识符。搜索工作从运行函数的激活目标之作用域链的前端开始。如 果找到了，那么就使用这个具有指定标识符的变量;如果没找到，搜索工作将进入作用域链的下一个对象。 此过程持续运行，直到标识符被找到，或者没有更多对象可用于搜索，这种情况下标识符将被认为是未定义的。函数运行时每个标识符都要经过这样的搜索过程，例如前面的例子中，函数访问 sum，num1，num2 时都会产生这样的搜索过程。正是这种搜索过程影响了性能。

在运行期上下文的作用域链中， 一个标识符所处的位置越深，它的读写速度就越慢。所以，函数中局部变量的访问速度总是最快的，而全局变量通常是最慢的(优化的 JavaScript 引擎在某些情况下可以改变这种状况)。请记住，全局变量总是 处于运行期上下文作用域链的最后一个位置，所以总是最远才能触及的。作用域链上 不同深度标识符的识别速度，深度为 1 表示一个局部变量。


**`with`可以改变作用域链,但是在with子句中访问外城变量就需要搜索下一级作用域节点(即导致当前环境下局部变量被推入第二个作用域链中,所以访问代价变高).**

```javascript
function initUI(){
with (document){ //avoid! 避免多次书写“document”。这看起来似乎更有效率，而实际上却产生了一个性能问题
    var bd = body,
    links = getElementsByTagName_r("a"), i = 0,
    len = links.length;
    while(i < len){
        update(links[i++]); }
        getElementById("go-btn").onclick = function(){ start();
    };
    bd.className = "active"; 
    }
}
```
`try-catch`中`catch`也改变作用域,并且将异常对象推入作用域链中前端的一个可变对象中,并且函数的所有局部变量被推入第二个作用域链中.catch执行完毕后,作用域链返回原来的状态

无论是 with 表达式还是 try-catch 表达式的 catch 子句，以及包含()的函数，都被认为是动态作用域。一 个动态作用域只因代码运行而存在，因此无法通过静态分析(察看代码结构)来确定

```JavaScript
function execute(code) {
     (code);
    function subroutine(){
         return window;
    }
    var w = subroutine(); //what value is w?
};
```
大多数情况下，w 将等价于全局的 window 对象，但是如下情况:`execute("var window = {};")`





选择器 API，可以像使用 CSS 选择器那样查询 DOM 文档。CSS 查询被 JavaScript 原生实 现并通过 jQuery 这个 JavaScript 库推广开来。jQuery 引擎被认为是最快的 CSS 查询引擎，但是它仍比原生 方法慢。原生的 querySelector()和 querySelectorAll()方法完成它们的任务时，平均只需要基于 JavaScript 的 CSS 查询 10%的时间。大多数 JavaScript 库已经使用了原生函数以提高它们的整体性能。



## 创建并部署高性能 JavaScript 应用程序

