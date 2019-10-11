---
title: sass快速教程
date: 2019-02-26 16:06:28
tags: [ sass, css ]
---
## 前言
本文旨在让帮助大家快速上手sass。
## 准备阶段
### 什么是sass？
学过css的都知道，css并不是一种编程语言，每个语句都是一条单独的命令。因此许多初学者常常不知所措。为了解决这一问题，让css更像是一门编程语言，css预编译处理器诞生了。`sass`便是其中的一种，经过编译之后可以形成浏览器可识别的css文件。与`sass`类似的还有`Less`、`Postcss`等。       
在css3之后，原生css也开始支持变量等特性（前缀为`–`），大部分浏览器均已支持这一特性。原生css变量的好处在于可直接与js代码进行互动，方便进一步执行复杂操作。
### 安装sass
要想使用sass，必须先安装Ruby。可直接从官网下载Ruby。Ruby安装结束后可直接通过以下命令安装sass。
> gem install sass
     
使用说明sass文件的后缀为`.scss`，意思是`sassy CSS（时髦的CSS）`。sass文件的编译方法主要有以下四种，分别对应不同的需求。
1. 单文件编译此命令用于将单独sass文件编译为指定路径的css文件。
> sass sass文件路径:css文件路径

2. 多文件编译使用该命令可以将目标文件夹内的所有sass文件编译并保存为指定文件夹下的css文件。
> sass sass文件夹:css文件夹

3. 动态编译使用此命令，sass会监测代码修改，并自动编译。
> sass - -watch sass文件路径:css文件路径sass - -watch sass文件夹:css文件夹

指定编译风格编译sass编译时提供四种编译风格，分别为
> nested：嵌套缩进的css代码（默认）。
> expanded：没有缩进的、扩展的css代码。
> compact：简洁格式的css代码。
> compressed：压缩后的css代码（生产环境常用）。    

使用以下命令可在编译时指定sass文件的编译风格。
> sass --style 编译风格 sass文件路径 css文件路径

## 语法
### 变量
sass允许定义变量。所有变量均以`$`开头。若重复声明变量值，则变量值以变量使用处之前最后一次声明的值为准。
```scss
$black: #000;

div{
    color: $black;
}    
```

若需将变量嵌套入字符串中，需将变量写在`#{}`内。
```scss
$side: right;
div{
    border-#{$side}-radius: 20px;
}
```

### 计算
sass允许在代码中进行计算。
```scss
div{
    margin: (14px/2);    
    top: 50px + 100px;    
    right: $var * 10%;
}
```

> 注意：
> 如计算后的结果需要保留单位，则应该在参与运算的数中添加单位，而不能通过字符串拼接的方式为结果添加单位。

```scss
div{    
    top: 50 + 20 + 'px'; /* 错误！ */    
    top: 50px + 20px; /* 正确！ */
}
```

### 嵌套
#### 选择器嵌套
sass实现了选择器嵌套，以解决重复书写前缀以及相同前缀css代码之间关系不够明确等问题。如以下css代码
```scss
div h1{    
    color: #000;
}
```

在sass中可写为
```scss
div{    
    h1{        
        color: #000;    
    }
}
```

这种写法不仅避免了重复书写前缀，还让css类之间的关系变得更加清晰。   
属性嵌套sass还允许属性嵌套。如针对`border-color`和`border-radius`属性
```scss
div{    
    border: {        
        color: #000;        
        radius: 20px;    
    }
}
```

> 注意：`border`后必须加`:`。

#### 父元素选择器&
为了使用伪类，必须在嵌套代码中使用父选择器`&`来指代父元素。直接在嵌套块中无法使用伪类！
```scss
/* css代码 */
div:hover{ 
    color: red; 
}

/* sass代码 */div{
    &:hover{
        color: red;
    }
}
```

#### 循环语句
sass支持`for循环`、`while循环`以及`each循环`（效果类似于`for..of循环`）。

##### for循环命令 @for
```scss
/* sass代码 */
@for $i from 1 to 3 {      
    .border-#{$i} { 
        border: #{$i}px solid blue; 
    }
}
```
##### while循环命令 @while
```scss
/* sass代码 */
$i: 3;

@while $i > 0 {    
    .item-#{$i} { 
        width: $i * 5px; 
    }    
    
    $i: $i - 1;
}
```
##### each循环命令 @each
`each循环`是一个比较特殊的循环，可近似理解为`for..of循环`。示例代码如下
```scss
/* sass代码 */
@each $member in a, b, c{    
    .#{$member} {        
        background-image: url("/image/#{$member}.jpg");    
    }
}
```

> 注意：
> `each循环`会遍历`in`之后的所有值，这些值不一定是变量，也可以是如示例代码中的类字符串。

#### 条件语句
sass支持`if..else`条件判断语句。写为`@if`和`@else`。
```scss
@if lightness($color) > 30% {    
    background-color: #000;
} @else {    
    background-color: #fff;
}
```

#### 自定义函数
sass支持用户自定义函数。一般用于完成一些重复且繁琐的单位转换等计算。函数用`@function`关键字声明，返回值用`@return`关键字声明。
```scss
@function double($n) {    
    @return $n * 2;
}

#sidebar {    
    width: double(5px);
}
```

#### 代码复用
继承sass允许一个类继承另一个类，使用`@extend`命令实现。
```scss
.class1{ 
    color: #red; 
}

.class2{    
    @extend .class1;    
    font-size: 5px;
}
```

#### 引入文件
使用`@import`命令用于引入外部文件。
```scss
@import "path/filename.scss";
```
.scss后缀可省略。

#### mixin
mixin是一部分可复用的css代码块，将其当做函数的简化版本。通过`@mixin`声明，使用时通过`@include`引用。mixin还可指定参数和默认值，使用时可传入参数。示例如下
```scss
@mixin left($value: 10px) {      
    float: left;    
    margin-right: $value;
}

div {    
    @include left(20px);
}
```