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

