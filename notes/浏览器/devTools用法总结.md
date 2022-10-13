# DevTools 用法总结

本文将会介绍浏览器`devtool`的一些常用工具方法，这些工具能够以可视化的方式展现当前网页情况，我们也能快速定位代码问题，如果我们能很好的利用起来，那就能写出高质量代码。

## Performance(重点)

> 强大的性能监控工具

### 工具栏

![](../../\imgs\devTools-1.jpg)

从左到右依次为：

- Record(Ctrl+E) 记录运行时性能

- Start profiling and reload page(Ctrl+Shift+E) 记录负载性能，会重新加载页面

- clear 清空面板

- Load profile... 上传

- Save profile... 下载，会生成个json文件，需要做比对可以再上传

- recordings 生成过的profile列表记录，注意：点clear就全没有了

- Screenshots 屏幕截图，启用会在录制时捕获每一帧的屏幕截图

- Memory 内存 下面也会有重点介绍

- Web Vitals 核心功能，会显示LCP，FCP，LongTasks

- Collect garbage 录制时强制垃圾收集

### 概览

![](../../\imgs\devTools-2.jpg)

在概览上可以拖动鼠标进行缩放，可以点击定位，可以鼠标滚轮放大缩小。

**CPU颜色划分：** *Loading*

| 颜色                  | 执行内容         |
| ------------------- | ------------ |
| 蓝色 ( *Loading* )    | 网络通信和HTML解析  |
| 黄色 ( *Scripting* )  | JavaScript执行 |
| 紫色( *Rendering* )   | 样式计算和布局(重排)  |
| 绿色 ( *Painting* )   | 样式更改(重绘)     |
| 灰色 ( *Other* )      | 其他事件         |
| 白色 ( *Idle* )       | 空闲时间         |
| 红色  ( *LongTasks* ) | 长任务出现        |

### 火焰图

> 点击火焰图任何区域，可以使用键盘的WASD进行缩放和左右位移

#### Frames-帧

![](../../\imgs\devTools-3.jpg)

- 白色：没有变化

- 绿色：按预期及时渲染

- 黄色：浏览器尽最大努力及时呈现至少一些视觉更新，比如滚动了但主线程没空

- 红色：掉帧，无法在合理的时间内渲染帧，比如：`scroll`，`resize`事件触发过于频繁,浏览器来不及处理导致在下一个事件被触发之前无法完成

#### Main-主线程

![](../../\imgs\devTools-4.jpg)

> `Ctrl + F`可以搜索`Task`名字，定位到具体的位置会标红

右上角有红色三角形表示该`Task`是一个长任务(执行超过50ms)，左边灰色区域执行时间50ms，右边红色区域执行时间就是39.83ms。长任务过多会导致页面出现卡顿。

从上倒下依次是调用堆栈，上面的事件导致下面的事件。

**Task 颜色划分**

`DevTools` 为脚本分配随机颜色，来自一个脚本的函数调用是浅绿色的，来自另一个脚本的调用可能是米色的。较深的黄色代表脚本活动，紫色代表渲染活动。

我们可以点击任意脚本活动查看

![](../../\imgs\devTools-5.jpg)

(anonymous)代表的是匿名函数的意思，点击旁边文件导航跳转过去，光标定位的地方就是函数调用的位置。

![](../../\imgs\devTools-6.jpg)

### Call Tree 调用树

![](../../\imgs\devTools-7.jpg)

调用树的顺序是从上到下，和`Buttom-Up`顺序相反，这里介绍一个就可以了

**SelfTime**指的当前函数执行时间，不包括子函数执行时间，百分比是当前时间除以所有SelfTime时间之和。

**TotalTime**指的SelfTime和子函数的`TotalTime`之和，百分比是当前时间除以首个函数的`TotalTime`。

**Activity**列中的顶级项目，例如`Event`、`Paint`和`Composite Layers`是根活动，嵌套表示调用堆栈。可以看到，实际计算出来的值还是会有一点误差。

### Event Log 事件日志

![](../../\imgs\devTools-8.jpg)

按记录期间发生的顺序查看活动，包括加载、脚本、渲染、绘制等，也提供了事件名和耗时时长搜索过滤。

**Start Time**表示该活动开始的时间点，相对于记录的开始时间。比如103.6ms，表示活动在录制开始后103.6毫秒开始。

## Network

### 重新发请求

与后端调试接口时，有时候会让你再请求一次，再请求一次...

![](../../\imgs\devTools-9.jpg)

右击，选择`Replay XHR`，则会重新发起请求。

### 阻断请求

可以阻断请求URL和当前域名

![](../../\imgs\devTools-10.jpg)

搜索`Show Network request blocking`查看阻断的列表

## Console

### 搭配`CSS`

![](../../\imgs\devTools-11.jpg)

### $0

![](../../\imgs\devTools-12.gif)

`$1`对上次的节点引用，`$2`对上上次节点的引用，一直到`$4`

### $_

![](../../\imgs\devTools-13.gif)

### $、$$

> 等同于`document.querySelector`和`document.querySelectorAll`

![](../../\imgs\devTools-14.jpg)

## Sources

### Snippets 片段

有时候我们在翻译网站的时候，会把代码也翻译成中文，那我们可以使用`Sources`的`Snippets`来内置代码，指定元素不进行翻译，也可以在片段中快捷键`Ctrl+Enter`执行

![](../../\imgs\devTools-15.jpg)

有时候我们想更快的执行这段代码，可以先重命名，然后通过快捷键`Ctrl+p`可以快速定位到片段的位置去执行

![](../../\imgs\devTools-16.gif)

当然，`Snippets`这里我只是举了一个例子，你可以实现你想要的一些东西

## JS分析器

> 打开开发者工具按`ctrl+shift+p`搜索`Show Javascript Profiler`选中然后回车

![](../../\imgs\devTools-17.jpg)

可以看到当前js代码执行时间，也可以通过左上角录制，测试当前按钮执行的函数是否有性能问题

![](../../\imgs\devTools-18.jpg)

然后我们点击右侧的html

![](../../\imgs\devTools-19.jpg)

点击事件执行时间和函数每一行执行的时间都给你展示出来，是不是very nice?

## Rendering

> 搜索`Rendering`

![](../../\imgs\devTools-20.jpg)

### 1. Paint Flashing(绘画闪烁)

```js
页面中需要重绘的区域会突显成绿色
```

### 2. Layer Shif Regions(层移位区域)

```js
页面中需要重排的区域会突显成蓝色
```

### 3. Scrolling Performance Issues(滚动性能问题)

```js
页面中减慢滚动速度的元素会突显成蓝绿色，包括触摸滚轮事件处理程序和其他主线程滚动情况
```

![](../../\imgs\devTools-21.jpg)

### 4. Core Web Vitals(核心网络生命力)

![](../../\imgs\devTools-22.jpg)

右上角面板中我们能够看到当前网站的三大核心指标所消耗的时间，这里做个简单的介绍：

- ***LCP*** (Largest Contentful Paint) 显示最大内容元素所需的时间，衡量网站的载入效率

- ***FID*** (First Input Delay) 首次输入延迟时间，衡量网站互动性

- ***CLS*** (Cumulative Layout Shift) 累计版面配置转移，衡量网页稳定性

那我们拿到这些指标，再结合浏览器相关标准就能有目的性的做性能优化。

## 图层面板

> 搜索`Show Layers`

![](../../\imgs\devTools-23.jpg)

`Slow scroll rects`翻译的意思是慢速移动矩形，其实就是对应上面`Rendering`中的第3点，影响页面滚动的元素。有时候在写页面的时候，页面出现横向滚动条又无法定位到哪个子集元素宽度超出了，这时候就可以打开`Layers`面板查看了，能快速定位我们的`css`问题。

## Css概览

> 搜索`Show CSS Overview`

![](../../\imgs\devTools-24.jpg)

颜色和字体颜色可以给ui检查检查，未使用的声明中点击数值可以看到具体的元素，鼠标放上去能定位到DOM位置。

## Memory

> 查看内存使用情况，是否有内存泄露

### 1.测试当前内存

![](../../\imgs\devTools-25.jpg)

点击`Heap snapshot`，再点击左上角圆圈，获取当前快照

![](../../\imgs\devTools-26.jpg)

可以看到当前内存占用情况

### 2.测试内存泄漏

点击`Allocation instrumentation on timeline`，再点击左上角圆圈，开始记录内存情况

![](../../\imgs\devTools-27.jpg)

可以看到内存泄露了，点击`(array)`进去能看到具体哪个变量造成的内存泄露。

### 3.performance查看内存泄露

切到performance也能测内存泄露，我们需要在下方勾选Menory，再去录制即可。

![](../../\imgs\devTools-28.jpg)

可以看到内存一直在增加，没有平稳成一条直线就说明有泄露了。

## 覆盖范围

> 搜索`Coverage`

![](../../\imgs\devTools-29.jpg)

![](../../\imgs\devTools-29-1.jpg)

可以看到,右边百分比就是当前代码未使用到的,单位是字节，所以我们可以对此优化。

## 性能监视器

> 搜索`Show Performance Monitor`

![](../../\imgs\devTools-30.jpg)

可以看到：CPU使用情况、JS内存大小、DOM节点数等都可以实时监控。

监控内存泄漏到平稳图：

![](../../\imgs\devTools-31.jpg)

## FPS

> 简介：FPS(Frames Per Second)，表示每秒传输帧数，是速度单位，是用来分析动画的一个主要性能指标。一般在50-60FPS的动画会相当流程，30FPS就会感觉卡顿了。

![](../../\imgs\devTools-32.jpg)

## 快速切换

- 搜索`Dock to right` 切换调试面板到右侧，`Dock to bottom` 切换调试面板到下侧

- 搜索`theme` 切换`dark`或`light`主题

- ...(未完待续)

## 面板快捷键

- `Ctrl ]` 切换到下个面板，`Ctrl [` 切换到上个面板

- `Ctrl L` 清空`Console`面板，*Ctrl `* 调出Console面板

- `Ctrl +`放大，`Ctrl -`缩小

- 面板右上角齿轮，选择`shortcuts`查看更多

> 作者：戴欧巴 https://juejin.cn/post/7126188054821208100
