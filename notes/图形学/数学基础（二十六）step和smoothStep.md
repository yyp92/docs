# step和smoothStep

## 阶梯函数 *step*

$step$ 函数接受两个参数,一个边界值 $edge$ ,一个输入变量 $x$ ,当 $x \geq edge$ 时,返回1, 否则返回0。

```ts
function step<T>(edge: T, value: T): T {
    return value >= edge ? 1.0 : 0.0
}
```

函数图像如下:

![](../../\images\graphics-mathematics-basic-26-vector-1.jpg)

## 平滑阶梯函数(*SmoothStep*)

### 定义

*smoothStep* 函数用来生成0到1的平滑过渡值.先看smoothStep函数实现:

```ts
function smoothStep<T>(a: T, b: T, x; T): T {
    let t:T = clamp((x-a)/(b-a), 0, 1)

    return t*t*(3-2t)
}
```

### 数学推导

通过 *smoothStep* 函数定义,我们知道:

$$
res = 3t^2 - 2t^3
$$

$$
t = \frac{(x-a)}{(b-a)}
$$

接下来我们一步步通过函数图像来解释 *smoothStep* 背后的数学原理. 

#### <1> 对于函数 $y=x$ 这个函数,其实就是一条直线:

![](../../\images\graphics-mathematics-basic-26-vector-2.jpg)

#### <2>对于函数 $y=x^2$ ,可以看到它比 $y=x$ 有一个更平滑的过渡:

![](../../\images\graphics-mathematics-basic-26-vector-3.jpg)

但是我们发现 $y=x^2$ 的函数图像仍然是不够“平滑的”，为什么呢？我们知道曲线的切线表示了这条曲线的变化率。通过观察我们明显发现，在0附近的变化率要低于1附近的变化率。

#### <3>  $y=1−(x−1)^2$ 我们在 $y=x^2$ 的基础上构造另外一条曲线.首先先将下2反转.得到:

![](../../\images\graphics-mathematics-basic-26-vector-4.jpg)

将 $y=−x^2$ 向右移动一个单位得到:

![](../../\images\graphics-mathematics-basic-26-vector-5.jpg)

然后再向上移动一个单位得到以下函数图像:

![](../../\images\graphics-mathematics-basic-26-vector-6.jpg)

此时 $y=1−(x−1)^2$

可以看到我们构造的第二条曲线在接近1的部分开始变缓,和 $y=x^2$ 从0开始变缓一样.因此我们将这两条曲线混合到一起即可以得到理想情况下的“平滑过渡”：

利用线性插值混合两条曲线：

$$
y = xf(x) + (1-x)g(x) = x(1-(x-1)^2)+(1-x)x^2 = 3x^2-2x^3
$$

![](../../\images\graphics-mathematics-basic-26-vector-7.jpg)

## 参考

[Scratchapixel](https://www.scratchapixel.com/lessons/mathematics-physics-for-computer-graphics/interpolation)

[GAMES101 -现代计算机图形学入门-闫令琪](https://www.bilibili.com/video/BV1X7411F744?p=15&vd_source=b3b87210888ec87be647603921054a36)

[SmoothStep函数/节点到底做了什么？](https://zhuanlan.zhihu.com/p/424589478)
