# 高性能

主要围绕:
1. 浏览器重新渲染和重排
2. 作用链访问
3. http请求
4. 对于长时间运行js任务提高速度,保持js任务运行在50毫秒以内


## 加载和运行

数浏览器使用单进程处理 UI 更新和 JavaScript 运行多个任务，而同一时间只能有一个任务被执行.JavaScript 运行了多长时间，那么在浏览器空闲下来响应用户输入之前的等待时间就有多长.**(如果一个任务运行很长时间,那么用户交互就会很卡)**

`<script>`标签的出现使整个页面因脚本解析、运行而出现等待.因为脚本可能在运行过程中修改页面内容.而下载js文件又需要一定的时间,所以必须等到文件下载好后,然后执行js脚本.浏览器才能继续执行页面解析和响应用户交互.此外脚本下载还会阻塞图片加载.

**`将script标签放到body尾部**

每个script标签都会在下载时阻塞页面解析.(建立http连接->执行下载好的脚本.)

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

**每次调用函数时,就会建立一个内部对象,称为运行期上下文,所以多次调用同一个函数就会导致多次建立上下文,函数执行完毕,运行期上下文就被销毁**

![img](./src/高性能/001.png)

在运行期上下文的作用域链中，一个标识符所处的位置越深，它的读写速度就越慢。

**`with`可以改变作用域链,但是在with子句中访问外城变量就需要搜索下一级作用域节点(即导致当前环境下局部变量被推入第二个作用域链中,所以访问代价变高).**

`try-catch`在进入catch时会在作用域链头部加入catch作用域,with语句也是同样的效果所以慎用with.当catch或者with执行完毕作用域才会恢复.

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

### 闭包,作用域和内存

```
function assignEvents(){
    var id = "xdi9592"; 
    document.getElementById("save-btn").onclick = function(event){
        saveDocument(id);
    };
}
```
由于闭包的[[Scope]]属性包含与运行期上下文作用域链相同的对象引用，会产生副作用。通常，一个函数的激活对象与运行期上下文一同销毁。**当涉及闭包时，激活对象就无法销毁了，因为引用仍然存在于闭包的[[Scope]]属性中**。**这意味着脚本中的闭包与非闭包函数相比，需要更多内存开销**
![img](./src/高性能/002.png)

### 原型 Prototypes

Prototypes最后一级是Object:`Array instanceof Object===> true`

对象的方法或者属性也是通过原型链进行搜索,一层一层深入,直到object对象.

hasOwnProperty()只会检查实例的第一级prototype,in搜索整条原形链直到找到或找不到.

访问嵌套成员,成员嵌套越深，访问速度越慢.**可以通过将嵌套成员临时保存为本地变量来提高速度**

### 优化作用域搜索

- 将全局变量保存到本地
- 嵌套成员保存为本地变量

## DOM

DOM是与语言无关的API,在浏览器中接口是通过JavaScript实现.

浏览器通常将JavaScript实现与DOM实现保持独立,例如:IE中JavaScript是jscript.dll,DOM是mshtml.dll;chrome中JavaScript是V8引擎.使用WebKit的WebCore库渲染页面;Safari使用WebKit的WebCode处理DOM和渲染

由于js和dom分离,所有每次使用js操作dom都会带来性能损耗.操作越多损耗越多

### 节点克隆

`element.cloneNode()`克隆速度比createElement要快一些

### HTML集合

- document.getElementsByName()
- document.getElementsByClassName()
- document.getElementsByTagName_r()
- document.images
- document.links
- document.forms
- document.forms[0].elements

上面这些方法和属性返回HTMLCollection对象,类似于数组,但没有数组的方法,只有一个length属性

**HTMLCollection对象是虚拟的,也就是说底层文档更新时,他们将自动更新.当读取length属性时就会重新查询一次**

```
var alldivs = document.getElementsByTagName_r('div'); 
for (var i = 0; i < alldivs.length; i++) {
    document.body.appendChild(document.createElement('div'))
}
```
**上面这段代码每次循环读取一次length属性,所以每次进行一次重新查询,而每次循环的时候就添加一个div,导致底层文档更新,因而每次冲查询后length都会加1,这样i永远小于length,变成死循环**

### 抓取DOM

childNodes获取所有子节点(包括注释节点,文本节点),nextSibling获取兄弟节点.

**childNodes也是一个集合需要小心处理**

DOM的一些API可以直接获取**元素节点**

children             -------    childNodes
childElementCount    -------    childNodes.length
firstElementChild    -------    firstChild
lastElementChild     -------    lastChild
nextElementSibling   -------     nextSibling
previousElementSibling --------  previousSibling

### 选择器API

`querySelectorAll('#menu a');querySelectorAll('div.warning,div.notice')同时选择多个;`利用css选择器

### 重绘和重排版

当浏览器下载完所有页面 HTML 标记，JavaScript，CSS，图片之后，它解析文件并创建两个内部数据结构:表示页面结构的一棵DOM树,表示 DOM 节点如何显示的一棵渲染树

渲染树为每个需要显示的DOM树节点**存放至少一个节点**(隐藏 DOM 元素在渲染树中没有对应节点),渲染树上的节点成为"框"或"盒",符合 CSS 模型的定义，将页面元素看作一个具有填充、边距、边框和位置的盒。**一旦 DOM 树和渲染树构造完毕，浏览器就可以显示(绘制)页面上的元素了**

当 DOM 改变影响到元素的几何属性(宽和高)——例如改变了边框宽度或在段落中添加文字，将发生 一系列后续动作——浏览器需要重新计算元素的几何属性，而且其他元素的几何属性和位置也会因此改变受到影响。**浏览器使渲染树上受到影响的部分失效，然后重构渲染树**。这个过程被称作重排版。**重排版完成时，浏览器在一个重绘进程中重新绘制屏幕上受影响的部分**

**并非所有dom修改都出发重排**

重排事件:
- 添加或删除可见的 DOM 元素
- 元素位置改变
- 元素尺寸改变(因为边距，填充，边框宽度，宽度，高度等属性改变)
- 内容改变，例如，文本改变或图片被另一个不同尺寸的所替代
- 最初的页面渲染
- 浏览器窗口改变尺寸

### 查询并刷新渲染树改变
**强迫队列刷新并要求所有计划改变的部分立刻应用**。**获取布局信息的操作将导致刷新队列动作**,因为:
- offsetTop, offsetLeft, offsetWidth, offsetHeight
- scrollTop, scrollLeft, scrollWidth, scrollHeight
- clientTop, clientLeft, clientWidth, clientHeight
- getComputedStyle()

在改变风格的过程中，最好不要使用前面列出的那些属性。任何一个访问都将刷新渲染队列

**将布局信息进行缓存,来优化性能**

### 最小化重绘和重排版

`el.style.cssText = 'border-left: 1px; border-right: 2px; padding: 5px;';`

`el.className = 'active';`

`el.style.display='none';.....el.style.display='block';`

文档片断,实际添加的是文档片断的子节点群，而不是片断自己
```
var fragment = document.createDocumentFragment(); 
appendDataToElement(fragment, data); 
document.getElementById('mylist').appendChild(fragment);
```

覆盖老节点
```JavaScript
var old = document.getElementById('mylist');
var clone = old.cloneNode(true); 
appendDataToElement(clone, data); 
old.parentNode.replaceChild(clone, old);
```

**最好是使用文档片段,性能最好**

### 动画

最好使用局对定位来让元素位于页面的布局流之外,减少重拍的影响范围


### 事件托管

元素有一个或多个事件句柄与之挂接,连接每个句柄都是有代价的,无论其形式是加重了页面负担,还是表现在运行期的运行时间上,**特别是因为事件 挂接过程都发生在 onload(或 DOMContentReady)事件中，对任何一个富交互网页来说那都是一个繁忙的时间段**,**浏览器需要保存每个句柄的记录，占用更多内存**.

DOM 事件的技术是事件托管。它基于这样一个事实:**事件逐层冒泡总能被父元素捕获**。采用事件托管技术之后，**只需要在一个包装元素上挂接一个句柄，用于处理子元素发生的所有事件**。

## 算法和流 程控制

## 创建并部署高性能 JavaScript 应用程序

