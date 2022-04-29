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

## 属性

### clip-path

 [CSS](https://developer.mozilla.org/zh-CN/docs/Web/CSS) 属性使用裁剪方式创建元素的可显示区域。区域内的部分显示，区域外的隐藏。

[clip-path](https://developer.mozilla.org/zh-CN/docs/Web/CSS/clip-path)

[基本语法](https://segmentfault.com/a/1190000010936207)

# Css Module

[CSS Modules 入门教程 - 掘金](https://juejin.cn/post/6844903665485119496)

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
