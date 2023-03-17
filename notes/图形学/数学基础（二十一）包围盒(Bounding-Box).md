# 包围盒(Bounding Box)

包围盒是一种求解离散点集最优包围空间的算法,基本思想是用体积稍大且特性简单的集合体来近似代替复杂的几何对象。包围盒常用于几何物体的碰撞检测，ray-tracing中光线求交等。常用的包围盒有两种，$AABB$ 与 $OBB$ ，$AABB$ 包围盒是轴对齐的，而 $OBB$ 是任意定向的。

## AABB

$AABB$ 是 $Axially Aligned Bounding Box$ 的缩写，三维 $AABB$ 是一个简单的6面盒子，每一面都与一个主要平面平行，这个盒子不一定是立方体，盒子的长度宽度高度可以各自不同。

### 重要属性

几何体AABB包围盒内的点满足以下不等式：

$$
x_{min } \leq x \leq x_{max}
$$

$$
y_{min } \leq y \leq y_{max}
$$

$$
z_{min } \leq z \leq z_{max}
$$

两个具有特殊意义的点：

- 最小值：$P_{min} = [x_{min}, y_{min}, z_{min}]$

- 最大值：$P_{max} = [x_{max}, y_{max}, z_{max}]$

中心点可以通过最小值和最大值的中点计算得出：

$$
c = \frac{(P_{min} + P_{max})}{2}
$$

大小矢量(Size Vector)s是从 $P_{min}$ 到 $P_{max}$ ​的矢量，该矢量的三个分量分别代表了包围盒的长宽高信息。

### 表示法

一般情况下会使用 $P_{min}​$ 和 $P_{max}$ ​表示包围盒。

![](../../\images\graphics-mathematics-basic-21-vector-1.jpg)

### 计算AABB

针对一组点计算AABB是一个很简单的过程，首先初始化min和max两个值，初始值分别设置为最大值和最小值，遍历点位集合，分别比较判断min,max的三个分量，将当前最小值赋给min最大值赋给max。

![](../../\images\graphics-mathematics-basic-21-vector-2.jpg)

### 变换AABB

有时候我们需要将AABB从一个坐标空间转换为另一个坐标空间。假设在对象空间中有AABB,现在希望在世界空间中获得AABB,其中一种做法就是，可以基于变换后的几何体顶点，重新计算包围盒，但是针对复杂的几何体，顶点数量特别多，这种计算是相对昂贵的，因此在世界空间中获得AABB，就需要考虑变换对象空间的AABB。

### 光线与AABB求交

## OBB

## 参考

[《3D数学基础》图形和游戏开发(第二版)](https://item.jd.com/12659881.html)
