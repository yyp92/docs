# TBN空间与TBN矩阵

![](../../\images\graphics-mathematics-basic-34-vector-1.jpg)

切线空间定义于每一个顶点之中，是由切线（Tangent），副切线（BiTangent），顶点法线（Normal）以模型顶点为中心的坐标空间。normalMap 中的法向量在切空间中表示，其中法向量总是大致指向正z方向。切线空间是一个三角形表面的局部空间:法线相对于单个三角形的局部参考系。把它想象成法向量的局部空间;它们都是指向正z方向的不管最终变换的方向是什么。使用一个特定的矩阵，我们可以将这个局部切线空间的法向量转换为世界或视图坐标，并将它们沿最终映射曲面的方向定向。这个矩阵就是 TBN 矩阵。接下来将详细推导TBN 矩阵的构造过程。

![](../../\images\graphics-mathematics-basic-34-vector-2.jpg)

只需要下面两个步骤即可得到规范化的TBN矩阵。

![](../../\images\graphics-mathematics-basic-34-vector-3.jpg)

$$
E_1 = \Delta U_1T + \Delta V_1B
$$

$$
E_2 = \Delta U_2T + \Delta V_2B
$$

该公式的数学意义是，如何将一个点从 uv 空间映射到三维空间，其中 TB 作为基矢量，以 uv 空间中 u 和 v 的增长作为控制参数，假设三角形中存在一点 p，则 $AP=u(p)\ast B+v(p)\ast B$ ,**点p可以表示为以TB为基矢量的uv空间，TB轴的线性组合。**

根据以上公式可以快速的推导出TB：

$$
\vec{T} = \frac{\Delta{V_1E_2} - \Delta{V_2E_1}}{\Delta{V_1}\Delta{U_2} - \Delta{V_2}\Delta{U_1}}
$$

$$
\vec{B} = \frac{-\Delta{U_1E_2} + \Delta{U_2E_1}}{\Delta{V_1}\Delta{U_2} - \Delta{V_2}\Delta{U_1}}
$$

目前给出的 TB 还是不是真正的切线与副切线，需要正交化后得到 TBN 矩阵:

$$
\vec{t_\bot} = normalized(\vec{t} - (\vec{t}\cdot\vec{n})\vec{n})
$$

$$
\vec{b_\bot} = normalized(\vec{b} - (\vec{b}\cdot\vec{n})\vec{n})- (\vec{b}\cdot\vec{t_\bot})\vec{t_\bot})
$$

其中 n 是建模软件中规定的顶点法线，可以看到 n 在正交化过程中不会受到影响，该过程是对 TB 向量进行方向的调整以及长度的归一化。TB 在此过程后会相互垂直，此时将不再一定与 UV 方向保持相同。特别的，当调整顶点法线后，TB 平面甚至将与三维空间中的三角形平面不同,也就是说归正交化后的 TBN 矩阵，TB 轴将不再与 uv 相等，uv 是正交化前的 TB 轴。

通过正交化后的 $Tangent(T),Bitangent(B),Normal(N)$ 可以推导出TBN矩阵：

$$
TBN = 
\begin{bmatrix}
T_x & B_x & N_x \\
T_y & B_y & N_y \\
T_z & B_z & N_z
\end{bmatrix}
$$

normalMap 中存储的法线信息是基于 TBN 空间的，而光照计算需要所有的参数在同一空间下，以上计算出的 TBN 矩阵就是用于实现将 TBN 空间中定义的法线转换到世界空间。

$$
Normal_{world} = 
\begin{bmatrix}
T_x & B_x & N_x \\
T_y & B_y & N_y \\
T_z & B_z & N_z
\end{bmatrix} Normal_{tbn}
$$

根据矩阵的逆的性质，TBN矩阵的逆矩阵可以用来将矢量从世界空间转换到TBN空间中，而TBN矩阵是正交化过的，根据正交矩阵的特殊性质（正交矩阵的逆等于其转置），可以轻松求得TBN的逆矩阵:

$$
TBN^{-1} = TBN^T = 
\begin{bmatrix}
T_x & B_x & N_x \\
T_y & B_y & N_y \\
T_z & B_z & N_z
\end{bmatrix}
$$

$$
Vector_{tbn} = 
\begin{bmatrix}
T_x & T_y & T_z \\
B_x & B_y & B_z \\
N_x & N_y & N_z
\end{bmatrix} Vector_{world}
$$

##### 利用法线贴图的信息计算模型光照

首先是纹理采样拿到当前像素点存储的值，如果纹理贴图是经过压缩的，需要计算（补全）三个通道值，然后将每个通道的数值范围从 $[0,255]$ 映射到 $[−1,1]$ 。要做基于法线的光照计算，需要保证所有的参数（法线 光照方向 观测方向等）都在同一空间下。实现方式有两种:

- 直接使用 TBN 矩阵，这个矩阵可以把切线坐标空间的向量转换到世界坐标空间。因此我们把它传给片段着色器中，把通过采样得到的法线坐标左乘上TBN矩阵，转换到世界坐标空间中，这样所有法线和其他光照变量就在同一个坐标系中了。

- 使用TBN矩阵的逆矩阵，这个矩阵可以把世界坐标空间的向量转换到切线坐标空间。因此我们使用这个矩阵左乘其他光照变量，把他们转换到切线空间，这样法线和其他光照变量再一次在一个坐标系中了。

更常用的做法是采取第二种方式，将向量从世界空间转换到切线空间有个额外好处，我们可以把所有相关向量在顶点着色器中转换到切线空间，不用在像素着色器中做这件事。这是可行的，因为lightPos和viewPos不是每个fragment运行都要改变，对于fs_in.FragPos，我们也可以在顶点着色器计算它的切线空间位置。基本上，不需要把任何向量在像素着色器中进行变换，而第一种方法中就是必须的，因为采样出来的法线向量对于每个像素着色器都不一样。

所以现在不是把TBN矩阵的逆矩阵发送给像素着色器，而是将切线空间的光源位置，观察位置以及顶点位置发送给像素着色器。这样我们就不用在像素着色器里进行矩阵乘法了。这是一个极佳的优化，因为顶点着色器通常比像素着色器运行的少。这也是为什么这种方法是一种更好的实现方式的原因。以下是shader代码；

**vertexShader**

```glsl
# version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;
layout (location = 2) in vec2 aTexCoords;
layout (location = 3) in vec3 aTangent;
layout (location = 4) in vec3 aBitangent;

out VS_OUT {
    vec3 FragPos;
    vec2 TexCoords;
    vec3 TangentLightPos;
    vec3 TangentViewPos;
    vec3 TangentFragPos;
} vs_out;

uniform mat4 projection;
uniform mat4 view;
uniform mat4 model;

uniform vec3 lightPos;
uniform vec3 viewPos;

void main()
{
    vs_out.FragPos = vec3(model * vec4(aPos, 1.0));   
    vs_out.TexCoords = aTexCoords;

    mat3 normalMatrix = transpose(inverse(mat3(model)));
    vec3 T = normalize(normalMatrix * aTangent);
    vec3 N = normalize(normalMatrix * aNormal);
    T = normalize(T - dot(T, N) * N);
    vec3 B = cross(N, T);

    mat3 TBN = transpose(mat3(T, B, N));    
    vs_out.TangentLightPos = TBN * lightPos;
    vs_out.TangentViewPos  = TBN * viewPos;
    vs_out.TangentFragPos  = TBN * vs_out.FragPos;

    gl_Position = projection * view * model * vec4(aPos, 1.0);
}
```

**fragmentShader**

```glsl
# version 330 core
out vec4 FragColor;

in VS_OUT {
    vec3 FragPos;
    vec2 TexCoords;
    vec3 TangentLightPos;
    vec3 TangentViewPos;
    vec3 TangentFragPos;
} fs_in;

uniform sampler2D diffuseMap;
uniform sampler2D normalMap;

uniform vec3 lightPos;
uniform vec3 viewPos;

void main()
{           
     // obtain normal from normal map in range [0,1]
    vec3 normal = texture(normalMap, fs_in.TexCoords).rgb;
    // transform normal vector to range [-1,1]
    normal = normalize(normal * 2.0 - 1.0);  // this normal is in tangent space

    // get diffuse color
    vec3 color = texture(diffuseMap, fs_in.TexCoords).rgb;
    // ambient
    vec3 ambient = 0.1 * color;
    // diffuse
    vec3 lightDir = normalize(fs_in.TangentLightPos - fs_in.TangentFragPos);
    float diff = max(dot(lightDir, normal), 0.0);
    vec3 diffuse = diff * color;
    // specular
    vec3 viewDir = normalize(fs_in.TangentViewPos - fs_in.TangentFragPos);
    vec3 reflectDir = reflect(-lightDir, normal);
    vec3 halfwayDir = normalize(lightDir + viewDir);  
    float spec = pow(max(dot(normal, halfwayDir), 0.0), 32.0);

    vec3 specular = vec3(0.2) * spec;
    FragColor = vec4(ambient + diffuse + specular, 1.0);
}
```

normalMap为什么都是偏蓝色的？

法线贴图是增加细节用的，因此在以原顶点法线为 z 轴的切线空间中，其（r，g，b）中肯定仍然是以 b 分量为重，故法线向量的值基本在（0，0，1）左右，经过-1到1映射到0-1在贴图存储的过程，贴图颜色变为（0.5，0.5，1），反映到颜色上自然是常见的法线贴图颜色了。

normalMap压缩

我们通常会把法线贴图归一化成一个3元向量n（x,y,z）来表示，常识上来看，因为这个n是归一化的，所以用两个向量（x,y）已经可以表示这个3元向量了，可以减少数据存储，压缩我们的贴图量。

- 只保留两个颜色通道
  - 因为normal是归一化向量，其大小为1；又因为切线空间的法线z方向总是正方向，所以可以只存x和y就可以用勾股定理计算出z值（因为正方向所以取正值）。所以只保存rg两个通道。
  - 但是如果只有一个通道那贴图压缩质量更好。所以只用g通道，并将r通道的值存入alpha透明通道。

## 参考

[用一篇文章理解法线变换、切线空间、法线贴图](https://zhuanlan.zhihu.com/p/261667233)

[切线空间（Tangent Space）完全解析](https://zhuanlan.zhihu.com/p/139593847)

[learningOpenGL](https://learnopengl-cn.github.io/05%20Advanced%20Lighting/04%20Normal%20Mapping/)

[GAMES101](https://sites.cs.ucsb.edu/~lingqi/teaching/games101.html)

[scratchapixel](https://www.scratchapixel.com/lessons/mathematics-physics-for-computer-graphics/geometry/transforming-normals)
