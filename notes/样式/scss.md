# sass

## Scss的两种注释

#### 多行注释`/* ... */`

多行注释会编译到`.css`文件中,`compressed`（压缩）模式下除外， 将 `!`作为多行注释的第一个字符表示在压缩输出模式下也保留这条注释，通常用于添加版权信息。

```scss
/*!
* 我是
* 多行
* 注释
*/
body { color: black; }

// 编译后
/*!
* 我是
* 多行
* 注释 
*/body{color:#000}
```

#### 单行注释`//`

单行注释不会编译到`.css`文件。

```scss
// 我是单行注释
body { color: black; }


// 编译后
body {
  color: black;
}
```

### 占位操作

```scss
// 占位
#{变量}
```

## 

## 变量



### 使用

原生`css`中的变量，使用`--变量名:变量值`定义，`var(--变量名)`进行使用。

```css
:root {
    --color: #F00;
}
p {
    color: var(--color);
}
```

`scss`中的变量，以美元符号`$`开头，赋值方法与 `css`属性的写法一样。

```scss
$color:#F00;
p {
    color: $color;
}

// 编译为
p {
    color: #F00;
}
```



### 5条变量规则

下文的`mixin`和`function`命名也遵循`1234`条规则：

- 变量以美元符号`$`开头，后面跟变量名；

- 变量名是不以数字开头的可包含字母、数字、下划线、横线（连接符）；

- 通过连接符`-`与下划线`_`定义的同名变量为同一变量；

- 变量一定要先定义，后使用；

- 写法同`css`，即变量名和值之间用冒号`:`分隔；



### 变量前缀

```scss
// 定义
$prefixCls: 'pre';

// 使用
.#{$prefixCls} {

  &-text {
    // 属性
  }
}
```

### 2种变量作用域



#### 变量作用域分为**全局变量域**和**局部变量域**：

- 全局变量：声明在最外层的变量，可在任何地方使用；

- 局部变量：嵌套规则内定义的变量只能在嵌套规则内使用。



#### 将局部变量转换为全局变量可以添加`!global` 声明。

```scss
$color: red;
.container {
    $height: 500px;
    $font-size: 16px !global;
    font-size: $font-size;
    color: $color;
    height: $height;
}
.footer {
    /**$font-size使用!global 申明成全局变量了*/
    font-size: $font-size; 
    /**
    * Error: Undefined variable. 
    * $height是.container下的局部变量，无法在.footer下面编译
    */
    height: $height;
}

// 编译成css
.container {
    font-size: 16px;
    color: red;
    height: 500px;
}

.footer {
     /**$font-size使用!global 申明成全局变量了*/
    font-size: 16px;
}

```

### 7种主要的数据类型

`scss`支持`7`种主要的数据类型：

- 数字，`1rem、2vh、13、 10px`；

- 字符串，分有引号字符串与无引号字符串，`"foo"、 'bar'、baz`；

- 颜色，`blue, #04a3f9, rgba(255,0,0,0.5)`；

- 布尔型，`true`和`false`；

- 空值，`null`是其类型的唯一值。表示缺少值，通常由函数返回以表示缺少结果；

- 数组 (`list`)，用空格或逗号作分隔符，`1.5em 1em 0 2em,Helvetica,Arial,sans-serif`；

- `maps`， 相当于 `JavaScript`的 `object`，`(key1: value1, key2: value2)`；

```scss
$layer-index: 10;
$border-width: 3px;

$font-weight: bold;

$font-base-family: "Open Sans", Helvetica, Sans-Serif;
$block-base-padding: 6px 10px 6px 10px;

$top-bg-color: rgba(255, 147, 29, 0.6);

$blank-mode: true;

$var: null;

$fonts: (
  serif: "Helvetica Neue",
  monospace: "Consolas",
);

.container {
  font-family: $font-base-family;
  font-size: $font-size;
  padding: $block-base-padding;

  @if $blank-mode {
    background-color: #000;
  } @else {
    background-color: #fff;
  }

  content: type-of($var);
  content: length($var);
  color: $top-bg-color;
}

// 如果列表中包含空值，则生成的CSS中将忽略该空值。
.wrap {
  font: 18px $font-weight map-get($fonts, "sans");
}


// 编译为
.container {
  font-family: "Open Sans", Helvetica, Sans-Serif;
  font-size: 16px;
  padding: 6px 10px 6px 10px;
  background-color: #000;
  content: null;
  content: 1;
  color: rgba(255, 147, 29, 0.6);
}

.wrap {
  font: 18px bold; // 如果列表中包含空值，则生成的CSS中将忽略该空值。
}

```

`scss`属性也支持其他值，比如`Unicode`字符集，或`!important` 声明。但是`scss` 不会特殊对待这些属性值，一律视为无引号字符串。

```scss
$color: red;
.container {
    color: $color !important;
}

// 编译为
.container {
  color: red !important;
}
```

### !default

可以在变量的结尾添加`!default`来给变量设置默认值，有点类似`Javascript`的逻辑运算符`let content=content || "Second content"`。注意，变量是 `null`时将视为未被`!default`赋值。

```scss
$content: "First content";
// 如果$content之前没定义就使用如下的默认值
$content: "Second content" !default;
#main {
    content: $content;
}


// 编译为
#main {
  content: "First content";
}

```



### 插值语句

通过 `#{}` 插值语句可以在选择器、属性名、注释中使用变量，使用`#{}`插值语句将变量包裹起来即可，和`js`中的模板字符串很像。

```scss
$font-size: 12px;
$line-height: 30px;
$class-name: danger;
$attr: color;
$author: "福大命大";

p {
    font: #{$font-size}/#{$line-height} Arial Helvetica, sans-serif;
}

/* 
* 这是文件的说明部分
* @author: #{$author}
*/

a.#{$class-name} {
    border-#{$attr}: #f00;
}



// 编译为
p {
    font: 12px/30px Arial Helvetica, sans-serif;
}

/* 
* 这是文件的说明部分
* @author: 福大命大
*/
a.danger {
    border-color: #f00;
}
```

### 圆括号

圆括号`()`可以用来影响运算的顺序，和数学中的效果是一致的。

### 运算符

相等运算`==`和不相等运算`!=`。所有数据类型均支持 `==` 和`!=`，另外，每种数据类型也有其各自支持的运算方式。

```scss
$theme:"blue";
.container {
    @if $theme=="blue" {
        background-color: red;
    }
    @else {
        background-color: blue;
    }
}

.container {
    @if $theme !="blue" {
        background-color: red;
    }
    @else {
        background-color: blue;
    }
}


// 编译为
.container {
  background-color: red;
}

.container {
  background-color: blue;
}
```

### 关系运算符

四个关系运算符`< > >= <=`

```scss
$theme:3;
.container {
    @if $theme >= 5 {
        background-color: red;
    }
    @else {
        background-color: blue;
    }
}


// 编译为
.container {
    background-color: blue;
}
```

### 布尔运行符

三个布尔运算符`and or not`

```scss
$width: 100;
$height: 200;
$last: false;
div {
  @if $width>50 and $height<300 {
    font-size: 16px;
  } @else {
    font-size: 14px;
  }

  @if not $last {
    border-color: red;
  } @else {
    border-color: blue;
  }

  @if $width>500 or $height<300{
    line-height: 20px;
  } @else {
    line-height: 50px;
  }
}


// 编译为
div {
    font-size: 16px;
    border-color: red;
    line-height: 20px;
}
div {
    font-size: 16px;
    border-color: red;
}
```

### 数字操作符

`+ - * / %`

```scss
/* 纯数字与百分号或单位运算时会自动转化成相应的百分比与单位值 */
.container {
    /* ================== + 运算===================== */
    width: 50 + 20;
    width: 50 + 20%;
    width: 50% + 20%;
    width: 10pt + 20px;
    width: 10pt + 20;

    /* ================== - 运算===================== */
    height: 50 - 20;
    height: 10 - 20%;
    height: 50pt - 20px;

    /* ================== * 运算===================== */
    height: 50 * 30;
    height: 10 * 30%;
    height: 50 * 2px;
    height: 50pt * 4;

    /* ==================/ 运算 (除完后最多只能保留一种单位)===================== */
    $width: 100px;
    width: 10/5;
    width: 10px / 5px;
    width: 10px / 20;
    width: ($width/2); // 使用变量与括号
    height: (500px/2); // 使用了括号

    /* ==================% 求余运算===================== */
    width: 10 % 3;
    width: 50px % 7;
    width: 50% % 7;
}


// 编译为
/* 纯数字与百分号或单位运算时会自动转化成相应的百分比与单位值 */
.container {
    /* ================== + 运算===================== */
    width: 70;
    width: 70%;
    width: 70%;
    width: 25pt;
    width: 30pt;
    /* ================== - 运算===================== */
    height: 30;
    height: -10%;
    height: 35pt;
    /* ================== * 运算===================== */
    height: 1500;
    height: 300%;
    height: 100px;
    height: 200pt;
    /* ==================/ 运算 (除完后最多只能保留一种单位)===================== */
    width: 10/5;
    width: 10px/5px;
    width: 10px/20;
    width: 50px;
    height: 250px;
    /* ==================% 运算===================== */
    width: 1;
    width: 1px;
    width: 1%;
}
```

`/`在`css`中有分隔数字的用途，在`scss`中，以下三种情况会进行除法运算：

1. 如果值或值的一部分，是变量或者函数的返回值；

2. 如果值被圆括号包裹；

3. 如果值是算数表达式的一部分。

```scss
$width: 1000px;
div {
    font: 16px/30px Arial, Helvetica, sans-serif; // 不运算
    width: ($width/2); // 使用变量与括号
    width: (#{$width}/2); // 使用 #{} 插值语句将变量包裹，避免运算。
    z-index: round(10)/2; // 使用了函数
    height: (500px/2); // 使用了括号
    margin-left: 5px + 8px/2px; // 使用了+表达式
}


// 编译为
div {
    font: 16px/30px Arial, Helvetica, sans-serif;
    width: 500px;
    width: 1000px/2;
    z-index: 5;
    height: 250px;
    margin-left: 9px;
}
```

> 如果需要使用变量，同时又要确保 `/` 不做除法运算而是完整地编译到 `css`文件中，只需要用 `#{}` 插值语句将变量包裹。

### 字符串运算

- `+`可用于连接字符串；

- 如果有引号字符串（位于 + 左侧）连接无引号字符串，运算结果是有引号的；

- 无引号字符串（位于 + 左侧）连接有引号字符串，运算结果则没有引号。

```scss
.container {
    content: "Foo " + Bar;
    font-family: sans- + "serif";
}


// 编译为


```







## 流程控制

### @if

`@if`语法和`js`类似，基本格式是`@if...@else if...@else`。

#### 使用

```scss
$theme:3;
.container {
    @if $theme >= 5 {
        background-color: red;
    }
    @else {
        background-color: blue;
    }
}

// 编译为
.container {
    background-color: blue;
}
```

#### 例子

```scss
@mixin triangle($direction:top, $size:30px, $border-color:black) {
  width: 0px;
  height: 0px;
  display: inline-block;
  border-width: $size;
  border-#{$direction}-width: 0;
  @if ($direction==top) {
     border-color: transparent transparent $border-color transparent;
     border-style: dashed dashed solid dashed;
  }
  @else if($direction==right) {
     border-color: transparent transparent transparent $border-color;
     border-style: dashed dashed dashed solid;
  }
  @else if($direction==bottom) {
     border-color: $border-color transparent transparent transparent;
     border-style: solid dashed dashed dashed;
  }
  @else if($direction==left) {
     border-color: transparent $border-color transparent transparent;
     border-style: dashed solid dashed dashed;
  }
}
.p0 {
     @include triangle($size:50px);
}

.p1 {
     @include triangle(right, 50px, red);
}

.p2 {
    @include triangle(bottom, 50px, blue);
}

.p3 {
     @include triangle(left, 50px, green);
}


// 编译为
.p0 {
    width: 0px;
    height: 0px;
    display: inline-block;
    border-width: 50px;
    border-top-width: 0;
    border-color: transparent transparent black transparent;
    border-style: dashed dashed solid dashed;
}

.p1 {
    width: 0px;
    height: 0px;
    display: inline-block;
    border-width: 50px;
    border-right-width: 0;
    border-color: transparent transparent transparent red;
    border-style: dashed dashed dashed solid;
}

.p2 {
    width: 0px;
    height: 0px;
    display: inline-block;
    border-width: 50px;
    border-bottom-width: 0;
    border-color: blue transparent transparent transparent;
    border-style: solid dashed dashed dashed;
}

.p3 {
    width: 0px;
    height: 0px;
    display: inline-block;
    border-width: 50px;
    border-left-width: 0;
    border-color: transparent green transparent transparent;
    border-style: dashed solid dashed dashed;
}
```

### @for

`for`在条件范围内重复操作，这个指令包含两种格式：

- `@for $var from <start> through <end>`；

- `@for $var from <start> to <end>`。

两者区别在于 `through` 与 `to`的含义：

- 使用 `through`时，条件范围包含 `<start>` 与 `<end>`的值；

- 使用 `to`时条件范围只包含`<start>`的值不包含`<end>`的值；

- `$var` 可以是任何变量，比如`$i`，`<start>` 和 `<end>` 必须是整数值。

```scss
// to
@for $i from 1 to 3 {
  #loading span:nth-child(#{$i}) {
      width: 20 * ($i - 1) + px;
  }
}

// 编译为
#loading span:nth-child(1) {
    width: 0px;
}
#loading span:nth-child(2) {
    width: 20px;
}


// through
#loading span:nth-child(1) {
    width: 0px;
}

#loading span:nth-child(2) {
    width: 20px;
}

#loading span:nth-child(3) {
    width: 40px;
}
```

### @each

`@each`指令的格式是`@each $var in $list` , `$var`可以是任何变量名，比如`$length` 或者`$name`，而`$list`是一连串的值，也就是值列表。

```scss
$color-list:red green blue turquoise darkmagenta;
@each $color in $color-list {
    $index: index($color-list, $color);
    .p#{$index - 1} {
        background-color: $color;
    }
}


// 编译为
.p0 {
    background-color: red;
}

.p1 {
    background-color: green;
}

.p2 {
    background-color: blue;
}

.p3 {
    background-color: turquoise;
}

.p4 {
    background-color: darkmagenta;
}
```

### @while

`@while` 指令循环输出直到表达式返回结果为 `false`。这样可以实现比`@for` 更复杂的循环。

```scss
$column:12;
@while $column>0 {
   .col-sm-#{$column} {
      width: $column / 12 * 100%;
   }
    $column:$column - 1;
}

// 编译为
.col-sm-12 {
    width: 100%;
}

.col-sm-11 {
    width: 91.6666666667%;
}

.col-sm-10 {
    width: 83.3333333333%;
}

.col-sm-9 {
    width: 75%;
}

.col-sm-8 {
    width: 66.6666666667%;
}

.col-sm-7 {
    width: 58.3333333333%;
}

.col-sm-6 {
    width: 50%;
}

.col-sm-5 {
    width: 41.6666666667%;
}

.col-sm-4 {
    width: 33.3333333333%;
}

.col-sm-3 {
    width: 25%;
}

.col-sm-2 {
    width: 16.6666666667%;
}

.col-sm-1 {
    width: 8.3333333333%;
}
```

### @import

`@import`算是一个比较简易的模块系统。`scss`拓展了`@import` 的功能，允许其导入 `scss`或 `sass`文件。被导入的文件将合并编译到同一个 `css`文件中，被导入的文件中所包含的变量或者混合指令 (`mixin`) 都可以在导入的文件中使用。 

#### 使用

```scss
// common.scss
$color:red;

// index.scss
@import "common.scss";
.container {
    border-color: $color;
}

// 编译为
.container {
  border-color: red;
}
```

> 以下情况下，`@import` 仅作为普通的`css`语句，不会导入`scss`文件：
> 
> - 文件拓展名是`.css`；
> 
> - 文件名以 `http://`开头；
> 
> - 文件名是`url()`；
> 
> - `@import`包含媒体查询。

```scss
@import "common.css";
@import url(common);
@import "http://xxx.com/xxx";
@import 'landscape' screen and (orientation:landscape);
```

`scss`允许同时导入多个文件，例如同时导入 `rounded-corners` 与`text-shadow` 两个文件，不用再单独写个`import`引入。

```scss
@import "rounded-corners", "text-shadow";
```

导入文件也可以使用 `#{}` 插值语句（下面有讲，这里把它理解成`js`中模板字符串就行）动态导入，但不是通过变量动态导入 `scss`文件，只能作用于 `css`的 `url()`导入方式。

```scss
$family: unquote("Droid+Sans");
@import url("http://fonts.googleapis.com/css?family=#{$family}");

// 编译为
@import url("http://fonts.googleapis.com/css?family=Droid+Sans");
```

### @Partials

如果需要导入 `scss`或者 `sass`文件，但又不希望将其编译为 `css`，只需要在文件名前添加下划线，这样会告诉 `scss`不要编译这些文件。注意：

- 导入语句中却不需要添加下划线；

- 不可以同时存在添加下划线与未添加下划线的同名文件，添加下划线的文件将会被忽略。

```scss
// _common.scss
$color: red;

// index.scss
@import "common.scss";
.container {
    border-color: $color;
}

// 编译为
.container {
    border-color: red;
}
```

> `_common.scss`文件不会编译成`_common.css`文件。
> 
> `Partials`主要是用来定义公共样式的，专门用于被其他的 `scss`文件 `import`进行使用的。

### 嵌套@import

大多数情况下，一般在文件的最外层（不在嵌套规则内）使用`@import`，其实，也可以将`@import` 嵌套进内层选择器或者 `@media` 中，与平时的用法效果相同，只是这样导入的样式只能出现在嵌套的层中，存在作用域。

```scss
// common.scss
.example {
    color: red;
}

// index.scss
#main {
    @import "example";
}

// 被编译成
#main .example {
    color: red;
}
```

> 注意：`@import`不能嵌套使用在控制指令或混入中（带有`@`符号的叫指令）。

### @media 媒体查询增强

`scss`中，`@media` 指令与 `css`中用法一样，只是增加了一点额外的功能，允许在`css`规则中嵌套。如果`@media` 嵌套在 `css`规则内，编译时，`@media` 将被编译到文件的最外层，包含嵌套的父选择器。这个让 `@media` 方便不少，不需要重复写选择器，也不会打乱 `css`的书写流程。

#### 使用

```scss
.sidebar {
  width: 300px;
  @media screen and (orientation: landscape) {
    width: 500px;
    .item {
      height: auto;
    }
  }
}

// 编译为
.sidebar {
    width: 300px;
}
@media screen and (orientation: landscape) {
  .sidebar {
    width: 500px;
  }
  .sidebar .item {
    height: auto;
  }
}
```

#### 嵌套

`@media`允许互相嵌套使用，编译时，`scss`自动添加 and。

```scss
@media screen {
  .sidebar {
    @media (orientation: landscape) {
      width: 500px;
    }
  }
}

// 编译为
@media screen and (orientation: landscape) {
  .sidebar {
    width: 500px;
  }
}
```

#### 使用插值

可以使用变量，函数，以及运算符代替条件的名称或者值。

```scss
$media: screen;
$feature: -webkit-min-device-pixel-ratio;
$value: 1.5;

@media #{$media} and ($feature: $value) {
  .sidebar {
    width: 500px;
  }
}

// 编译为
@media screen and (-webkit-min-device-pixel-ratio: 1.5) {
  .sidebar {
    width: 500px;
  }
}
```

### @mixin

混合指令（`Mixin`）用于定义可重复使用的样式。混合指令可以包含所有的`css`规则，绝大部分 `scss`规则，甚至可以通过参数功能引入变量，输出多样化的样式。

**注意：函数命名和变量命名规则一致。**

#### 使用

```scss
@mixin mixin-name() {
    /* css 声明 */
}
// 使用
@include mixin-name;
```

#### 标准形式

```scss
// 定义一个区块基本的样式
@mixin block {
    width: 96%;
    margin-left: 2%;
    border-radius: 8px;
    border: 1px #f6f6f6 solid;
}
// 使用混入 
.container {
    .block {
        @include block;
    }
}

// 编译为
.container .block {
    width: 96%;
    margin-left: 2%;
    border-radius: 8px;
    border: 1px #f6f6f6 solid;
}
```

#### 嵌入选择器

`@mixin`里面再嵌套一层。

```scss
@mixin warning-text {
    font-size: 12px;
    .warn-text {
        color: rgb(255, 253, 123);
        line-height: 180%;
    }
}

.container {
    @include warning-text;
}


// 编译为
.container {
    font-size: 12px;
}

.container .warn-text {
    color: #fffd7b;
    line-height: 180%;
}
```

#### 单参数

```scss
// 定义flex布局元素纵轴的排列方式
@mixin flex-align($aitem) {
    align-items: $aitem;
}

// 只有一个参数，直接传递参数
.container {
    @include flex-align(center);
}


// 编译为
.container {
    align-items: center;
}
```

#### 多参数

```scss
// 定义块元素内边距
@mixin block-padding($top, $right, $bottom, $left) {
    padding-top: $top;
    padding-right: $right;
    padding-bottom: $bottom;
    padding-left: $left;
}

// 按照参数顺序赋值
.container1 {
   @include block-padding(10px, 20px, 30px, 40px);
}

// 可指定参数赋值
.container2 {
   @include block-padding($left: 20px, $top: 10px, $bottom: 10px, $right: 30px);
}

// 可指定参数赋值，但是必须指定4个值，不能缺失
.container3 {
   @include block-padding($left: 10px, $top: 10px, $bottom: 0, $right: 0);
}


// 编译为
.container1 {
    padding-top: 10px;
    padding-right: 20px;
    padding-bottom: 30px;
    padding-left: 40px;
}

.container2 {
    padding-top: 10px;
    padding-right: 30px;
    padding-bottom: 10px;
    padding-left: 20px;
}

.container3 {
    padding-top: 10px;
    padding-right: 0;
    padding-bottom: 0;
    padding-left: 10px;
}
```

#### 指定默认值

```scss
// 定义块元素内边距，参数指定默认值
@mixin block-padding($top:0, $right:0, $bottom:0, $left:0) {
    padding-top: $top;
    padding-right: $right;
    padding-bottom: $bottom;
    padding-left: $left;
}

// 可指定参数赋值
.container {
    /** 不带参数 */
    @include block-padding;
    /** 按顺序指定参数值 */
    @include block-padding(10px,20px);
    /** 给指定参数指定值 */
    @include block-padding($left: 10px, $top: 20px)
}


// 编译为
.container {
    /** 不带参数 */
    padding-top: 0;
    padding-right: 0;
    padding-bottom: 0;
    padding-left: 0;

    /** 按顺序指定参数值 */
    padding-top: 10px;
    padding-right: 20px;
    padding-bottom: 0;
    padding-left: 0;

    /** 给指定参数指定值 */
    padding-top: 20px;
    padding-right: 0;
    padding-bottom: 0;
    padding-left: 10px;
}
```

#### 可变参数

使用`...`处理参数不固定的情况，类似于`js`中的函数的剩余参数。

```scss
@mixin linear-gradient($direction, $gradients...) {
    background-color: nth($gradients, 1);
    background-image: linear-gradient($direction, $gradients);
}

.table-data {
    @include linear-gradient(to right, #F00, orange, yellow);
}


// 编译为
.table-data {
    background-color: #F00;
    background-image: linear-gradient(to right, #F00, orange, yellow);
}
```

#### 总结

- `mixin`是可以重复使用的一组`css`声明，有助于减少重复代码，只需声明一次，就可在文件中引用；

- 混合指令可以包含所有的 `css`规则，绝大部分`scss`规则，可以传递参数，输出多样化的样式；

- 使用参数时建议加上默认值；

- `@import`导入局部模块化样式（类似功能、同一组件）；

- `@minix`定义的是可重复使用的样式；

### @function

`@function`用于封装复杂的操作，可以很容易地以一种可读的方式抽象出通用公式和行为，函数提供返回值，常用来做计算方面的工作。

注意：函数命名和变量命名规则一致。

#### 使用

```scss
@function square($base) {
    @return $base * $base * 1px;
}

.sidebar {
    float: left;
    margin-left: square(4);
}


// 编译为
.sidebar {
    float: left;
    margin-left: 16px;
}
```

#### 可选参数

```scss
// change-color和hue是内置方法
// hue 返回$color的颜色为0到360度之间的一个数字。
// change-color 用于设置颜色的属性
@function invert($color, $amount: 100%) {
    //@error hue($color); 调试 210deg
    $inverse: change-color($color, $hue: hue($color) + 180);
    @return mix($inverse, $color, $amount);
}

$primary-color: #036;
.header {
    background-color: invert($primary-color, 80%);
}


// 编译为
.header {
    background-color: #523314;
}
```

#### 指定参数

```scss
$primary-color: #036;
.banner {
    //scale-color Fluidly scales one or more properties of .$color
    background-color: $primary-color;
    color: scale-color($primary-color, $lightness: +40%);
}

// 编译为
.banner {
    background-color: #036;
    color: #0a85ff;
}
```

#### 可变参数

```scss
@function sum($numbers...) {
    $sum: 0;
    @each $number in $numbers {
        $sum: $sum + $number;
    }
    @return $sum;
}

$widths: 50px, 30px, 100px;
.micro {
    width: sum($widths...);
}

// 编译为
.micro {
    width: 180px;
}
```

#### 总结

- `@function`和`@mixin`参数的使用方式没啥区别；

- `@function`用来计算，`@mixin`用来封装样式，`@import`用来抽离他们为一个模块。

### @return

`@return`只允许在`@function`内使用，和`js`一样，遇到`return`就会返回。

```scss
@function red() {
    $is: true;
    @if $is {
        @return 'is';
    }
    @return red;
}
.con{
    color: red();
}

// 编译为
.con {
    color: "is";
}
```

### @extend继承

#### 使用

```scss
.button {
    display: inline-block;
    margin-bottom: 0;
    font-weight: normal;
    text-align: center;
    white-space: nowrap;
    vertical-align: middle;
    cursor: pointer;
    background-image: none;
    border: 1px solid transparent;
    padding: 6px 12px;
    font-size: 14px;
    line-height: 1.42857143;
    border-radius: 4px;
    user-select: none;
}

.btn-default {
    @extend .button;
    color: #333;
    background-color: #fff;
    border-color: #ccc;
}

.btn-danger {
    @extend .button;
    color: #fff;
    background-color: #d9534f;
    border-color: #d43f3a;
}


// 编译为
.button, .btn-danger, .btn-default {
    display: inline-block;
    margin-bottom: 0;
    font-weight: normal;
    text-align: center;
    white-space: nowrap;
    vertical-align: middle;
    cursor: pointer;
    background-image: none;
    border: 1px solid transparent;
    padding: 6px 12px;
    font-size: 14px;
    line-height: 1.42857143;
    border-radius: 4px;
    user-select: none;
}

.btn-default {
    color: #333;
    background-color: #fff;
    border-color: #ccc;
}

.btn-danger {
    color: #fff;
    background-color: #d9534f;
    border-color: #d43f3a;
}
```

#### 可以使用多个@extend

```scss
.alert {
    padding: 15px;
    margin-bottom: 20px;
    border: 1px solid transparent;
    border-radius: 4px;
    font-size: 12px;
}

.important {
    font-weight: bold;
    font-size: 14px;
}
.alert-danger {
    @extend .alert;
    @extend .important;
    color: #a94442;
    background-color: #f2dede;
    border-color: #ebccd1;
}


// 编译为
.alert, .alert-danger {
    padding: 15px;
    margin-bottom: 20px;
    border: 1px solid transparent;
    border-radius: 4px;
    font-size: 12px;
}

.important, .alert-danger {
    font-weight: bold;
    font-size: 14px;
}

.alert-danger {
    color: #a94442;
    background-color: #f2dede;
    border-color: #ebccd1;
}
```

#### 多层继承

`@extend`可以多层继承，列如：`.alert-danger`继承自`.important`，`.important`又继承自`.alert`。

```scss
.alert {
    padding: 15px;
    margin-bottom: 20px;
    border: 1px solid transparent;
    border-radius: 4px;
    font-size: 12px;
}

.important {
    @extend .alert;
    font-weight: bold;
    font-size: 14px;
}
.alert-danger {
    @extend .important;
    color: #a94442;
    background-color: #f2dede;
    border-color: #ebccd1;
}


// 编译为
.alert, .important, .alert-danger {
    padding: 15px;
    margin-bottom: 20px;
    border: 1px solid transparent;
    border-radius: 4px;
    font-size: 12px;
}

.important, .alert-danger {
    font-weight: bold;
    font-size: 14px;
}

.alert-danger {
    color: #a94442;
    background-color: #f2dede;
    border-color: #ebccd1;
}
```

#### 占位符选择器

占位符选择器`%`，与常用的`id` 与 `class`选择器写法相似，只是 `#` 或 `.` 替换成了`%`，占位符选择器必须通过 `@extend` 指令调用。

效果和上面的类选择器一样，但是，他有个有优点，占位符选择器%所属的样式未使用时，不会被编译到css文件中，算是一个小优化吧。

```scss
.button %base {
    display: inline-block;
    margin-bottom: 0;
    font-weight: normal;
    text-align: center;
    white-space: nowrap;
    vertical-align: middle;
    cursor: pointer;
    background-image: none;
    border: 1px solid transparent;
    padding: 6px 12px;
    font-size: 14px;
    line-height: 1.42857143;
    border-radius: 4px;
    user-select: none;
}

.btn-default {
    @extend %base;
    color: #333;
    background-color: #fff;
    border-color: #ccc;
}

.btn-danger {
    @extend %base;
    color: #fff;
    background-color: #d9534f;
    border-color: #d43f3a;
}


// 编译为
.button .btn-danger, .button .btn-default {
    display: inline-block;
    margin-bottom: 0;
    font-weight: normal;
    text-align: center;
    white-space: nowrap;
    vertical-align: middle;
    cursor: pointer;
    background-image: none;
    border: 1px solid transparent;
    padding: 6px 12px;
    font-size: 14px;
    line-height: 1.42857143;
    border-radius: 4px;
    user-select: none;
}

.btn-default {
    color: #333;
    background-color: #fff;
    border-color: #ccc;
}

.btn-danger {
    color: #fff;
    background-color: #d9534f;
    border-color: #d43f3a;
}
```

### @use

- [ ] 待补充

### @forward

`@forward`语句可以引入另一个模块的所有变量、`mixins`和函数，将它们直接作为当前模块的`API`暴露出去，不会真的在当前模块增加代码。不同于 `@use`， `@forward`不能给变量添加命名空间。

#### 用法

注意，此时生成的`bootstrap.css`文件中，是不包含`functions`、`variables`、`mixins`代码的，也不能直接在`bootstrap.scss`文件中使用这些模块。而是需要在另一个文件中 `@import 'bootstrap'`或者 `@use bootstrap`模块，再去使用这些方法。`bootstrap.scss`文件类似于一个传输中转站，把上下游的成员变量无缝连接起来。

```scss
/* bootstrap.scss */
@forward "functions";
@forward "variables";
@forward "mixins";
```

##### 例子

```scss
// a.scss
@mixin rounded {
    border-radius: 100px;
}
footer {
    height: 1px;
}


// b.scss
$radius: 3px;


// c.scss
@forward "a";
@forward "b";


// index.scss
@import "c.scss";
.button {
    @include rounded;
    padding: $radius;
}


// 编译为
footer {
    height: 1px;
}
.button {
    border-radius: 100px;
    padding: 3px;
}
```

#### show/ hide

通过控制 `show`和 `hide`，可以决定模块中的哪些成员对引入后的模板可见。对隐藏的变量，在下游文件中不可以使用，相当于模块私有成员。

```scss
// c.scss
@forward "a" show rounded;
@forward "b" hide $radius;

// index.scss
@import "c.scss";

.button {
    @include rounded;
    padding: $radius;
}
// Error: Undefined variable. padding: $radius;
```

#### 使用as *号为子模块添加前缀

大多数情况下，一个样式库会存在一个入口文件`index.scss`，然后在`index.scss`中引入其他的子文件。这种结构类似于一个多合一模块。那么，如果要在某一文件中 `@forward`多个子模块，就可以使用`as <prefix>-*`语句，为子模块下的成员自动带上前缀以区分。

```scss
// c.scss
@forward "a" as mixin-*;
@forward "b" as var-*;

// index.scss
@import "c.scss";
.button {
    @include mixin-rounded;
    padding: $var-radius;
}
```

# less

## 变量前缀

```less
// 定义
@prefixCls: 'pre';

// 使用
.@{prefixCls} {
  &-text {
    // 属性
  }
}
```

# css

## var()

方法的第一个参数是要替换的自定义属性的名称。函数的可选第二个参数用作回退值。如果第一个参数引用的自定义属性无效，则该函数将使用第二个值。

```css
/*在 :root 上定义，然后使用它*/
:root {
  --main-bg-color: pink;
}

body {
  background-color: var(--main-bg-color);
}


/*当第一个值未定义，回退值生效*/
/* 在父元素样式中定义一个值 */
.component {
  --text-color: #080; /* header-color 并没有被设定 */
}

/* 在 component 的样式中使用它： */
.component .text {
  /* 此处 color 正常取值 --text-color */
  color: var(--text-color, black); 
}

.component .header {
  /* 此处 color 被回退到 blue */
  color: var(--header-color, blue);
}
```

# Css Module

https://juejin.cn/post/6844903665485119496

## webpack 配置

CSS Modules默认是以[hash:base64]来进行类名转换的（全是hash值），可辨识度不高，因此我们需要自定义，开启自定义，可以使用一个配置参数`localIdentName`，具体配置如下：

```js
{ 
  loader: "css-loader",
  options: {
      modules: true,
      localIdentName: '[path][name]__[local]--[hash:base64:5]'
  }
}

// 效果(生成类名)：src-styles-index__header--3Ukt9
```
