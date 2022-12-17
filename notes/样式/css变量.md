# CSS变量

## 基本使用

CSS变量即**自定义属性**，它包含的值可以在整个文档中重复使用。下面来看看CSS变量是如何定义和使用的。

首先，我们要声明一个自定义属性，属性名以两个减号（--）开始，属性值可以是任何有效的CSS值，并且这个属性和其他属性一样可以写在规则集之内：

```css
:root {
  --main-bg-color: #C92E33;
}
```

通常，最佳实践是将CSS变量定义在根伪类`:root`下，这样就可以在HTML文档的任何地方访问到它了。在使用CSS变量时，使用 `var()` 函数包裹所需变量的变量名即可：

```css
div {
  background-color: var(--main-bg-color)
}
```

除了变量名之外，`var()` 还有第二个参数——**备用值**。在发现变量值不可访问的情况下，将使用备用值来代替它：

```css
div {
  background-color: var(--main-bg-color, red);
}
```

对于CSS变量的备用值，在替换的过程中，会有四种可能：

- 浏览器不支持 CSS 变量，带有`var()`的代码行将会被忽略，将使用浏览器的默认值；

- 浏览器支持变量，并且该变量设置为正确值，则直接使用该变量；

- 浏览器支持变量，并且变量未设置为任何值，则直接使用备用值；

- 浏览器支持变量，并且该变量设置为无效值，则使用浏览器的默认值。

我们甚至可以在**媒体查询**中重新设置变量，这些重新设置的值可以在任何地方使用，这是预处理器变量无法实现的：

```css
<div></div>
<div></div>
<div></div>
<div></div>


:root {
  --width: 25%;
  --margin: 20px;
}

@media (max-width: 400px) {
  :root {
    --width: 50%;
    --margin: 10px;
  }
}

body {
  display: flex;
  flex-wrap: wrap;
}

div {
  flex: 1;
  height: 220px;
  flex-basis: calc(var(--width) - (var(--margin) * 3));
  background: #ccc;
  margin: var(--margin);
}
```

CSS 变量使重用和自定义 CSS 代码成为了可能，因为它们使封装成为可能。假如我们有一个按钮，它的 `class` 为 flat，代码如下：

```css
button.flat {
 border: 1px solid black;
 background: transparent;
 color: black;
}

button.flat:hover {
 background: black;
 color: white;
}
```

假如我们想要不同的按钮有不同的操作，比如红色的按钮用于危险操作，那么我们就可以使用 `danger` 类来覆盖对应的样式声明：

```css
button.flat.danger {
 border-color: red;
 color: red;
}

button.flat.danger:hover {
 background: red;
 color: white;
}
```

如果有很多种颜色和操作，那就需要多次定义样式来覆盖之前的样式。我们可以使用变量来替换颜色：

```css
button {
 --color-initial: black;
 border: 1px solid var(--color, var(--color-initial));
 background: transparent;
 color: var(--color, var(--color-initial));
}

button:hover {
 background: var(--color, var(--color-initial));
 color: white;
}
```

现在想要主题化这个按钮，只需要定义`--color`变量即可：

```css
button.flat.danger {
 --color: red;
}
```

这样就相当于将CSS代码进行了封装，代码简洁了很多~

## 作用域

我们可以在全局范围内声明 CSS 变量，这些变量可以在整个应用程序中使用。也可以在局部范围内设置 CSS 变量，它只在特定的选择器中可用。

#### （1）CSS 全局变量

要在全局范围内声明变量，就要将变量定义在 :`root` 选择器中：

```css
:root {
  --primary-color: #000;
}

h1 {
  color: var(--primary-color);
}
```

#### （2）CSS 局部变量

要在局部范围声明变量，只需要在选择器中定义变量即可，这样声明的变量只能在该选择器中使用，如果尝试在其他地方使用，它不会有任何效果：

```css
h2 {
  --h2-color: #999;
  color: var(--h2-color);
}

h3 {
  color: var(--h2-color);  /* 不生效 */
}
```

#### （3）优先级和继承

现在我们知道了，如果在根目录中定义变量，它将是全局变量，如果在选择器中定义变量，它将是局部变量。那如果在两个地方都声明了同一变量，谁的优先级更高呢？

实际上，CSS变量和JavaScript变量类似，CSS 变量中的**局部作用域优先于全局作用域**。

```css
:root {
  --color: red;
}

h2 {
  --color: orange;
  color: var(--color);
}
```

这里`<h2>`标签的内容将是橙色的，因为局部变量的优先级比全局变量的优先级更高。

在CSS中是存在继承的概念的，对于CSS变量也是如此，如果没有为元素找到变量，它会从其父元素继承变量值。下面的`<h2>`标签的内容也将是橙色的，因为`<h2>`在其选择器中没有找到 `--color` 变量，所以就会在其父级逐级往上查找这个变量：

```css
:root {
  --color: red;
}

body {
  --color: orange;
}

h2 {
  color: var(--color);
}
```

## 注意事项

#### （1）CSS变量区分大小写

在定义CSS变量时需要注意，CSS变量的变量名（即属性名）对大小写是敏感的，`--my-color` 和 `--My-color` 会被认为是两个不同的CSS变量。

#### （2）避免循环依赖

在定义CSS变量时不能出现循环依赖关系，这会导致页面无法加载：

```css
--variable_name_1 : var(variable_name_2, fallback);
--variable_name_2 : var(variable_name_1, fallback);
```

#### （3）CSS 变量不能是属性名

不能使用 CSS 中的现有属性作为 CSS 的变量名。例如，我们不能使用名称“font-size”来初始化变量。

## 在 JavaScript 中使用

使用 CSS 变量的一大好处就是可以在 JavaScript 中访问它。

假如我们通过内联样式来设置 CSS 变量：

```html
<p style="--color: red"></p>
```

可以通过以下代码来获取变量值：

```js
// 获取 <p> 元素
const element = document.querySelector('p');

// 检索CSS变量 --color 
element.style.getPropertyValue('--color'); // 'red'
```

如果在CSS文件中设置CSS变量：

```css
p {
  --color: red;
}
```

就需要通过以下形式来获取变量值：

```js
// 获取 <p> 元素
const element = document.querySelector('p');

// 获取CSS变量 --color 
getComputedStyle(element).getPropertyValue('--color'); // 'red'
```

当然也可以使用这种形式来获取变量值：

```js
<p style="--color: red"></p>

// 获取 <p> 元素
const element = document.querySelector('p');

// 获取CSS变量 --color
getComputedStyle(element).getPropertyValue('--color'); // 'red'
```

可以通过以下形式使用JavaScript来设置CSS变量：

```js
// 获取 <p> 元素
const element = document.querySelector('p');

// 将 --color 变量设置为 blue 
element.style.setProperty('--color', 'blue');
```

## SASS 变量 vs CSS 变量

下面来看看 CSS 变量出现之前都是如何做的。在CSS变量出现之前开发人员会使用 SASS 变量，它们的目的是相似的，但是不够流畅和灵活。

SASS 变量在定义时需在变量名前加上“$”，在使用变量时直接使用变量名即可：

```scss
$main-bg-color: #C92E33;

background-color: $main-bg-color;
```

SASS 的问题就在于它是一个预处理器，所以，任何在 SASS 中声明的变量都需要经过编译才能执行。上面的代码就会编译成这样：

```scss
background-color: #C92E33;
```

这样才是有效的CSS，代码经过编译后，变量也就消失了。因此，我们不能在 CSS 运行时更改变量值。

预处理器中的变量范围归结为嵌套的大括号块。然而，因为 CSS 变量是属性，所以它们的作用域是基于 DOM 的。这意味着 CSS 变量是按元素解析的，而不是按作用域解析的，来看下面的例子：

```css
body {
  --shadow-color: red;
}

button {
  box-shadow: 1px 1px 1px var(--shadow-color);
}

button:hover {
  --shadow-color: blue;
}
```

当我们将鼠标悬停在按钮上时，按钮的红色变为了蓝色。那使用预处理器语言 SASS 会是什么结果呢，来看代码：

```scss
body {
 $shadow-color: gray;
}

button {
 box-shadow: 1px 1px 1px $shadow-color;
}

button:hover {
 $shadow-color: blue;
}
```

这样就会出现语法错误，第六行使用了未定义的变量。因为SASS不知道 `<button>` 在 `<body>` 里面（因为它不是使用 CSS 在浏览器中的 HTML 上下文执行的），也不知道button:hover 是一个按钮，所以两个定义的变量都没有被识别到。

CSS 变量与预处理器变量最重要的区别就是**CSS变量是动态分配的**。它们在页面的整个生命周期中保持活动状态，当更新它们时，所有引用它们的地方都会更新。因为它们是属性，所以可以通过任何更新 CSS 属性的机制来更新它们：样式表、内联样式，甚至 JavaScript。

下面是 CSS 变量和 SASS 变量的主要区别：

![](../../\images\css-vs-sass-1.png)

## 浏览器兼容性

目前，CSS 变量已经得到了各大浏览器的支持：

![](../../\images\css-vs-sass-2.png)
