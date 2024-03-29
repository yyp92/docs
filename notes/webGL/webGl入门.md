# webGl入门

## WebGL是什么

WebGL（Web图形库）是一个JavaScript API，可在任何兼容的web浏览器中渲染高性能的交互式3D和2D图形，而无需使用插件。WebGL通过引入一个与OpenGL ES 2.0非常一致的API来做到这一点，该API可以在HTML5 <canvas> 元素中使用。这种一致性使API可以利用用户设备提供的硬件图形加速。

## WebGL 发展史

WebGL的发展最早要追溯到2006年，WebGL起源于 Moailla 员工弗拉基米尔.弗基西维奇的一项 Canvas 3D实验项目，并于2006年首次展示了 Canvas 3D的原型。这一项技术在2007年底在FireFox和Opera浏览器中实现 。2009你那初 Khronos Group联盟创建了 WebGL 的工作组最初的工作成员包括 Apple、Goolgle、Mozilla、Opera 等。2011年3月 WebGL 1.0 规范发布，WebGL 2.0 规范的发展始于 2013年，并于2017年1月完成，WebGL 2.0 的规范，首度在FIREFox 51、chrome 56 和 Opera43 中被支持。

## 渲染管线

WebGL 的渲染依赖层 GPU 的渲染能力。所以 WebGL 渲染流程和 GPU 的内容的渲染管线是相符的。

**渲染管线的作用是将3D模型转换为2维图像。**

在早期，渲染管线是不可编程的，叫做**固定渲染管线**，工作的细节已经固定，修改的话需要调整一些参数。现代的GPU所包含的渲染管线为**可编辑渲染管线**，可以通过编程 **GLSL 着色器语言** 来控制一些渲染阶段的细节。简单来说：就是使用 **shader**, 我们可以对画布中 **每个像素点做处理**，然后就可以生成各种酷炫的效果了。

![](../../\imgs\webgl-render-1.png)

![](../../\imgs\webgl-render-2.png)

## 渲染流程

渲染历程大概经历了下面这么多过程，因为本篇的重点其实是在着色器，所以重点分析从**顶点着色器 --> 片元着色器**的一个过程：

- 顶点着色器

- 图片装配

- 光栅化

- 片元着色器

- 裁剪测试

- 多重采样操作

- 背面剔除

- 模板测试

- 深度测试

- 融合

- 缓存

## 顶点着色器

WebGL就是和GPU打交道，在GPU上运行的代码是一对着色器，一个是顶点着色器，另一个是片元着色器。每次调用着色程序都会先执行顶点着色器，再执行片元着色器。

![](../../\imgs\webgl-render-3.png)

一个顶点着色器的工作是生成裁剪空间坐标值，通常是以下的形式：

```js
const vertesShaderSource = `
    attribute vec3 position;
    void main() {
        gl_Position = vec4(position, 1)
    }
}
`
```

每个顶点调用一次（顶点）着色器，每次调用都需要设置一个特殊的全局变量 **gl_Position**。该变量的值就是裁剪空间坐标值。

**为什么需要裁剪空间的坐标值？**

何为裁剪空间坐标？就是无论你的画布有多大，裁剪坐标的坐标范围永远是 -1 到 1。

看下面这张图：

![](../../\imgs\webgl-render-4.png)

如果运行一次顶点着色器，那么gl_Position 就是（-0.5, -0.5, 0, 1）记住他永远是个 Vec4 ,简单理解就是对应 x、y、z、w。即使你没用其他的，也要设置默认值，这就是所谓的 3维模型转换到我们屏幕中。

顶点着色器需要的数据，可以通过以下四种方式获得。

- attribute 属性（从缓存读取数据）

- uniforms 全局变量（一般用来对物体做整体变化、旋转、缩放）

- tectures 纹理（从像素或者纹理获得数据）

- varyings 变量（将顶点着色器的变量传给片元着色器）

## 图元装配和光栅化

什么是图元？

**描述各种图形元素的函数叫做图元，描述几何元素的称为几何图元（点、线段或多边形）。点和线是最简单的几何图元**。经过顶点着色器计算之后的坐标会被组装成**组合图元**。

通俗解释：图元就是一个点、一条线段或者是一个多边形。

**什么是图元装配呢？**

简单理解就是说将我们设置的顶点、颜色、纹理等内容组装称为一个可渲染的多边形的过程。

组装的类型取决于：你最后绘制选择的图形类型

```js
gl.drawArrays(gl.TRIANGLES, 0, 3)
```

如果是三角形的话，顶点着色器就执行三次。

## 光栅化

什么是光栅化？

通过图元装配生成的多边形，计算像素填充，剔除不可见的部分，剪裁掉不在可视范围内的部分。最终生成可见的带有颜色数据的图形并绘制。

光栅化流程图解：

![](../../\imgs\webgl-render-5.png)

## 剔除和剪裁

![](../../\imgs\webgl-render-6.png)

**剔除：** 在日常生活中，对于不透明物体，背面对于观察者来说是不可见的。同样，在webgl中，我们也可以设定物体的背面不可见，那么在渲染过程中，就会将不可见的部分剔除，不参与绘制。节省渲染开销。

![](../../\imgs\webgl-render-7.png)

**剪裁：** 日常生活中不论是在看电视还是观察物体，都会有一个可视范围，在可视范围之外的事物我们是看不到的。类似的。图形生成后，有的部分可能位于可视范围之外，这一部分会被剪裁掉，不参与绘制。以此来提高性能。这个就是**视锥体**，在相机范围内能看到的东西，才进行绘制。

![](../../\imgs\webgl-render-8.png)

## 片元着色器

!](../../\imgs\webgl-render-9.png)

!](../../\imgs\webgl-render-10.png)

光栅化后，每一个像素点都包含了颜色、深度、纹理数据，这个我们叫做片员。

> tips: 每个像素的颜色由片元着色器的 gl_FragColor 提供接收光栅化阶段生成的片元，在光栅化阶段中，已经计算出每个片元的颜色信息，这一阶段会将片元做逐片元挑选的操作，处理过的片元会继续向后面的阶段传递。**片元着色器运行的次数由图形有多少个片元决定的。**

逐片元挑选

通过模板测试和深度测试来确定片元是否要显示，测试过程中会丢弃掉部分无用的片元内容，然后生成可绘制的二维图像绘制并显示。

- **深度测试：** 就是对 z 轴的值做测试，值比较小的片元内容会覆盖值比较大的。（类似于近处的物体会遮挡远处物体）

- **模板测试：** 模拟观察者的观察行为，可以接为镜像观察。标记所有镜像中出现的片元，最后只绘制有标记的内容。

## 实战 - 绘制个三角形

在进行实战之前，我们先看一张图，能大概了解，用原生webgl生成一个三角形需要哪些步骤：

![](../../\imgs\webgl-render-11.png)

### 绘制类型共有以下几种

![](../../\imgs\webgl-render-12.png)
