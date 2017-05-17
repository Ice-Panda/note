# CSS

## 选择器

- h1 input 后代元素
- h1 > input 子元素
- h1 + p 相邻的下一个同一级元素
- [attribute] 用于选取带有指定属性的元素。
- [attribute=value] 用于选取带有指定属性和值的元素。
- [attribute~=value] 用于选取属性值中包含指定词汇的元素。
- [attribute|=value] 用于选取带有以指定值开头的属性值的元素，该值必须是整个单词。
- [attribute^=value] 匹配属性值以指定值开头的每个元素。
- [attribute$=value] 匹配属性值以指定值结尾的每个元素。
- [attribute*=value] 匹配属性值中包含指定值的每个元素。
- :active 向被激活的元素添加样式。
- :focus 向拥有键盘输入焦点的元素添加样式。
- :hover 当鼠标悬浮在元素上方时，向元素添加样式。
- :link 向未被访问的链接添加样式。
- :visited 向已被访问的链接添加样式。
- :first-child 向元素的第一个子元素添加样式。
- :last-child 向元素的最后一个子元素添加样式。
- :after 向元素的后面添加样式。
- :lang 向带有指定lang属性的元素添加样式。
- :first-letter 向文本的第一个字母添加特殊样式。
- :first-line 向文本的首行添加特殊样式。
- :before 在元素之前添加内容。
- :after 在元素之后添加内容。

## 字体

```css
@font-face
{
font-family: myFirstFont;
src: url('Sansation_Light.ttf'),
     url('Sansation_Light.eot'); /* IE9+ */
}
div
{
font-family:myFirstFont;
}
```

## 2D转换

```css
div
{
transform: rotate(30deg);
-ms-transform: rotate(30deg);        /* IE 9 */
-webkit-transform: rotate(30deg);    /* Safari and Chrome */
-o-transform: rotate(30deg);        /* Opera */
-moz-transform: rotate(30deg);        /* Firefox */
}
```

2D转换支持的属性

- transform 向元素应用 2D 或 3D 转换。 3
- transform-origin 允许你改变被转换元素的位置。 3

- matrix(n,n,n,n,n,n) 定义 2D 转换，使用六个值的矩阵。

- translate(x,y) 定义 2D 转换，沿着 X 和 Y 轴移动元素。

- translateX(n) 定义 2D 转换，沿着 X 轴移动元素。
- translateY(n) 定义 2D 转换，沿着 Y 轴移动元素。
- scale(x,y) 定义 2D 缩放转换，改变元素的宽度和高度。
- scaleX(n) 定义 2D 缩放转换，改变元素的宽度。
- scaleY(n) 定义 2D 缩放转换，改变元素的高度。
- rotate(angle) 定义 2D 旋转，在参数中规定角度。
- skew(x-angle,y-angle) 定义 2D 倾斜转换，沿着 X 和 Y 轴。
- skewX(angle) 定义 2D 倾斜转换，沿着 X 轴。
- skewY(angle) 定义 2D 倾斜转换，沿着 Y 轴。

## 3D转换

```css
div
{
transform: rotateX(120deg);
-webkit-transform: rotateX(120deg);    /* Safari 和 Chrome */
-moz-transform: rotateX(120deg);    /* Firefox */
}
```

转换属性

- transform 向元素应用 2D 或 3D 转换。
- transform-origin 允许你改变被转换元素的位置。
- transform-style 规定被嵌套元素如何在 3D 空间中显示。
- perspective 规定 3D 元素的透视效果。
- perspective-origin 规定 3D 元素的底部位置。
- backface-visibility 定义元素在不面对屏幕时是否可见。 2D Transform 方法 函数 描述
- matrix3d(n,n,n,n,n,n,n,n,n,n,n,n,n,n,n,n) 定义 3D 转换，使用 16 个值的 4x4 矩阵。
- translate3d(x,y,z) 定义 3D 转化。
- translateX(x) 定义 3D 转化，仅使用用于 X 轴的值。
- translateY(y) 定义 3D 转化，仅使用用于 Y 轴的值。
- translateZ(z) 定义 3D 转化，仅使用用于 Z 轴的值。
- scale3d(x,y,z) 定义 3D 缩放转换。
- scaleX(x) 定义 3D 缩放转换，通过给定一个 X 轴的值。
- scaleY(y) 定义 3D 缩放转换，通过给定一个 Y 轴的值。
- scaleZ(z) 定义 3D 缩放转换，通过给定一个 Z 轴的值。
- rotate3d(x,y,z,angle) 定义 3D 旋转。
- rotateX(angle) 定义沿 X 轴的 3D 旋转。
- rotateY(angle) 定义沿 Y 轴的 3D 旋转。
- rotateZ(angle) 定义沿 Z 轴的 3D 旋转。
- perspective(n) 定义 3D 转换元素的透视视图。

## 过度

```css
div
{
transition: width 2s;
-moz-transition: width 2s;    /* Firefox 4 */
-webkit-transition: width 2s;    /* Safari 和 Chrome */
-o-transition: width 2s;    /* Opera */
}
```

过度属性

- transition 简写属性，用于在一个属性中设置四个过渡属性。
- transition-property 规定应用过渡的 CSS 属性的名称。
- transition-duration 定义过渡效果花费的时间。默认是 0。
- transition-timing-function 规定过渡效果的时间曲线。默认是 "ease"。
- transition-delay 规定过渡效果何时开始。默认是 0。

## 动画

```html
<!DOCTYPE html>
<html>
<head>
<style>
div
{
width:100px;
height:100px;
background:red;
animation:myfirst 5s;
-moz-animation:myfirst 5s; /* Firefox */
-webkit-animation:myfirst 5s; /* Safari and Chrome */
-o-animation:myfirst 5s; /* Opera */
}

@keyframes myfirst
{
from {background:red;}
to {background:yellow;}
}

@-moz-keyframes myfirst /* Firefox */
{
from {background:red;}
to {background:yellow;}
}

@-webkit-keyframes myfirst /* Safari and Chrome */
{
from {background:red;}
to {background:yellow;}
}

@-o-keyframes myfirst /* Opera */
{
from {background:red;}
to {background:yellow;}
}
</style>
</head>
<body>

<div></div>

<p><b>注释：</b>本例在 Internet Explorer 中无效。</p>

</body>
</html>

<!DOCTYPE html>
<html>
<head>
<style>
div
{
width:100px;
height:100px;
background:red;
position:relative;
animation:myfirst 5s;
-moz-animation:myfirst 5s; /* Firefox */
-webkit-animation:myfirst 5s; /* Safari and Chrome */
-o-animation:myfirst 5s; /* Opera */
}

@keyframes myfirst
{
0%   {background:red; left:0px; top:0px;}
25%  {background:yellow; left:200px; top:0px;}
50%  {background:blue; left:200px; top:200px;}
75%  {background:green; left:0px; top:200px;}
100% {background:red; left:0px; top:0px;}
}

@-moz-keyframes myfirst /* Firefox */
{
0%   {background:red; left:0px; top:0px;}
25%  {background:yellow; left:200px; top:0px;}
50%  {background:blue; left:200px; top:200px;}
75%  {background:green; left:0px; top:200px;}
100% {background:red; left:0px; top:0px;}
}

@-webkit-keyframes myfirst /* Safari and Chrome */
{
0%   {background:red; left:0px; top:0px;}
25%  {background:yellow; left:200px; top:0px;}
50%  {background:blue; left:200px; top:200px;}
75%  {background:green; left:0px; top:200px;}
100% {background:red; left:0px; top:0px;}
}

@-o-keyframes myfirst /* Opera */
{
0%   {background:red; left:0px; top:0px;}
25%  {background:yellow; left:200px; top:0px;}
50%  {background:blue; left:200px; top:200px;}
75%  {background:green; left:0px; top:200px;}
100% {background:red; left:0px; top:0px;}
}
</style>
</head>
<body>

<p><b>注释：</b>本例在 Internet Explorer 中无效。</p>

<div></div>

</body>
</html>
```

动画属性

- @keyframes 规定动画。
- animation 所有动画属性的简写属性，除了 animation-play-state 属性。
- animation-name 规定 @keyframes 动画的名称。
- animation-duration 规定动画完成一个周期所花费的秒或毫秒。默认是 0。
- animation-timing-function 规定动画的速度曲线。默认是 "ease"。
- animation-delay 规定动画何时开始。默认是 0。
- animation-iteration-count 规定动画被播放的次数。默认是 1。
- animation-direction 规定动画是否在下一周期逆向地播放。默认是 "normal"。
- animation-play-state 规定动画是否正在运行或暂停。默认是 "running"。
- animation-fill-mode 规定对象动画时间之外的状态。

##  新的用户界面属性(基本不支持)

- appearance 允许您将元素设置为标准用户界面元素的外观 3
- box-sizing 允许您以确切的方式定义适应某个区域的具体内容。 3
- icon 为创作者提供使用图标化等价物来设置元素样式的能力。 3
- nav-down 规定在使用 arrow-down 导航键时向何处导航。 3
- nav-index 设置元素的 tab 键控制次序。 3
- nav-left 规定在使用 arrow-left 导航键时向何处导航。 3
- nav-right 规定在使用 arrow-right 导航键时向何处导航。 3
- nav-up 规定在使用 arrow-up 导航键时向何处导航。 3
- outline-offset 对轮廓进行偏移，并在超出边框边缘的位置绘制轮廓。 3
- resize 规定是否可由用户对元素的尺寸进行调整。 3
