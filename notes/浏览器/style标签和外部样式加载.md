# style标签加载 vs 外部样式加载

## 工作中常见问题

为什么有时候进入页面会产生'闪屏现象'(样式结构突然改变), 有的时候进入网页会等待一会然后才会展示网页内容.

因为使用了两种不同的样式修改方式所导致的,一种是style标签中进行样式修改,一种是通过link css样式表进行修改.下面我们来看一些两者加载的不同点吧.

## 谷歌浏览器分析工具使用

首先了解针对style和link css样式表解析不同之前先了解一下谷歌浏览器中的分析工具, 注意解析html和加载完成两个日志.

![](../../\images\style-vs-link-1.jpg)

## 使用bootstrap的样式文件进行分析

### 情况1: 直接下载bootstrap的样式放入到style标签中

```html
<!doctype html>
<html lang="en">
    <head>
        <style>
            /* 假设里面为bootscrap样式 */
        </style>
    </head>

    <body>
        <h1>标题</h1>
    </body>
</html>
```

分析结果

![](../../\images\style-vs-link-2.jpg)

此时这个style标签过大接近5000行, 可以看出是一边加载html一边解析. html是异步进行解析的. 这样边加载边解析就可能会产生闪屏现象.

### 情况2 使用link标签加载css样式

```html
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        // 链接cdn
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">
    </head>

    <body>
        <h1>标题</h1>
    </body>
</html>
```

分析结果

![](../../\images\style-vs-link-3.jpg)

Parse Stylesheet 解析css样式, 样式解析完成后页面完成.

## style 与 link css区别

**渲染方面:**

style样式是使用HTML进行解析是异步的, 不会阻塞浏览器渲染，不会阻塞DOM解析, 会一边解析一边渲染。

link css标签解析虽然不会阻塞DOM树的解析但是会阻塞DOM树渲染, 所以必须等待它加载完成后才会渲染页面。

**加载方面:**

因为style是写在html中的首次加载会更快, link是外部链接加载相对慢一些但是浏览器可以对该文件进行缓存减少下一次访问时间。

## 两者使用

个人认为应该具体分析使用情况, 如果样式较少可以使用style进行更改提高加载速度, 如果样式较多可以抽离一个单独的css文件. 当然如果不是对页面极致的性能需求我觉得还是使用link分离样式会更好,代码整洁也便于维护。

## 扩展

这篇文章主要说明了内联样式渲染和外链样式渲染不同点,关于外链css样式也有很多细节问题, 只有了解css加载的细节问题才能更好的对网页进行优化. [可以点击这里查看.](https://juejin.cn/post/7065299247129690120)
