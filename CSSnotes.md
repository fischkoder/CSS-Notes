# CSS in Depth Notes

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
