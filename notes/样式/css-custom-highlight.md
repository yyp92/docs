# CSS Custom Highlight

介绍一个比较前沿但是非常有用的新特性：一个浏览器原生支持的 `CSS` 文本高亮功能，官方名称叫做 [CSS Custom Highlight API](https://developer.mozilla.org/en-US/docs/Web/API/CSS_Custom_Highlight_API)，有了它，**可以在不改变 `dom` 结构的情况下自定义任意文本的样式**，例如:

![](../../\images\css-custom-highlight-1.png)

再例如搜索词高亮:

![](../../\images\css-custom-highlight-2.jpg)

还可以轻易实现代码高亮:

![](../../\images\css-custom-highlight-3.jpg)

现在在 `Chrome 105` 中已经正式支持了（无需开启实验特性）。

## 伪元素 ::highlight()

要自定义任意文本样式需要 `CSS` 和 `JS` 的共同作用。

首先来看 `CSS` 部分，一个新的伪元素，非常简单。

```css
::highlight(custom-highlight-name) {
    color: red;
}
```

和`::selection`这类伪元素比较类似，仅支持部分文本相关样式，如下

- 文本颜色 `color`

- 背景颜色 `background-color`

- 文本修饰 `text-decoration`

- 文本阴影 `text-shadow`

- 文本描边 `-webkit-text-stroke`

- 文本填充 `-webkit-text-fill-color`

> **注意，注意，注意**不支持`background-image`，也就是渐变之类的也不支持

但是，仅仅知道这个伪类是没用的，她还需要一个`参数`，也就是上面的`custom-highlight-name`，表示高亮的名称，那这个是怎么来的呢？或者换句话说，如何去**标识页面中需要自定义样式的那部分文本**呢？

这就需要借助下面的内容了，看看如何生成这个“参数”。

## CSS Custom Highlight API

在介绍之前，建议先仔细阅读这篇文章：[**web 中的“光标”和“选区”**](http://mp.weixin.qq.com/s?__biz=MzU4MzUzODc3Nw==&mid=2247484857&idx=1&sn=c1178ac741be5e3e9131950eb9d28fb7&chksm=fda6c48fcad14d99cda4d3108612a3d101352e3c7f9a58a2bfd82a6b9828a7587af624f5bb65&scene=21#wechat_redirect)

大部分操作其实和这个原理是相同的，只是把拿到的选区做了进一步处理，具体分以下几步

### 创建选区（重点）

首先，通过[Range](https://developer.mozilla.org/en-US/docs/Web/API/Range)对象创建文本选择范围，**就像用鼠标滑过选区一样**，这也是最复杂的一部分，例如:

```js
const parentNode = document.getElementById("foo");

const range1 = new Range();
range1.setStart(parentNode, 10);
range1.setEnd(parentNode, 20);

const range2 = new Range();
range2.setStart(parentNode, 40);
range2.setEnd(parentNode, 60);
```

这样可以得到**选区**对象`range1`、`range2`。

### 创建高亮

然后，将创建的选区**高亮实例化**，需要用到[Highlight](https://developer.mozilla.org/en-US/docs/Web/API/Highlight)对象。

```js
const highlight = new Highlight(range1, range2, ...);
```

当然也可以根据需求创建多个。

```js
const highlight1 = new Highlight(user1Range1, user1Range2);
const highlight2 = new Highlight(user2Range1, user2Range2, user2Range3);
```

这样可以得到**高亮**对象`highlight1`、`highlight2`。

### 注册高亮

接着，需要将实例化的高亮对象通过[CSS.Highlight](https://developer.mozilla.org/en-US/docs/Web/API/HighlightRegistry)注册到页面

有点类似于`Map`对象的操作

```js
CSS.highlights.set("highlight1", highlight1);
CSS.highlights.set("highlight2", highlight2);
```

目前兼容性比较差，所以需要额外判断一下

```js
if (CSS.highlights) {
  //...支持CSS.highlights
}
```

注意看，上面注册的`key`名，`highlight1`就是上一节提到的高亮名称，也就是 `CSS` 中需要的“参数”。

## 自定义样式

最后，将定义的高亮名称结合`::highlight`，这样就可以自定义选中样式了。

```css
::highlight(highlight1) {
    background-color: yellow;
    color: black;
}
```

以上就是全部过程了，稍显复杂，但是还是比较好理解的，关键是第一步创建选区的过程，最为复杂，再次推荐仔细阅读这篇文章：[**web 中的“光标”和“选区”**](http://mp.weixin.qq.com/s?__biz=MzU4MzUzODc3Nw==&mid=2247484857&idx=1&sn=c1178ac741be5e3e9131950eb9d28fb7&chksm=fda6c48fcad14d99cda4d3108612a3d101352e3c7f9a58a2bfd82a6b9828a7587af624f5bb65&scene=21#wechat_redirect)，下面用一张图总结一下。

![](../../\images\css-custom-highlight-4.jpg)

原理就是这样。

## 彩虹文本

现在来实现文章开头图示效果，彩虹文本效果。总共7种颜色，文字依次变色，不断循环，而且仅有一个标签。

```html
<p id="rainbow-text">CSS Custom Highlight API</p>
```

这里总共有`7`种颜色，所以需要创建`7`个高亮区域，可以先定义高亮 `CSS`，如下：

```css
::highlight(rainbow-color-1) { color: #ad26ad;  text-decoration: underline; }
::highlight(rainbow-color-2) { color: #5d0a99;  text-decoration: underline; }
::highlight(rainbow-color-3) { color: #0000ff;  text-decoration: underline; }
::highlight(rainbow-color-4) { color: #07c607;  text-decoration: underline; }
::highlight(rainbow-color-5) { color: #b3b308;  text-decoration: underline; }
::highlight(rainbow-color-6) { color: #ffa500;  text-decoration: underline; }
::highlight(rainbow-color-7) { color: #ff0000;  text-decoration: underline; }
```

现在肯定不会有什么变化，因为还没创建选区:

![](../../\images\css-custom-highlight-5.jpg)

先创建一个高亮区域试试，比如第一个文字:

```js
const textNode = document.getElementById("rainbow-text").firstChild; 

if (CSS.highlights) {
  const range = new Range();
  range.setStart(textNode, 0); // 选区起点
  range.setEnd(textNode, 1); // 选区终点
  const Highlight = new Highlight(range);
  CSS.highlights.set(`rainbow-color-1`, Highlight);
}
```

效果如下:

![](../../\images\css-custom-highlight-6.png)

下面通过循环，创建`7`个高亮区域：

```js
const textNode = document.getElementById("rainbow-text").firstChild;

if (CSS.highlights) {
  const highlights = []; 

  for (let i = 0; i < 7; i++) {
    // 给每个颜色实例化一个Highlight对象
    const colorHighlight = new Highlight();
    highlights.push(colorHighlight);

    // 注册高亮
    CSS.highlights.set(`rainbow-color-${i + 1}`, colorHighlight);
  }

  // 遍历文本节点
  for (let i = 0; i < textNode.textContent.length; i++) {
    // 给每个字符创建一个选区
    const range = new Range();
    range.setStart(textNode, i);
    range.setEnd(textNode, i + 1);

    // 添加到高亮
    highlights[i % 7].add(range);
  }
}
```

这样就在不改变`dom`的情况下实现了彩虹文字效果:

![](../../\images\css-custom-highlight-7.png)

完整代码可以查看以下任意链接：（注意需要Chrome 105+）

- [CSS Custom Highlight API (juejin.cn)](https://code.juejin.cn/pen/7198496899391815736)

- [CSS Custom Highlight API (codepen.io)](https://codepen.io/xboxyan/pen/qByzGYr)

- [CSS Custom Highlight API (runjs.work)](https://runjs.work/projects/450431c8f0064298)

## 文本搜索高亮

大家都知道浏览器的搜索功能，`ctrl+f`就可以快速对整个网页就行查找，查找到的关键词会添加黄色背景的高亮，如下:

![](../../\images\css-custom-highlight-8.jpg)

以前一直很疑惑这个颜色是怎么添加的，毕竟没有任何包裹标签。现在有了`CSS Custom Highlight API` ，完全可以手动实现一个和原生浏览器一模一样的搜索高亮功能。

> 到目前为止，还无法自定义原生搜索高亮的黄色背景，以后可能会开放

假设`HTML`结构是这样的，一个搜索框和一堆文本:

```html
<label>搜索 <input id="query" type="text"></label>
<article>
  <p>
    阅文旗下囊括 QQ 阅读、起点中文网、新丽传媒等业界知名品牌，汇聚了强大的创作者阵营、丰富的作品储备，覆盖 200 多种内容品类，触达数亿用户，已成功输出《庆余年》《赘婿》《鬼吹灯》《全职高手》《斗罗大陆》《琅琊榜》等大量优秀网文 IP，改编为动漫、影视、游戏等多业态产品。
  </p>
  <p>
    《盗墓笔记》最初连载于起点中文网，是南派三叔成名代表作。2015年网剧开播首日点击破亿，开启了盗墓文学 IP 年。电影于2016年上映，由井柏然、鹿晗、马思纯等主演，累计票房10亿元。
  </p>
  <p>
    庆余年》是阅文集团白金作家猫腻的作品，自2007年在起点中文网连载，持续保持历史类收藏榜前五位。改编剧集成为2019年现象级作品，播出期间登上微博热搜百余次，腾讯视频、爱奇艺双平台总播放量突破160亿次，并荣获第26届白玉兰奖最佳编剧（改编）、最佳男配角两项大奖。
  </p>
  <p>《鬼吹灯》是天下霸唱创作的经典悬疑盗墓小说，连载于起点中文网。先后进行过漫画、游戏、电影、网络电视剧的改编，均取得不俗的成绩，是当之无愧的超级IP。</p>
</article>
```

简单美化一下后效果如下:

![](../../\images\css-custom-highlight-9.jpg)

然后就是监听输入框，遍历文本节点（推荐使用原生的`treeWalker`，当然普通的递归也可以），根据搜索词创建选区，详细代码如下:

```js
const query = document.getElementById("query");
const article = document.querySelector("article");

// 创建 createTreeWalker 迭代器，用于遍历文本节点，保存到一个数组
const treeWalker = document.createTreeWalker(article, NodeFilter.SHOW_TEXT);
const allTextNodes = [];
let currentNode = treeWalker.nextNode();
while (currentNode) {
  allTextNodes.push(currentNode);
  currentNode = treeWalker.nextNode();
}

// 监听inpu事件
query.addEventListener("input", () => {
  // 判断一下是否支持 CSS.highlights
  if (!CSS.highlights) {
    article.textContent = "CSS Custom Highlight API not supported.";
    return;
  }

  // 清除上个高亮
  CSS.highlights.clear();

  // 为空判断
  const str = query.value.trim().toLowerCase();
  if (!str) {
    return;
  }

  // 查找所有文本节点是否包含搜索词
  const ranges = allTextNodes
    .map((el) => {
      return { el, text: el.textContent.toLowerCase() };
    })
    .map(({ text, el }) => {
      const indices = [];
      let startPos = 0;
      while (startPos < text.length) {
        const index = text.indexOf(str, startPos);
        if (index === -1) break;
        indices.push(index);
        startPos = index + str.length;
      }

      // 根据搜索词的位置创建选区
      return indices.map((index) => {
        const range = new Range();
        range.setStart(el, index);
        range.setEnd(el, index + str.length);
        return range;
      });
    });

  // 创建高亮对象
  const searchResultsHighlight = new Highlight(...ranges.flat());

  // 注册高亮
  CSS.highlights.set("search-results", searchResultsHighlight);
});
```

最后，通过`CSS`设置高亮的颜色:

```css
::highlight(search-results) {
    background-color: #f06;
    color: white;
}
```

实时搜索效果如下

![](../../\images\css-custom-highlight-10.gif)

完整代码可以查看以下任意链接：（注意需要Chrome 105+）

- [CSS Highlight search (juejin.cn)](https://code.juejin.cn/pen/7198488612801871929)

- [CSS Highlight search (codepen.io)](https://codepen.io/xboxyan/pen/eYjwoqo)

- [CSS Highlight search (runjs.work)](https://runjs.work/projects/a661feba3dad44c9)

还可以将高亮效果改成波浪线:

```css
::highlight(search-results) {
    text-decoration: underline wavy #f06;
}
```

效果如下，是不是也可用作**错别字标识**呢？

![](../../\images\css-custom-highlight-11.jpg)

除了避免`dom`操作带来的便利外，**性能也能得到极大的提升**，毕竟创建、移除`dom`也是性能大户，下面是一个测试 demo，搬运自

> [https://ffiori.github.io/highlight-api-demos/demo-performance.html](https://ffiori.github.io/highlight-api-demos/demo-performance.html)

测试代码可以查看以下任意链接：

- []Highlight performance demo (juejin.cn)](https://code.juejin.cn/pen/7198487962978353208)

- [Highlight performance demo (codepen.io)](https://codepen.io/xboxyan/pen/YzjoMmp)

- [Highlight performance demo (runjs.work)](https://runjs.work/projects/e5fe09f70d324d99)

测试效果如下:

![](../../\images\css-custom-highlight-12.jpg)

在`10000`个节点的情况下，两者相差`100`倍的差距！而且数量越大，性能差距越明显，甚至直接导致浏览器卡死！

## 代码高亮编辑器

最后再来看一个非常实用的例子，可以轻易实现一个代码高亮的编辑器。

假设 `HTML`结构是这样的，很简单，就一个纯文本的标签:

```html
<pre class="editor" id="code">ul{
  min-height: 0;
}
.sub {
  display: grid;
  grid-template-rows: 0fr;
  transition: 0.3s;
  overflow: hidden;
}
:checked ~ .sub {
  grid-template-rows: 1fr;
}
.txt{
  animation: color .001s .5 linear forwards;
}
@keyframes color {
  from {
    color: var(--c1)
  }
  to{
    color: var(--c2)
  }
}</pre>
```

简单修饰一下，设置为可编辑元素

```css
.editor{
    white-space: pre-wrap;
    -webkit-user-modify: read-write-plaintext-only; /* 读写纯文本 */
}
```

效果如下:

![](../../\images\css-custom-highlight-13.jpg)

那么，如何让这些代码高亮呢？

这就需要对内容进行关键词分析提取了，我们可以用现有的代码高亮库，比如[highlight.js](https://highlightjs.org/)。

```js
 hljs.highlight(pre.textContent, {
   language: 'css'
 })._emitter.rootNode.children
```

通过这个方法可以获取到`CSS`语言的关键词以及类型，如下:

![](../../\images\css-custom-highlight-14.jpg)

简单解释一下，这是一个数组，如果是纯文本，表示普通的字符，如果是对象，表示是关键词，例如第一个，`children`里面的`ul`就是关键词，类型是`selector-tag`，也就是**选择器**，除此之外，还有`attribute`、`number`、`selector-class`等各种类型。有了这些关键词，我们就可以把这些文本单独选取出来，然后高亮成不同的颜色。

接下来，就需要对代码内容进行遍历了，方法也是类似的，如下:

```js
const nodes = pre.firstChild
const text = nodes.textContent
const highlightMap = {}
let startPos = 0; 

words.filter(el => el.scope).forEach(el => {
  const str = el.children[0]
  const scope = el.scope
  const index = text.indexOf(str, startPos);

  if (index < 0) {
    return
  } 

  const item = {
    start: index,
    scope: scope,
    end: index + str.length,
    str: str
  } 

  if (highlightMap[scope]){
    highlightMap[scope].push(item)
  } else {
    highlightMap[scope] = [item]
  }
  startPos = index + str.length;
}) 

Object.entries(highlightMap).forEach(function([k,v]){
  const ranges = v.map(({start, end}) => {
    const range = new Range();
    range.setStart(nodes, start);
    range.setEnd(nodes, end);
    return range;
  }); 

  const highlight = new Highlight(...ranges.flat());
  CSS.highlights.set(k, highlight);
})

highlights(code)
code.addEventListener('input', function(){
  highlights(this)
})
```

最后，根据不同的类型，定义不同的颜色就行了，如下:

```css
::highlight(built_in) {
    color: #c18401;
}
::highlight(comment) {
  color: #a0a1a7;
  font-style: italic;
}
::highlight(number),
::highlight(selector-class){
    color: #986801;
}
::highlight(attr) {
    color: #986801;
}
::highlight(string) {
    color: #50a14f;
}
::highlight(selector-pseudo) {
    color: #986801;
}
::highlight(attribute) {
    color: #50a14f;
}
::highlight(keyword) {
    color: #a626a4;
}
```

这样就得到了一个支持代码高亮的简易编辑器了。

![](../../\images\css-custom-highlight-15.jpg)

相比传统的编辑器而言，这个属于纯文本编辑，非常轻量，在高亮的同时也不会影响光标，因为不会生成新的`dom`，性能也是超级棒👍🏻。

![](../../\images\css-custom-highlight-16.jpg)

完整代码可以查看以下任意链接：

- [CSS highlight editor (juejin.cn)](https://code.juejin.cn/pen/7198487629262749756)

- [CSS highlight editor (codepen.io)](https://codepen.io/xboxyan/pen/RwBzOmK)

- [CSS highlight editor (runjs.work)](https://runjs.work/projects/9ff7ab8f12844ce1)

## 最后总结一下

以上就是关于`CSS Custom Highlight API`的使用方式以及应用示例了，下面再来回顾一下使用步骤：

1. 创建选区，`new Range`

2. 创建高亮，`new Highlight`

3. 注册高亮，`CSS.highlights.set`

4. 自定义样式，`::highlight()`

相比传统使用标签的方式而已，有很多优点

1. 使用场景更广泛，很多情况下不能修改`dom`或者成本极大

2. 性能更高，避免了操作`dom`带来的额外开销，在`dom`较多情况下性能差异至少`100`倍

3. 几乎没有副作用，能有效减少`dom`变化引起的其他影响，比如光标选区的处理

其实归根结底，都是`dom`变化带来的，而`Highlight API`恰好能有效避开这个问题。当然也有一些缺陷，由于仅仅能改变文本相关样式，所以也存在一些局限性，这个就需要权衡了，目前兼容性也还不足，仅适用于内部项目。
