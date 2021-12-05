# CSS in Depth Notes

[toc]

## Chapter 1

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

## Chapter 2

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

### rem定义字号

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

