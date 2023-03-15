# mvp变换(中)

本章我们将实现mvp变换中的投影变换（projectionTransform），投影变换有两种方式，分别为正交投影（Orthographicprojection）和透视投影（perspectiveprojection）.两种投影方式最终都将视景体内的物体投影到近平面上（将视图从相机空间映射到 $[-1, 1]^3$ ），也是3D空间映射到2D屏幕空间最为关键的一步。的标准立方体中，如下图所示：

![](../../\images\graphics-mathematics-basic-13-vector-1.jpg)

## 正交投影（Orthographicprojection）

正交投影会保持几何图元相对位置关系不变，平行的线永远平行。不满足透视关系。又叫做平行投影。

正交投影的“视景体”是一个长方形，由 top bottom left right near far6个参数定义。如下图所示：

![](../../\images\graphics-mathematics-basic-13-vector-2.jpg)

如何将该长方体映射到 $[−1,1]^3$ 呢?和 $M_{view}$ 类似,我们先将立方体中心移动到原地,然后根据每个方向的长度,缩放到[-1,1]之间.即 $\textbf{M}_{scale}\textbf{M}_{translate}$ .需要一次平移变换 + 一次缩放变换.

![](../../\images\graphics-mathematics-basic-13-vector-3.jpg)

### $M_{translate​}$平移矩阵

根据正交投影视景体的6个参数,我们可以计算出它的中心位置.即:

$$
center = 
\begin{bmatrix}
\frac{r + l}{2} \\
\frac{t + b}{2} \\
\frac{n + f}{2} 
\end{bmatrix}
$$

$$
M_{translate} = 
\begin{bmatrix}
1 & 0 & 0 & \frac{r + l}{2} \\
0 & 1 & 0 & \frac{t + b}{2} \\
0 & 0 & 1 & \frac{n + f}{2} \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

### $M_{scale}$​缩放矩阵

同样的,根据视景体6个参数,可以计算出立方体的长宽高(假设z方向的边为长,y方向的边为高)分别为:  $n-f, r-l, t-b$ .

根据缩放矩阵的定义可知:

$$
M_{scale} = 
\begin{bmatrix}
\frac{2}{r - l} & 0 & 0 & 0 \\
0 & \frac{2}{t - b} & 0 & 0 \\
0 & 0 & \frac{2}{n - f} & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

### 正交投影矩阵

$$
M_{orthographic} = M_{scale}M_{translate} = 
\begin{bmatrix}
\frac{2}{r - l} & 0 & 0 & 0 \\
0 & \frac{2}{t - b} & 0 & 0 \\
0 & 0 & \frac{2}{n - f} & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
1 & 0 & 0 & \frac{r + l}{2} \\
0 & 1 & 0 & \frac{t + b}{2} \\
0 & 0 & 1 & \frac{n + f}{2} \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

## 参考

[《3D数学基础》图形和游戏开发(第二版)](https://item.jd.com/12659881.html)

[GAMES101 -现代计算机图形学入门-闫令琪](https://www.bilibili.com/video/BV1X7411F744?p=5&spm_id_from=pageDriver&vd_source=b3b87210888ec87be647603921054a36)

[fundamentals-of-computer-graphics](https://item.jd.com/10037953813770.html)
