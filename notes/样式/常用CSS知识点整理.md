# 前端工作中常用 CSS 知识点整理

## 1. 文字溢出省略号

文字单行溢出:

```css
/*溢出隐藏*/
overflow: hidden; 
/*溢出用省略号显示*/
text-overflow: ellipsis;
/*规定段落中的文本不进行换行*/
white-space: nowrap;
```

多行文字溢出:

```css
/*溢出隐藏*/
overflow: hidden;
/*溢出用省略号显示*/
text-overflow: ellipsis;
/*作为弹性伸缩盒子模型显示*/
display: -webkit-box;
/*设置伸缩盒子的子元素排列方式：从上到下垂直排列*/
-webkit-box-orient: vertical;
/*显示的行数*/
-webkit-line-clamp: 3;
```

## 2. css变量

**CSS变量**又称**CSS自定义属性**，通过在`css`中自定义属性`--var`与函数`var()`组成，`var()`用于引用自定义属性。谈到为何会在`CSS`中使用变量，以下使用一个示例讲述。

```css
:root {
 --c-color: orange;
}
.title {
 background-color: var(--c-color);
}
```

## 3. 渐变

渐变分为`线性渐变`、`径向渐变`，这里直接记录的使用方式，也是为了使用的时候更加的直观，这里说下，在使用线性渐变的时候，使用`角度`以及`百分比`去控制渐变，会更加的灵活

使用方式:

```css
/*渐变(方向)*/
background: linear-gradient(to right, rgba(255, 255, 255, 0),#3FB6F7,rgba(255,255,255,0));

/*渐变(角度)*/
background: linear-gradient(88deg, #4DF7BF 0%, rgba(77, 247, 191, 0.26) 12%, rgba(77, 247, 191, 0) 100%);
```

![](../../\images\css-common-1.png)

边框渐变:

`border`有个`border-image`的属性，类似`background`也有个`background-image`一样,通过为其设置渐变颜色后，实现的渐变，后面的数字`4`为`x`方向偏移量

使用方式:

```css
.border-grident{
  margin-top: 20px;
  width: 200px;
  height: 200px;
  border: 4px solid;
  border-image: linear-gradient(to right, #8f41e9, #578aef) 4;
}
```

![](../../\images\css-common-2.png)

## 4. background-size：cover和contain以及100%

`contain`:**图片放大至满足背景区域的最小边即止**，当背景区域与背景图片的宽高比不一致的情况下，**背景区域可能会长边下有空白覆盖不全**。`cover`:**图片放大至能满足最大变时为止**，当背景区域与背景图片的宽高比不一致时，**背景图片会在短边下有裁切，显示不全**。

`百分比`:可以设置两个值

- 第一个设置宽度，第二个设置高度

- 如果只设置了一个值，那么第二个值默认会被设置为`auto`

示例:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    *{
      margin: 0;
      padding: 0;
    }
    .bg{
      width: 100%;
      height: 300px;
      background: url('./img/mtk.png');
      /* background-size: contain; */ 
      /* background-size: cover; */
      background-size: 100%;
      background-repeat: no-repeat;
    }
  </style>
</head>
<body>
  <div class="bg"></div>
</body>
</html>
```

结果依次为下图展示:

`contain`：

![](../../\images\css-common-3.png)

`cover`:

![](../../\images\css-common-4.png)

`百分比`（这里是100%）：

![](../../\images\css-common-4.png)

## 5 css伪类三角形

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>css-三角形</title>
  <style>
    .triangle {
        width: 0;
        height: 0;
        border: 100px solid;
        border-color: orangered skyblue gold yellowgreen;
    }
  </style>
</head>
<body>
  <div class="triangle">
  </div>
</body>
</html>
```

![](../../\images\css-common-5.png)

如果想要一个`下`的三角形，可以让`border`的`上边框`可见，其他边框颜色都设置为透明

```css
.down-triangle {
    width: 0;
    height: 0;
    border-top: 50px solid skyblue;
    border-right: 50px solid transparent;
    border-left: 50px solid transpa
    border-bottom: 50px solid transparent;
}
```

![](../../\images\css-common-6.png)

## 6 媒体查询

页面头部必须有`meta`关于`viewport`的声明

```html
<meta name="viewport" content="width=device-width" initial-scale="1." maximum-scale="1" user-scalable="no"/>
```

通常在做响应式布局的时候，以及大屏的时候很常用的，从而实现在不通`分辨率`下，实现不同的展示效果

```css
/* 超过1920分辨率后显示多列 */
@media screen and (min-width: 1920px) {
  .car_box.el-card {
    min-width: 450px !important;
    width: 450px !important;
  }
}
```
