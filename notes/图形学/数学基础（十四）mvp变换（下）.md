# mvp变换（下）

## 透视投影（perspectiveprojection）

透视投影满足透视关系，即近大远小的效果。平行的线可能在远处相较于某一点。符合人眼视觉系统。计算机图形学中绝大部分都是使用透视投影，来模拟人自然看到物体的样子。

![](../../\images\graphics-mathematics-basic-14-vector-1.jpg)

### 视锥体（Frustum）定义

和正交投影不同,透视投影的视景体是个截锥体(Frustum)

![](../../\images\graphics-mathematics-basic-14-vector-2.jpg)

视锥体由以下几个参数定义：

- fov（field of view）: 视角范围
- aspectRatio（长宽比）：定义近平面的宽高比
- n（近平面距离）：近平面距离原点距离

![](../../\images\graphics-mathematics-basic-14-vector-3.jpg)

由视锥体的参数可以推导出 $l,r,b,t$ ：

$$
\tan\frac{fov}{2} = \frac{t}{\lvert n \rvert}
$$

$$
aspectRadio = \frac{r}{t}
$$

### 推导原理

观察上图我们发现,透视投影远平面比近平面要大些,除了这些两个视景体几乎是一样的。所以在我们做透视投影变换时，不妨先将其转换为右侧的长方体，然后再做一次正交投影变换即可。

$$
M_{perspective} = M_{orthographic}M_{persp \to ortho}
$$

### 挤压变换

![](../../\images\graphics-mathematics-basic-14-vector-4.jpg)

任选一点P，如果将其“挤压”至P1点，我们观察 $\Delta OPR$ 和 $\Delta NPP_1$ ,不难发现两个三角形是相似三角形，根据相似三角形定律: $y' = \frac{ny}{z}$ ,同理  $x' = \frac{nx}{z}$ .

在齐次坐标下：

$$
M_{persp \to ortho} \begin{bmatrix}
x \\
y \\
z \\
1
\end{bmatrix} = 
\begin{bmatrix}
\frac{nx}{z} \\
\frac{ny}{z} \\
unknown \\
1
\end{bmatrix} = 
\begin{bmatrix}
nx \\
ny \\
unknown \\
z
\end{bmatrix}
$$

$$
M_{persp \to ortho} = 
\begin{bmatrix}
n & 0 & 0 & 0 \\
0 & n & 0 & 0 \\
? & ? & ? & ? \\
0 & 0 & 1 & 0 
\end{bmatrix}
$$

近平面的任意一点在挤压后的z值不会改变：

$$
\begin{bmatrix}
\frac{nx}{n} \\
\frac{ny}{n} \\
n \\
1
\end{bmatrix} = 
\begin{bmatrix}
nx \\
ny \\
n^2 \\
n
\end{bmatrix}
$$

$$
\begin{bmatrix}0 & 0 & A & B \\\end{bmatrix}\begin{bmatrix}x \\y \\n \\1\end{bmatrix} = n^2
$$

$$
An + B = n^2
$$

远平面的任意一点挤压后z值不会改变,更特殊的是，远平面的中心点（0，0，f）在挤压后仍然在（0，0，f），根据这一特性我们能够得到：

$$
\begin{bmatrix}0 \\0 \\f \\1\end{bmatrix} = \begin{bmatrix}0 \\0 \\f^2 \\f\end{bmatrix}
$$

$$
Af + B = f^2
$$

解二元一次方程组：

$$
An + B = n^2
$$

$$
Af + B = f^2
$$

$$
A = n + f
$$

$$
B = -nf
$$

$$
M_{persp \to ortho} = 
\begin{bmatrix}
n & 0 & 0 & 0 \\
0 & n & 0 & 0 \\
? & ? & ? & ? \\
0 & 0 & 1 & 0
\end{bmatrix} = 
\begin{bmatrix}
n & 0 & 0 & 0 \\
0 & n & 0 & 0 \\
0 & 0 & A & B \\
0 & 0 & 1 & 0
\end{bmatrix} = 
\begin{bmatrix}
n & 0 & 0 & 0 \\
0 & n & 0 & 0 \\
0 & 0 & n + f & -nf \\
0 & 0 & 1 & 0
\end{bmatrix}
$$

$$
M_{perspective} = M_{orthographic}M_{persp \to ortho} = 
\begin{bmatrix}\frac{2}{r-l} & 0 & 0 & 0 \\0 & \frac{2}{t-b} & 0 & 0 \\0 & 0 & \frac{2}{n-f} & 0 \\0 & 0 & 0 & 1\end{bmatrix}\begin{bmatrix}1 & 0 & 0 & \frac{r+l}{2} \\0 & 1 & 0 & \frac{t+b}{2} \\0 & 0 & 1 & \frac{n+f}{2} \\0 & 0 & 0 & 1\end{bmatrix}\begin{bmatrix}n & 0 & 0 & 0 \\0 & n & 0 & 0 \\0 & 0 & n + f & -nf \\0 & 0 & 1 & 0\end{bmatrix}
$$

## 视口变换(viewport)

经过复杂的投影变换后，我们将空间中的一个视景体映射到了 $[-1, 1]^3$ 的标准立方体中，接下来我们将通过视口变换将其映射到屏幕空间中。

### 屏幕空间（screenSpace）定义

![](../../\images\graphics-mathematics-basic-14-vector-5.jpg)

- 屏幕左下角为原点（0，0）
- 向右为x，向上为y
- 像素（pixel）的坐标以（x，y）表示，xy均为整数
- 像素坐标的范围 （0，0） ~ （width - 1，height - 1）
- Pixel（x，y）的中心点位于（x+0.5，y+0.5）
- 每个像素形成单位宽高的“正方体”
- 屏幕空间覆盖范围为（0，0）~（width，height）

### 从 $[-1, 1]^2$ 到 $[0,width][0,height]$

$$
原地缩放 \begin{bmatrix} \frac{width}{2}\\ \frac{height}{2} \end{bmatrix}​，然后平移到 \begin{bmatrix} \frac{width}{2}\\ \frac{height}{2} \end{bmatrix}
$$

$$
M_{viewport} = 
\begin{bmatrix}
\frac{width}{2} & 0 & 0 & \frac{width}{2} \\
0 & \frac{height}{2}& 0 & \frac{height}{2} \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1 \\
\end{bmatrix}
$$

## 参考

[《3D数学基础》图形和游戏开发(第二版)](https://item.jd.com/12659881.html)

[GAMES101 -现代计算机图形学入门-闫令琪](https://www.bilibili.com/video/BV1X7411F744?p=5&spm_id_from=pageDriver&vd_source=b3b87210888ec87be647603921054a36)

[fundamentals-of-computer-graphics](https://item.jd.com/10037953813770.html)
