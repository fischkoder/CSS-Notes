# CSS in Depth Notes

[toc]

## Chapter 1 层叠、优先级与继承

### 优先级理解

**优先级顺序**

1.In-line Style

理论上行内样式的优先级最高，不会被external css覆盖，若需要覆盖行内样式，可以使用 **!important**加在对应样式声明之后

2.Selector Style

选择器主要出现在页面的`<style>`标签里或者是外部的CSS文件，选择器优先级：

id `#` > class `.`> tag

* 选择器里ID最多的会胜出，优先套用
* ID数量一致，CLASS最多的会胜出
* ID，CLASS一致拥有最多html tag的胜出

### Link样式

链接样式link

`<a>`这个tag可以设定超链接的样式，主要有如下几个属性：

* `a:link{}`
* `a:visited{}`
* `a:hover{}`
* `a:active{}`

### 层叠值

在页面样式声明中胜出的会成为`层叠值`

也可理解为在层叠中胜出，作为层叠的结果，应用到某一个元素中时特定属性的值

PS: selector 中尽量不使用id，因为id会大幅度提升优先级。若想覆盖掉有id的selector则需要再增加一个class以增加优先级；然后就是不要随意使用`!important`；另根据个人理解，`class`具备一定编组性质，`id`配合`class`使用可以实现某编组内的单个要素覆盖。

### 继承

继承是依据DOM tree，使得某个元素会继承上一层的某些元素值

例如给body 增加字体的样式，整个网页都会生效

**inherit**关键字 

``` css
.footer {
    color: #666
}

.footer a {
    color: inherit
}
```

footer中的`<a>`会继承来自footer的颜色设定

**initial**关键字

如果想撤销某个元素的style，可以采取给这个元素的对应属性值设定为**initial**从而使其设定值回归到默认初始值

例如<br>
`color:initial`在大多数浏览器里是black<br>
`display:initial`的初始值是inline

### 简写属性

关于简写属性是可以把某一类属性通过简写到一行，通过空格隔开，实现一条表达控制所有。但是简写属性存在一个问题就是如果有某些属性在简写表达式中没有明确，那么系统会自动套用默认值，会造成一些不必要的覆盖

**简写的值顺序**

在类似于`margin`,`padding`这类属性，简写数值设定遵循上、右、下、左的顺序

``` css
.nav {
    padding: 10px 15px 0 5px
}
```
上述的顺序就会是上10，右15，下0，左5

但是存在一种等价情况
```
padding: 1em 2em
padding: 1em 2em 1em
padding: 1em 2em 1em 2em
```

因为在未明确全部padding的数值时，只定义`上`，和`右`，实际上`下`和`左`会对应继承`上`和`右`的设定

所以上述三种写法实现的结果是同样的

但是对于`background-position, box-shadow, text-shadow`这类，其设定只包含两个坐标即x,y轴

``` css
.button {
    box-shadow: 10px 2px
}
```
即表达水平向右10px，垂直向下2px

## Chapter 2 相对单位

### em 与 rem

在CSS中，1em等于当前元素的字号，即如果字号是14px，那么1em就是14px，2em则为28px

对于em与当前元素字号的运算结果，其绝对值被称为`计算值Computed value`

范例中
``` html
<span class="box box-small">Small</span>
<span class="box box-large">Large</span>
```
可以通过`空格`给class里面定义多个内容实现编组与单独调整

``` css
.box{
    padding: 1em;
}

.box-small {
    font-size: 12px;
}

.box-large {
    font-size: 18px;
}
```

这样两个span内的1em就不同了

#### em定义字号

如果像`font-size=1.2em`这样定义字号，那么其em的参考系，将是它所继承的上一级元素的对应字号

比如一个元素可以从`body`取得一个字号的值，那么它自己用`em`来定义的时候，参考值就是`body`的字号

如果一个元素的`font-size`以`em`定义了，那么后面比如再设定padding的时候使用了`em`,padding的距离就会是 `font-size * em数 * em数`,即如果`body`的`font-size` 是16px, 那么这个元素的`font-size`就是`1.2 * 16px`,那么他的`padding`如果是`1.2em`,那么padding的值就是`1.2 * 16px * 1.2`

如果使用值小于1的`em`连续继承，会造成字体逐层减小 `0.x * 0.x * 0.x ...`

#### rem定义字号

`rem`的意思是root em，em参考值是相对于根元素的的值。无论在哪个位置使用rem，所取得的rem计算值都相同

``` css
:root {
    font-size: 1em;
}

ul {
    font-size: 0.8rem;
}
```
root是一个伪类，等价于html selector。通常默认em对应的像素是16px。ul则直接通过root的font-size进行计算

### 构造响应式面板

我们可以通过媒体查询media query`@media`来取得当前屏幕的尺寸
同时我们可以通过`@media`设定对应的样式
``` css
:root {
    font-size: 0.75em;
}

@media (min-width: 800px) {
    :root {
        Font-size: 0.875em;
    }
}

@media (min-width: 1200px) {
    :root {
        font-size: 1em;
    }
}
```

通过这种方式可以对于全局的字号进行控制

#### 调整单个组件

给某个组件的类进行修改
`<div class="panel large">`
这样可以先为所有的panel定义好内容
``` css
.panel {
    font-size: 1rem;
    padding: 1em;
    border: 1pm solid #999;
    border-radius: 0.5em;
}

.panel > h2 {
    margin-top: 0;
    font-size: 0.8em;
    font-weight: bold;
    text-transform: uppercase;
}

.panel.large {
    font-size: 1.2rem;
}
```

如此，`.panel.large`会覆盖掉一开始的panel的`font-size` 从而使得标记了对应类的元素具备单独放大显示的样式

#### 可是区域的相对单位

`vh`:窗口可视范围高度的1/100
`vw`:窗口可视范围宽度的1/100
`vmin`:窗口可视范围中宽与高较小一方的1/100
`vmax`:窗口可视范围中宽与高较大一方的1/100

这样可以通过`vmin`与`vmax`自动匹配设备的水平与垂直显示的变化

``` css
.square {
    width: 90vmin;
    height: 90vmin;
    backgroud-color: #369;
}
```
#### 使用vw定义字号

可以利用`vw`的特性来自动匹配不同分辨率下的字号

#### 使用calc()定义字号

`calc()`函数可以对两个及以上的值进行基本运算。
函数中可以进行常规的`➕`,`➖`,`✖️`,`➗`
``` css
:root {
    font-size: calc(0.5em + 1vw);
}
```

#### 无单位的数值与行高

某些属性允许无单位数值，包括`line-height`,`z-index`,`font-weight`
无单位的0只能用于长度和百分比，比如内边距，边框，宽度等，而不能用于角度值

``` css
body {
    line-height: 1.2;
}

.about-us {
    font-size: 2em;
}
```
上述css描述中，行高设定了1.2，因为段落`.about-us`字号设定了2em,即`2em*16px`,后代单位集成了无单位的值，因此行高计算值就是`32px * 1.2`

倘若行高没有使用无单位行高，例如`1.2em`会造成行高仅有19.2px，每行的字符会重叠

### 自定义属性（CSS变量）

``` css
:root {
    --main-font: Helvetica, Arial, sans-serif;
}
```
变量前面通过`--`来表达声明，用于与css属性进行区分

我们通过如下方式来使用变量
``` css
:root {
    --main-font: Helvetica, Arial, sans-serif;
    --brand-color: #369;
}

p {
    font-family: var(--main-font);
    color: var(--brand-color);
}
```
同时，`var()`函数接受第二个参数，作为指定的备用值
``` css
:root {
    --brand-color: #369;
}

p {
    color: var(--secondary-color, blue);
}
```
因为没有定义过`--secondary-color`这个变量，因此`var()`函数会直接读取第二个参数作为备用选项

如果`var()`函数计算出来的值是一个非法值，对应的属性将被设置为其初始值

#### 动态改变自定义属性
自定义属性变量的核心在于可以层叠与继承，因此可以在多个选择器中定义相同的变量，这个变量在不同的位置可以有不同的值

``` css
:root {
    --main-bg: #fff;
    --main-color: #000;
}

.panel {
    background-color: var(--main-bg);
    color: var(--main-color);
}

.dark {
    background-color: #999;
    --main-bg: #333;
    --main-color: #fff;
}
```
以上的css样式套用在了两个panel中，第二个panel以class dark定义，在dark中，`--main-bg`,`--main-color`被重新定义，于是这个容器内的对应颜色被修改，其他地方还会是继承`:root`中所定义的内容

#### 使用JavaScript改变自定义属性
``` html
<script type="text/javascript">
    var rootElement = document.documentElement;
    var styles = getComputedStyle(rootElement);
    var mainColor = styles.getPropertyValue('--main-bg');
    console.log(String(mainColor).trim());
</script>
```
JavaScript 在浏览器中可以访问修改自定义属性
``` javascript
    var rootElement = document.documentElement;
    rootElement.style.setProperty('--main-bg','#cdf');
```
## Chapter 3 盒模型

### 元素宽度的问题

PS. IE浏览器不能正常渲染`<main>`为块级元素，需要增加`display:block`来修正这个bug

在使用水平分列，分为两列时我们通常如下操作：
``` html
<div class="container">
    <main class="main">
        <p>say something</p>
    </main>
    <aside class="sidebar">
        <div class="widget"></div>
    </aside>
</div>
```
``` css
.main {
    float: left;
    width: 70%;
    background-color: #fff;
    border-radius: .5em;
}

.sidebar {
    float: left;
    width: 30%;
    padding: 1.5em;
    background-color: #fff;
    border-radius: .5em;
}
```
此处会发生一个问题，`<main>`与`<aside>`并没有并排，而是发生了换行

因为`padding`导致了70%与30%的写法的元素实际宽度之和已经超出了100%

这要引入一个核心概念 **盒模型**

盒模型中包含四部分从外向内可以分为最外圈的**外边距margin**，之后向内是**边框border**，在向内是**内边距padding**，最后中心部分是**内容content**

一个元素的`height`,`width`宽与高是包含了盒模型的**外边距**加**边框**加**内边距**加内容本身

因此在案例中，1.5em的内边距`padding`，导致实际上是70%加上了3em再加30%，宽度超过100%，因此不能正确显示


#### 避免魔术数值

在修正上文中的错误时，有时候会用到反复测试到的结果，比如`.sidebar`的`width: 26%;`，这样的数值是一个魔术数值**magic number**，

为避免使用魔术数值，我们在案例中发现实际上宽度可知超出了`3em`，那么可以通过使用`calc()`函数来减去这`3em`实现正常并排显示的目的

#### 调整盒模型

除了上文的方式，我们还可以利用调整`box-sizing`的方式，来改变盒模型的行为

盒模型默认的`box-sizing`是`content-box`，就是指定参数只给到内容区域，不包含内边距、外边距和边框。
如果将盒模型的行为变成`box-sizing: border-box`, 则包含了内容区域，内边距和边框，这样设定的30%是包含了这些内容的，避免了渲染错误
所以除去采用上文的`calc()`方式以外，我们还可以采取`border-box`的参数设定，让元素之间的宽度总和等于100%

#### 全局调整盒模型

可以通过，如下方式全局设定所有元素与伪元素为`border-box`行为
``` css
*,
::before,
::after {
    box-sizing: border-box;
}
```
但是这样会带来一个问题，使用第三方css库的时候，可能会破坏其中某些布局，因此我们需要使用某些方式把第三方库的组件恢复成`content-box`

``` css
:root {
    box-sizing: border-box;
}

*,
::before,
::after {
    box-sizing: inherit; //盒模型不会被继承，所以需要inherit强制继承
}

.third-party-component {
    box-sizing: content-box;
}
```
上面`.third-party-component`是给第三方组件准备的类选择器，我们可以通过给第三方组件的顶级容器配置对应的类名，来恢复第三方css库的盒模型行为

经过这样的了解后，对于将元素隔开，我们可以采用增加外边距的方式
ps. 假设已经设定了`border-box`
``` css
.main {
    float: left;
    width: 70%;
    background-color: #fff;
    border-radius: .5em;
}

.sidebar {
    float: left;
    margin-left: 1%
    width: 29%;
    padding: 1.5em;
    background-color: #fff;
    border-radius: .5em;
}
```
另外也可以采取用`calc()`的方式，减去`margin`的值也可

### 元素的高度问题

`border-box`的设定依然适用于高度，高度的理解与计算方式与宽度一致

#### 控制溢出

控制内容溢出元素的容器范围，可以通过`overflow`这个属性控制，这项属性有4个值
`visible`--所有内容可见，允许内容溢出容器边缘
`hidden`--仅显示容器内部的部分，溢出部分不可见
`scroll`--容器会出现滚动条，可以滚动查看内容，内容不会溢出显示
`auto`--只有出现溢出的时候才会出现滚动条

水平方向溢出同样适用

#### 等高列

##### 表格布局

设置等高列，可以借助表格布局，将容器设置为`display:table`,然后将每一列设定为`display: cell`

``` css
.container {
    display: table;
    width: 100%;
}

.main {
    display: table-cell;
    width: 70%;
}

.sidebar {
    display: table-cell;
    width: 30%;
}
```
另外注意，如果是用`display: table` 外边距`margin`将不再生效

不同于`block`类型，table 的宽度不会默认扩展到100%，因此要单独声明
如果希望`table`类型中`table-cell`也产生间隔，就需要`border-spacing` 来定义，但是它有个副作用，`border-spacing`是全方位的，也就意味着`cell`不能对其其他`table`外的元素。解决方案可以通过给table加一个外部容器，然后根据设定的`border-spacing` 给table外的容器增加**负边距**，即相对应的负值来抵消table的设定

##### Flexbox

Flexbox不支持ie9以下浏览器

Flexbox不需要一个额外的`<div>`包裹元素，他会默认产生等高元素，也不需要使用负外边距

``` css
.container {
    display: flex;
}

.main {
    width: 70%;
}

.sidebar {
    width: 30%;
}
```

给容器设定为`display: flex`， 容器就变成了**弹性容器**，弹性容器中的子元素会默认是等高的，可以给子元素设定`width`与`margin`，flexbox可以妥善处理宽度之和超过100%

ps.除非必要，尽量不要给元素设定高度

#### min-height 与 max-height

`min-height`与`max-height`不会明确定义高度，它们只设定最大与最小值，这样元素就可以在设定的边界内自由决定高度的大小，在达到max边界前，内容不会溢出，会自动适配大小，同理还有`min-width`与`max-width`

#### 垂直居中内容

`vertical-align: middle`在很多情况下都不生效，不能做到让块级元素里内容垂直居中的原因是，这个声明只针对`table-cell`元素有效，对于行内元素，他控制该元素与同意行内其他元素之间的对齐关系

所以采用给出上下上等的`padding`值，这样容器会自动适应高度，匹配`padding`与内容的实际大小，从而实现垂直居中

### 负外边距(margin)

不同于`padding`和`width`，`margin`可以设定为负值
负值可以让元素拉伸到比当前所在容器更宽
上文中抵消table中`border-spacing`的影响也是负外边距的一个用途
但是负外边距要避免频繁使用

### 外边距折叠



## 附录 选择器 Selector

### 基础选择器：

`tagname` 类型或者标签选择器，直接匹配标签名
`.class` 类选择器匹配标签属性中的class名称
`#id` ID选择器，匹配标签属性中的id
`*` 通用选择器，匹配所有元素

### 组合器 Combinators：

组合器是将多个基础选择器连接起来组成一个复杂的选择器
例如：
`.nav-menu li`选择器中，两个基础选择器之间的空格被称作后代选择器`descendant combinator` 
空格的做法是后代与parent level应用同样的样式

`子组合器 >` 匹配目标元素是某个元素的直接后代 样式仅应用于这个后代
例如：`.parent > .child`
`相邻兄弟组合器 +` 匹配目标是紧跟在一个元素后面的一个元素 样式会仅仅应用于这个某个元素后面紧挨着的第一个元素
例如：`p + h2`
`通用兄弟组合器 ~` 匹配所有跟随在一个元素后面的元素 样式会应用到一个元素后面的所有元素
例如：`li.active ~ li`

这里解释一下 `ul li` 与 `ul > li`的区别
``` html
<ul>
    <li></li>   <!-- 后代选择器与子组合选择器都会选中这个元素>
    <li></li>   <!-- 后代选择器与子组合选择器都会选中这个元素>
    <li></li>   <!-- 后代选择器与子组合选择器都会选中这个元素>
    <ol>
        <li></li>  <!-- 只有后代选择器会选择这个元素>
    </ol>
</ul>
```
以上的区别主要是 子组合器`>`要求，其选择的后代必须是组合器左边元素的直接后代
对于`ul > li`而言，只有前三行符合这个要求，而`<ol>`内部的不属于`<ul>`的直接后代

### 复合选择器 Compound Selectors
指的是将多个选择器组合起来中间不采用任何组合器（空格 > + ~）进行连接

复合选择器会将能匹配全部选择器的元素进行样式应用
例如：
`.dropdown.is-active` 能够选中 `<div class="dropdown is-active"> 但是无法选中<div class="dropdown">`

### 伪类选择器 Psuedo Selectors
伪类选择器用于选中处于特定状态的元素。这个状态可以是由于用户交互或者元素相对于其父级与兄弟元素的位置。伪类选择器始终以`:`开始，优先级等同于`.class`

`:first-child` 匹配元素是其父级元素中的第一个子元素
`:last-child` 匹配的元素是其父级元素中的最后一个子元素
`:only-child` 匹配的元素是其父级元素中的唯一一个子元素，因此也没有兄弟元素
`:nth-child(an+b)` 匹配的元素是兄弟元素间特定的位置，`a`与`b`是整数，n从0开始递增
`:nth-last-child(an+b)` 同上，不过是从后往前的顺序
`:first-of-type` 匹配具有相同标签类型的元素中的第一个元素
`:last-of-type` 匹配具有相同标签类型的元素中的最后一个元素
`:only-of-type` 匹配满足某个标签类型的唯一一个元素
`:nth-of-type(an+b)` 原理类似`nth-child`但是是根据标签类型匹配
`:nth-last-of-type(an+b)` 同上，但是顺序变为从后往前
`:not(<selector>)`  匹配元素不是括号内选择器的元素。并且括号内只能是基础选择器，只能用于元素本身，无法用于排除祖先元素，也不允许包含多个选择器
`:empty` 所匹配的元素必须没有子元素，如果元素包含空格就无法被选中
`:focus` 匹配通过鼠标点击、触摸、tab而被选中获得焦点的元素
`:hover` 匹配鼠标指针悬停在上方的元素
`:root` 匹配文档根元素，对于html来说就是<html>
`:disabled` 匹配已禁用的元素，包括`input`,`select`,`button`元素
`:enabled` 匹配已启用的元素，既能够激活或者能够作为焦点的元素
`:checked` 匹配已经被选定的checkboxes、radio buttons或者select box options 
`:invalid` 根据输入类型的定义，匹配具有非法输入值的元素，例如`input`验证输入邮箱不正确时会被匹配
`:valid` 匹配了具有合法值的元素
`:required` 匹配设置了required属性的元素
`:optional` 匹配没有设置required属性的元素

### 伪元素选择器 Pseudo-element Selectors
类似于伪类选择器，不同于选择特定状态的元素，伪元素选择器作用于文档中的一部分，并不会作用于某个特定的HTML元素。他们可能只作用于一个元素中的一部分，甚至会插入一些内容到html定义的范围之外。 伪元素选择器以`::`开始,其优先级与类型选择器`tagname`相等

`::before` 创建一个伪元素，使其成为所匹配元素的第一个元素，该元素默认是inline element，可用于插入文字，图片或者其他形状。`内容`的属性被明确了才会让元素出现,`.menu::before`
`::after` 创建一个伪元素，使其成为所匹配元素的最后一个元素，该元素默认是inline element，可用于插入文字，图片或者其他形状。`内容`的属性被明确了才会让元素出现,`.menu::after`
`::first-letter` 用于匹配元素的的第一个文本字符的样式 `h2::first-letter`
`::first-line` 用于指定匹配元素的第一行文本样式
`::selection` 用于制定用户使用鼠标高粱选择的文本样式。 通常用于改变选中文本的`background-color`。其只有少数属性可用，包括`color`,`background-color`,`cursor`,`text-decoration`

### 属性选择器 Attribute Selector

属性选择器是根据HTML tag里面的属性，进行匹配，优先级与`.class`一致

`[attr]` 匹配的元素拥有指定属性attr, 无论值是什么 `input[disabled]`
`[attr="value"]` 匹配元素拥有指定attr， 且属性值等于指定的字符串值 `input[type="radio"]`
`[attr^="value"]` 选择器匹配元素具有的指定attr，属性值的开头要匹配指定的值 `a[href^="https"]`
`[attr$="value"]` 选择器匹配元素具有的指定attr，属性值的结尾要匹配指定的值 `a[href$=".pdf"]`
`[attr*="value"]` 选择器匹配元素具有的指定attr，属性值中包含要匹配指定的值 `[class*="sprite-"]`
`[attr~="value"]` 选择器匹配元素具有的指定attr，属性值中包含要匹配指定的值,并且属性值是一个空格分隔成的列表 `a[rel="author"]`
`[attr|="value"]` 选择器匹配元素具有的指定attr，属性值等于指定的字符串值或者该字符串开头紧跟一个连字符，通常用于语言属性， `[lang|="es"]`

