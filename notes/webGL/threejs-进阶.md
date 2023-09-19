# threejs-进阶

## 基础入门 （上）

### 渲染器 WebGLRenderer

`WebGLRenderer` 用 `WebGL` 渲染出场景。通过 `new THREE.WebGLRenderer` 初始化渲染器，并将 `canvas` 容器作为参数传给它。通过调用 `setSize` 方法设置渲染器的尺寸；调用 `setPixelRatio` 设置 `canvas` 的像素比为当前设备的屏幕像素比，避免高分屏下出现模糊情况。

### 场景 Scene

`Scene` 是场景对象，所有的网格对象、灯光、动画等都需要放在场景中，使用 `new THREE.Scene` 初始化场景，下面是场景的一些常用属性和方法。

- `fog`：设置场景的雾化效果,可以渲染出一层雾气，隐层远处的的物体。
- `overrideMaterial`：强制场景中所有物体使用相同材质。
- `autoUpdate`：设置是否自动更新。
- `background`：设置场景背景，默认为黑色。
- `children`：所有对象的列表。
- `add()`：向场景中添加对象。
- `remove()`：从场景中移除对象。
- `getChildByName()`：根据名字直接返回这个对象。
- `traverse()`：传入一个回调函数访问所有的对象。

### 透视相机 PerspectiveCamera

相机类型可以分为**正交相机**和**透视相机**，一般使用的是透视相机 `PerspectiveCamera`。

```js
PerspectiveCamera(fov, aspect, near, far)
```

- `fov`：表示视场，就是能够看到的角度范围，人的眼睛大约能够看到 `180度` 的视场，视角大小设置要根据具体应用，一般游戏会设置 `60~90` 度，默认值 `45`。
- `aspect`：表示渲染窗口的长宽比，如果一个网页上只有一个全屏的 `canvas` 画布且画布上只有一个窗口，那么 `aspect` 的值就是网页窗口客户区的宽高比 `window.innerWidth/window.innerHeight`。
- `near`：属性表示的是从距离相机多远的位置开始渲染，一般情况会设置一个很小的值。 默认值 `0.1`。
- `far`：属性表示的是距离相机多远的位置截止渲染，如果设置的值偏小，会有部分场景看不到，默认值 `1000`。

#### 页面缩放适配

为了防止画布渲染内容在浏览器尺寸发生变化时产生变形和移位，可以对 `resize` 事件进行监听，当页面发生变化时，同时更新渲染器的尺寸和相机视角比例，并调用 `.updateProjectionMatrix()` 手动更新相机对象的投影矩阵属性。

```js
// 页面缩放事件监听
window.addEventListener('resize', () => {
  sizes.width = window.innerWidth;
  sizes.height = window.innerHeight;
  // 更新渲染
  renderer.setSize(sizes.width, sizes.height);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
  // 更新相机
  camera.aspect = sizes.width / sizes.height;
  camera.updateProjectionMatrix();
}); 
```

### 立方体 BoxGeometry

`BoxGeometry` 是四边形的原始几何类，来创建立方体或者不规则四边形。

```js
BoxGeometry(width : Float, height : Float, depth : Float, widthSegments : Integer, heightSegments : Integer, depthSegments : Integer)
```

- `width`：`X轴` 的宽度，默认为 `1`。
- `height`：`Y轴` 的高度，默认为 `1`。
- `depth`：`Z轴` 的深度，默认为 `1`。
- `widthSegments`：可选，宽度的分段数，默认是 `1`。
- `heightSegments`：可选，高度的分段数，默认是 `1`。
- `depthSegments`：可选，深度的分段数，默认是 `1`。

### 基础网格材质 MeshBasicMaterial

基础网格材质是一种一个以简单着色方式来绘制几何体的材质，它不受光照的影响。

```js
MeshBasicMaterial(parameters: Object)
```

- `parameters`：可选，用于定义材质外观的对象，具有一个或多个属性如 `color`、`map` 等。

### 动画更新

完成上面的内容，如果你在浏览器中直接运行，会发现页面不会加载任何东西，因为还没有进行真正的渲染。为此，我们需要调用 `requestAnimationFrame` 在页面重回动画中更新渲染内容。在这里创建了一个 `tick` `动画方法，它的功能是使渲染器可以在每次屏幕刷新时对场景进行绘制的循环，在大多数屏幕上，requestAnimationFrame` 刷新率一般为 `60次/秒`。

## 基础入门（下）

### Scene.Fog 场景雾化

`Fog` 类定义的是线性雾，雾的密度随着距离线性增大，即场景中物体雾化效果随着随距离线性变化。

```js
Fog(color, near, far)
```

- `color`: 表示雾的颜色，如设置为白色，场景中远处物体为蓝色，场景中最近处距离物体是自身颜色，最远和最近之间的物体颜色是物体本身颜色和雾颜色的混合效果。
- `near`：表示应用雾化效果的最小距离，距离活动摄像机长度小于 `near` 的物体将不会被雾所影响。
- `far`：表示应用雾化效果的最大距离，距离活动摄像机长度大于 `far` 的物体将不会被雾所影响。

### 光源 Light

`three.js` 中提供了很多种光源，它们可以模拟现实世界中大部分场景的光照效果。光源的使用方法也大致和本文示例中是一样的。下表列出了几种常用的光源，可以根据自己的需求场景分别选择不同的光源。大家可以在实践中把本示例中的环境光 `AmbientLight` 换成其他效果光源，看看它们生成的有什么区别。

| 光源名称              | 描述                                         |
| ----------------- | ------------------------------------------ |
| `AmbientLight`    | 环境光，是一种基础光源，它的颜色会添加到整个场景和所有对象的当前颜色上        |
| `PointLight`      | 点光源，空间中的一点，朝所有的方向发射光线                      |
| `SpotLight`       | 聚光灯光源，这种光源有聚光的效果，类似台灯、天花板上的吊灯，或者手电筒        |
| `DirectionLight`  | 方向光，也称为无限光。从这种光源发出的光线可以看着平行的。例如，太阳光        |
| `HemishpereLight` | 半球光，这是一种特殊光源，可以用来创建更加自然的室外光线，模拟放光面和光线微弱的天空 |
| `AreaLight`       | 面光源，使用这种光源可以指定散发光线的平面，而不是空间中的一个点           |
| `LensFlare`       | 镜头眩光，不是源，但可以通过 `LensFlare` 为场景中的光源添加镜头光晕效果 |

光源的一些通用属性：

- `color`：光源颜色。
- `intensity`：光照强度。默认值是 `1`。
- `visible`：如果设为 `true`，该光源就会显示；如果设置为`false`，光源就会消失。

### 几何体 Geometry

下面汇总了 `Three.js` 常用几何体的分类介绍以及构造器的参数，后续使用过程中可通过此表查询。由于本文篇幅内容有限，就不一一展示具体形状，大家在学习过程中一定要亲自动手试试各种几何体创建后是什么样子的，也可以多看看 `threejs.org` 官网文档。

| 名称                     | 构造器参数                                                                                                                                                                                                                                                                                                                                                          |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `PlaneGeometry`        | 【平面几何体】 `width` — 平面沿着X轴的宽度。默认值是 `1`。`height` — 平面沿着 `Y轴` 的高度。默认值是 `1`。`widthSegments` — 可选，平面的宽度分段数，默认值是 `1`。 `heightSegments` — 可选，平面的高度分段数，默认值是 `1`。                                                                                                                                                                                                        |
| `CircleGeometry`       | 【圆形几何体】 `radius` — 圆形的半径，默认值为`1`。 `segments` — 分段的数量，最小值为 `3`，默认值为 `8`。`thetaStart` — 第一个分段的起始角度，默认为 `0`。`thetaLength` — 圆形扇区的中心角，通常被称为 `θ`。默认值是 `2*Pi`，这使其成为一个完整的圆。                                                                                                                                                                                           |
| `RingGeometry`         | 【环形几何体】 `innerRadius` — 内部半径，默认值为 `0.5`。`outerRadius` — 外部半径，默认值为 `1`。`thetaSegments` — 圆环的分段数。这个值越大，圆环就越圆。最小值为 `3`，默认值为 `8`。`phiSegments` — 最小值为 `1`，默认值为 `8`。`thetaStart` — 起始角度，默认值为 `0`。`thetaLength` — 圆心角，默认值为 `Math.PI * 2`。                                                                                                                            |
| `ShapeGeometry`        | 【形状几何体】 `shapes` — 一个单独的 `shape`，或者一个包含形状的 `Array`。 `curveSegments` - `Integer` - 每一个形状的分段数，默认值为 `12`。                                                                                                                                                                                                                                                         |
| `BoxGeometry`          | 【立方几何体】 `width` — X轴上面的宽度，默认值为 `1`。 `height` — `Y` 轴上面的高度，默认值为 `1`。 `depth` — `Z` 轴上面的深度，默认值为 `1`。`widthSegments` — 可选，宽度的分段数，默认值是 `1`。`heightSegments` — 可选，宽度的分段数，默认值是 `1`。`depthSegments` — 可选，宽度的分段数，默认值是 `1`。                                                                                                                                             |
| `SphereGeometry`       | 【球几何体】 `radius` — 球体半径，默认为 `1`。`widthSegments` — 水平分段数，最小值为 `3`，默认值为 `8`。`heightSegments` — 垂直分段数，最小值为 `2`，默认值为 `6`。`phiStart` — 指定水平起始角度，默认值为 `0`。 `phiLength` — 指定水平扫描角度的大小，默认值为 `Math.PI * 2`。`thetaStart` — 指定垂直起始角度，默认值为 `0`。`thetaLength` — 指定垂直扫描角度大小，默认值为 `Math.PI`。                                                                                   |
| `CylinderGeometry`     | 【圆柱几何体】 `radiusTop` — 圆柱的顶部半径，默认值是 `1`。`radiusBottom` — 圆柱的底部半径，默认值是 `1`。`height` — 圆柱的高度，默认值是 `1`。`radialSegments` — 圆柱侧面周围的分段数，默认为 `8`。 `heightSegments` — 圆柱侧面沿着其高度的分段数，默认值为 `1`。`openEnded` — 一个 `Boolean` 值，指明该圆锥的底面是开放的还是封顶的。默认值为 `false`，即其底面默认是封顶的。`thetaStart` — 第一个分段的起始角度，默认为 `0`。`thetaLength` — 圆柱底面圆扇区的中心角，通常被称为 `“θ”`。默认值是 `2*Pi`，这使其成为一个完整的圆柱。 |
| `ConeGeometry`         | 【圆锥几何体】 `radius` — 圆锥底部的半径，默认值为 `1`。height — 圆锥的高度，默认值为1。 `radialSegments` — 圆锥侧面周围的分段数，默认为 `8`。`heightSegments` — 圆锥侧面沿着其高度的分段数，默认值为 `1`。`openEnded` — 一个Boolean值，指明该圆锥的底面是开放的还是封顶的。默认值为 `false`，即其底面默认是封顶的。·thetaStart· — 第一个分段的起始角度，默认为 `0`。`thetaLength` — 圆锥底面圆扇区的中心角，通常被称为 `“θ”`。默认值是 `2*Pi`，这使其成为一个完整的圆锥。                                               |
| `TorusGeometry`        | 【圆环几何体】 `radius` - 圆环的半径，从圆环的中心到管道的中心。默认值是 `1`。`tube` — 管道的半径，默认值为 `0.4`。`radialSegments` — 圆环的分段数，默认值为 `8`。`tubularSegments` — 管道的分段数，默认值为 `6`。`arc` — 圆环的中心角，默认值为 `Math.PI * 2`。                                                                                                                                                                             |
| `TextGeometry`         | 【文本几何体】 `font` — `THREE.Font` 的实例。`size` — `Float`。字体大小，默认值为 `100`。`height` — `Float`。挤出文本的厚度。默认值为 `50`。`curveSegments` — `Integer`。曲线上点的数量。默认值为 `12`。`bevelEnabled` — `Boolean`。是否开启斜角，默认为 `false`。`bevelThickness` — `Float`。文本上斜角的深度，默认值为 `20`。`bevelSize` — `Float`。斜角与原始文本轮廓之间的延伸距离。默认值为 `8`。`bevelSegments` — `Integer`。斜角的分段数。默认值为 `3`。                 |
| `TetrahedronGeometry`  | 【四面几何体】 `radius` — 四面体的半径，默认值为 `1`。`detail` — 默认值为 `0`。将这个值设为一个大于 `0` 的数将会为它增加一些顶点，使其不再是一个四面体。                                                                                                                                                                                                                                                                 |
| `OctahedronGeometry`   | 【八面几何体】 `radius` — 八面体的半径，默认值为 `1`。`detail` — 默认值为 `0`，将这个值设为一个大于 `0` 的数将会为它增加一些顶点，使其不再是一个八面体。                                                                                                                                                                                                                                                                 |
| `DodecahedronGeometry` | 【十二面几何体】 `radius` — 十二面体的半径，默认值为 `1`。`detail` — 默认值为 `0`。将这个值设为一个大于 `0` 的数将会为它增加一些顶点，使其不再是一个十二面体。                                                                                                                                                                                                                                                              |
| `IcosahedronGeometry`  | 【二十面几何体】 `radius` — 二十面体的半径，默认为 `1`。`detail` — 默认值为 `0`。将这个值设为一个大于 `0` 的数将会为它增加一些顶点，使其不再是一个二十面体。当这个值大于 `1` 的时候，实际上它将变成一个球体。                                                                                                                                                                                                                                    |
| `TorusKnotGeometry`    | 【圆环扭结几何体】 `radius` - 圆环的半径，默认值为 `1`。`tube` — 管道的半径，默认值为 `0.4`。`tubularSegments` — 管道的分段数量，默认值为 `64`。`radialSegments` — 横截面分段数量，默认值为 `8`。`p` — 这个值决定了几何体将绕着其旋转对称轴旋转多少次，默认值是 `2`。`q` — 这个值决定了几何体将绕着其内部圆环旋转多少次，默认值是 `3`。                                                                                                                                          |
| `PolyhedronGeometry`   | 【多面几何体】 `vertices` — 一个顶点 `Array`：`[1,1,1, -1,-1,-1, … ]`。`indices` — 一个构成面的索引 `Array`， `[0,1,2, 2,3,0, … ]`。`radius` — `Float` - 最终形状的半径。`detail` — `Integer` - 将对这个几何体细分多少个级别。细节越多，形状就越平滑。                                                                                                                                                                   |
| `TubeGeometry`         | 【管道几何体】 `path` — `Curve` - 一个由基类 `Curve` 继承而来的路径。`tubularSegments` — `Integer` - 组成这一管道的分段数，默认值为 `64`。`radius` — `Float` - 管道的半径，默认值为 `1`。`radialSegments` — `Integer` - 管道横截面的分段数目，默认值为 `8`。`closed` — `Boolean` 管道的两端是否闭合，默认值为 `false`。                                                                                                                      |

#### 材质 Material

材质可以模拟现实世界中物体表面的物理特性，`Three.js` 也提供的丰富的材质，我们在创建不同物体时可以选择不同的材质，比如创建木制桌面时可以选择 `MeshPhysicalMaterial 物理网格材质`，创建卡通风格模型时可以选择 `MeshToonMaterial 卡通网格材质`。下表列出了几种常用的材质类型及其说明。

| 名称                     | 描述                                              |
| ---------------------- | ----------------------------------------------- |
| `MeshBasicMaterial`    | 基础网格基础材质，用于给几何体赋予一种简单的颜色，或者显示几何体的线框。            |
| `MeshDepthMaterial`    | 网格深度材质，这个材质使用从摄像机到网格的距离来决定如何给网格上色。              |
| `MeshStandardMaterial` | 标准网格材质，一种基于物理的标准材质，使用 `Metallic-Roughness` 工作流程 |
| `MeshPhysicalMaterial` | 物理网格材质，`MeshStandardMaterial` 的扩展，能够更好地控制反射率。   |
| `MeshNormalMaterial`   | 网格法向材质，这是一种简单的材质，根据法向向量计算物体表面的颜色。               |
| `MeshLambertMaterial`  | 网格 `Lambert` 材质，这是一种考虑光照影响的材质，用于创建暗淡的、不光亮的物体。   |
| `MeshPhongMaterial`    | 网格 `Phong` 式材质，这是一种考虑光照影响的材质，用于创建光亮的物体。         |
| `MeshToonMaterial`     | 网格 `Phong` 式材质，`MeshPhongMaterial` 卡通着色的扩展。     |
| `ShaderMaterial`       | 着色器材质，这种材质允许使用自定义的着色器程序，直接控制顶点的放置方式以及像素的着色方式。   |
| `LineBasicMaterial`    | 直线基础材质，这种材质可以用于 `THREE.Line` 直线几何体，用来创建着色的直线。   |

### 模型

> `📌` Three.js 支持的模型格式：3ds (.3ds)、amf (.amf)、3mf (.3mf)、assimp & assimp2json (.assimp |.json)、awd (.awd)、Babylon (.babylon)、BVH (.bvh)、Collada(.dae |.xml)、OpenCTM (.ctm)、draco(.drc)、FBX(.fbx)、GCode (.gcode)、glTF (.gltf)、Clara(.json)、KMZ(.kmz)、LDraw(.mpd)、LightWave(.lwo)、MD2 (.md2)、MMD(.pmd | .vmd)、nrrd (.nrrd)、obj/obj2 (.obj)、pcd (.pcd)、PDB(.pdb)、PlayCanvas(.json)、ply (.ply)、prwm(.prwm)、sea3d(.sea3d)、stl(.stl)、vrm(.vrm)、vrml(.vrml)、vtk、x等。

### 贴图

为了模拟更加真实的效果，就要给模型材质添加贴图，贴图就像模型的皮肤一样，使其三维效果更佳。添加贴图的原理是通过纹理贴图加载器 `TextureLoader()` 去新创建一个贴图对象出来，然后再去调用里面的 `load()` 方法去加载一张图片，这样就会返回一个纹理对象，纹理对象可以作为模型材质颜色贴图 `map` 属性的值，材质的颜色贴图属性 `map` 设置后，模型会从纹理贴图上采集像素值。下面列出了几种常用的贴图类型以及加载贴图的基本流程。

- `map`：材质贴图
- `normalMap`：法线贴图
- `bumpMap`：凹凸贴图
- `envMap`：环境贴图
- `specularMap`：高光贴图
- `lightMap`：光照贴图

### 动画

- 基础动画
- 相机控制
- 变形动画
- 用骨骼和蒙皮制作动画
- 使用外部模型创建动画

## 模型光源结合生成明暗变化的创意页面-光与影之诗

### 渲染编码outputEncoding

`outputEncoding` 属性控制输出渲染编码。

- 默认情况下，值为 `THREE.LinearEncoding`，这种线性编码的缺点是看起来不够真实。
- 此时可以将值设置为 `THREE.sRGBEncoding` 提升渲染输出效果的真实性。
- 此外还有另一个可选属性值为 `THREE.GammaEncoding`，它是一种存储颜色的方法, 这种编码的优点在于它允使用一种表现像亮度的 `gammaFactor` 值，根据人眼的敏感度优化明暗值的存储方式。当使用 `sRGBEncoding` 时，其实就像使用默认 `gammaFactor` 值为 `2.2` 的 `GammaEncoding`。

### THREE.LoadingManager

其功能是处理并跟踪已加载和待处理的数据。如果未手动设置加强管理器，则会为加载器创建和使用默认全局实例加载器管理器。以下是加载器的基本使用方法：

```js
// 初始化加载器
const manager = new THREE.LoadingManager(); 

// 此函数在加载开始时被调用
manager.onStart = function ( url, itemsLoaded, itemsTotal ) {
  console.log( 'Started loading file: ' + url + '.\nLoaded ' + itemsLoaded + ' of ' + itemsTotal + ' files.' );
}; 

// 所有的项目加载完成后将调用此函数。默认情况下，该函数是未定义的，除非在构造函数中传入
manager.onLoad = function ( ) {
  console.log( 'Loading complete!');
}; 

// 此方法加载每一个项，加载完成时进行调用
manager.onProgress = function ( url, itemsLoaded, itemsTotal ) {
  console.log( 'Loading file: ' + url + '.\nLoaded ' + itemsLoaded + ' of ' + itemsTotal + ' files.' );
}; 

// 此方法将在任意项加载错误时，进行调用
manager.onError = function ( url ) {
  console.log( 'There was an error loading ' + url );
};
const loader = new THREE.OBJLoader( manager ); 

// 加载模型
loader.load('file.obj', function (object) {});
```

### 补间动画TWEEN

`Tween.js` 是附加在 `Three.js` 库中的一个扩充动画库，它可以平滑的修改元素的属性值，使一个对象在一定时间内从一个状态缓动变化到另外一个状态，配合动画函数实现丝滑的动画效果`TWEEN.js` 本质就是一系列缓动函数算法，结合`Canvas`、`Three.js` 很简单就能实现很多动画效果。示例中将多次使用它实现创意的动画效果。

**基本使用**：

```js
var tween = new TWEEN.Tween({x: 1})     // position: {x: 1}
.delay(100)                             // 等待100ms
.to({x: 200}, 1000)                     // 1s时间，x到200
.onUpdate(render)                       // 变更期间执行render方法
.onComplete(() => {})                   // 动画完成
.onStop(() => {})                       // 动画停止
.start();                               // 开启动画
```

要让动画真正动起来，需要在 `requestAnimationFrame` 中调用 `update` 方法。

```js
TWEEN.update()
```

### DRACOLoader

- `DRACOLoader` 是使用 `Draco` 库压缩的几何图形加载器。
- `Draco` 是一个开源库，用于压缩和解压缩 `3D` 网格和点云。压缩后的几何图形可以明显更小，代价是客户端设备上的额外解码时间。
- 独立的 `Draco` 文件具有 `.drc` 扩展名，并包含顶点位置、法线、颜色和其他属性。 `Draco` 文件不包含材质、纹理、动画或节点层次结构，要使用这些功能，需要将 `Draco` 几何图形嵌入到 `glTF` 文件中。 可以使用 `glTF-Pipeline` 将普通的 `glTF` 文件转换为 `Draco` 压缩的 `glTF` 文件。 当使用带有 `glTF` 的 `Draco` 时，`GLTFLoader` 将在内部使用 `DRACOLoader` 的实例。
- `DRACOLoader` 依赖于 `IE11` 不支持的 `ES6 Promises`，要在 `IE11` 中使用加载器，必须包含一个提供 `Promise` 替换的 `polyfill`。
- 开源 `3D` 建模工具 `Blender` 可以生成使用 `Draco` 压缩过的模型。

### THREE.Clock

本文中使用 `Clock` 提供的 `getElapsedTime` 方法来获取页面重绘两帧之间的时间间隔。 `Clock` 本质上就是对 `Date` 进行封装，提供了一些方法和属性，在 `Three.js` 使用过程中涉及到时间相关的方法时不用对 `Date` 进行封装，直接使用 `Clock` 提供的方法即可。在骨骼动画、变形动画、粒子动画等功能的开发中常常需要调用 `Clock` 的方法。

**两个常用方法**：

- `getElapsedTime()`：获取自时钟启动后的秒数，同时将 `.oldTime` 设置为当前时间。 如果 `.autoStart` 设置为 `true` 且时钟并未运行，则该方法同时启动时钟。
- `getDelta()`：获取自 `.oldTime` 设置后到当前的秒数。 同时将 `.oldTime` 设置为当前时间。 如果 `.autoStart` 设置为 `true` 且时钟并未运行，则该方法同时启动时钟。

### Intersection Observer

- 本文中使用 `Intersection Observer` 来辨识当前处于哪个页面以更新相机位置。
- `IntersectionObserver` 接口提供了一种异步观察目标元素与其祖先元素或顶级文档视窗 `viewport` 交叉状态的方法。
- 可以使用它来检测元素在页面上的可视状态或者两个元素之间的相对可视状态。应用这一特性可以用它来实现页面滚动加载、图片懒加载等功能。

## 粒子系统

### 精灵材质 THREE.SpriteMaterial

`THREE.SpriteMatrial` 对象的一些可修改属性及其说明。

- `color`：粒子的颜色。
- `map`：粒子所用的纹理，可以是一组 `sprite sheet`。
- `sizeAttenuation`：如果该属性设置为 `false`，那么距离摄像机的远近不影响粒子的大小，默认值为 `true`。
- `opacity`：该属性设置粒子的不透明度。默认值为 `1`，不透明。
- `blending`：该属性指定渲染粒子时所用的融合模式。
- `fog`：该属性决定粒子是否受场景中雾化效果影响。默认值为 `true`。

### 点材质 THREE.PointsMaterial

使用 `THREE.PointsMaterial` 来样式化粒子，它是 `THREE.Points` 使用的默认材质，下面列举了 `THREE.PointsMaterial` 中所有可设置属性及其说明。

- `color`: 粒子系统中所有粒子的颜色。将 `vertexColors` 属性设置为 `true`，并且通过颜色属性指定了几何体的颜色来覆盖该属性。默认值为 `0xFFFFFF`。
- `map`: 通过这个属性可以在粒子材质，比如可以使用 `canvas`、贴图等。
- `size`：该属性指定粒子的大小，默认值为 `1`。
- `sizeAnnutation`: 如果该属性设置为 `false`，那么所有的粒子都将拥有相同的尺寸，无论它们距离相机有多远。如果设置为 `true`，粒子的大小取决于其距离摄像机的距离的远近，默认值为`true`。
- `vertexColors`：通常 `THREE.Points` 中所有的粒子都拥有相同的颜色，如果该属性设置为 `THREE.VertexColors`，并且几何体的颜色数组也有值，那就会使用颜色数组中的值，默认值为 `THREE.NoColors`。
- `opacity`：该属性与 `transparent` 属性一起使用，用来设置粒子的不透明度。默认值为 `1`（完全不透明）。
- `transparent`：如果该属性设置为 `true`，那么粒子在渲染时会根据 `opacity` 属性的值来确定其透明度，默认值为 `false`。
- `blending`：该属性指定渲染粒子时的融合模式。
- `fog`：该属性决定粒子是否受场景中雾化效果影响，默认值为 `true`。

### dat.GUI

`dat.GUI` 是一个轻量级的 `JavaScript` 图形用户界面控制库，它可以轻松地即时操作变量和触发函数，通过设定好的控制器去快捷的修改设定的变量。下面是它的一些基本使用方法：

```js
// 初始化
const gui = new dat.GUI({ name: 'name'});
// 初始化控件属性
const ctrls = {
  name: 'acqui',
  speed: 0.5,
  color1: '#FF0000',
  color2: [0, 128, 255],
  color3: [0, 128, 255, 0.3],
  color4: { h: 350, s: 0.9, v: 0.3 },
  test: ',
  test2: ',
  cb: () => {},
  gender:true
};
// gui.add(控件对象变量名，对象属性名，其它参数)，控制字符类型或数字
gui.add(ctrls, 'name');
// 缩放区间[0,100],变化步长10
gui.add(ctrls, 'speed', 0, 100, 10);
// 创建一个下拉选择
gui.add(ctrls, 'test', { 低速: 0.005, 中速: 0.01, 高速: 0.1 }).name('转速');
gui.add(ctrls, 'test2', ['低速', '中速', '高速']).name('转速2');
//  创建按钮
gui.add(ctrls, 'cb').name('按钮');
gui.add(ctrls, 'gender').name('性别');
// 控制颜色属性
gui.addColor(ctrls, 'color1');
// 通过name可设置别名
gui.addColor(ctrls, 'color2').name('颜色2');
// 创建一个Folder
const folder = gui.addFolder('颜色组');
folder.addColor(ctrls, 'color3');
folder.addColor(ctrls, 'color4');
// 可以通过onChange方法来监听改变的值，从而修改样式
gui.addColor(ctrls, 'color2').onChange(callback);
```

### Canvas纹理CanvasTexture

用于从 `Canvas` 元素中创建纹理贴图。

**构造函数**:

```js
CanvasTexture(canvas: HTMLElement, mapping: Constant, wrapS: Constant, wrapT: Constant, magFilter: Constant, minFilter: Constant, format: Constant, type: Constant, anisotropy: Number )
```

- `canvas`：将会被用于加载纹理贴图的 `Canvas` 元素。
- `mapping`：纹理贴图将被如何应用到物体上。
- `wrapS`：默认值是 `THREE.ClampToEdgeWrapping`。
- `wrapT`：默认值是 `THREE.ClampToEdgeWrapping`。
- `magFilter`：当一个纹素覆盖大于一个像素时贴图将如何采样，默认值为 `THREE.LinearFilter`。
- `minFilter`：当一个纹素覆盖小于一个像素时贴图将如何采样，默认值为 `THREE.LinearMipmapLinearFilter`。
- `format`：在纹理贴图中使用的格式。
- `type`：默认值是 `THREE.UnsignedByteType`。
- `anisotropy`：沿着轴，通过具有最高纹素密度的像素的样本数。 默认情况下，这个值为 `1`。设置一个较高的值将会产生比基本的 `mipmap` 更清晰的效果，代价是需要使用更多纹理样本。

**属性和方法**：

- `.isCanvasTexture[Boolean]`：检查是否是 `CanvasTexture` 类型纹理的只读属性。
- `.needsUpdate[Boolean]`：是否需要更新，默认值为 `true`，以便使得 `Canvas`中的数据能够载入。
- 其他属性和方法继承于 `Texture`。

### 雾化效果Fog和FogExp2

为了增强场景的真实性，示例中使用了 `FogExp2` 雾化效果，那么 `THREE.Fog` 和 `THREE.FogExp2` 有什么不同呢？

- **雾Fog**
  - 定义：表示线性雾，雾的密度是随着距离线性增大的，即场景中物体雾化效果随着随距离线性变化。
  - 构造函数：`Fog(color, near, far)`
    - `.color`：表示雾的颜色，场景中远处物体为黑色，场景中最近处距离物体是自身颜色，最远和最近之间的物体颜色是物体本身颜色和雾颜色的混合效果。
    - `.near`：表示应用雾化效果的最小距离，距离活动摄像机长度小于 `.near` 的物体将不会被雾所影响。
    - `.far`： 表示应用雾化效果的最大距离，距离活动摄像机长度大于 `.far` 的物体将不会被雾所影响。
- **指数雾FogExp2**
  - 定义：表示指数雾，即雾的密度随着距离指数而增大。
  - 构造函数：`FogExp2(color, density)`
    - `.color`：表示雾的颜色。
    - `.density`：表示雾的密度的增速，默认值为 `0.00025`。

## 后期处理

### three.js 后期处理通道汇总

下面列举了 `Three.js` 中内置的一些后期处理通道，我们在开发完页面优化过程中，可以选择合适的通道给页面场景添加后期滤镜。它们的具体用法可以查看本文最后的**附录**，在附录中我总结汇总了它们的构造函数、参数含义、属性、示例等基本用法。不幸的是，`Three.js` 对大多数后期处理效果都没有给出详细的使用文档，如果想要完全了解它们，就必须仔细阅读[官网文档](https://threejs.org/examples/#webgl_animation_keyframes)提供的示例或者源码，可以在文档中通过 `postprocessing` 进行检索。

- `AdaptiveToneMappingPass`：该通道可以根据场景的光照度自动调节场景的亮度。
- `BloomPass`：该通道通过增强场景中明亮的区域来模拟真实世界中的摄像机。
- `BokehPass`：该通道可以实现类似大光圈镜头的景深效果。
- `ClearPass`：该通道清空当前纹理缓存。
- `CubeTexturePass`：用于渲染天空盒。
- `DotScreenPass`：将黑点图层应用于屏幕的原始图片上。
- `FilmPass`：通过扫描线和失真来模拟电视屏幕效果。
- `GlitchPass`：随机在屏幕上显示电脉冲。
- `HalftonePass`：用于模拟传统印刷术的半色调效果。
- `MaskPass`：在图片上显示掩码，后续通道只会影响到掩码区域。
- `OutlinePass`：勾勒出场景中的物体轮廓。
- `RenderPass`：在当前场景和摄像机的基础上渲染出一个新场景。
- `SAOPass`：实现实时环境光遮挡效果。
- `SMAAPass`：全屏反锯齿效果。
- `SSAARenderPass`：使用另一种算法实现全屏反锯齿效果。
- `SSAOPass`：使用另一种算法实现实时环境光遮挡效果
- `SavePass`：该通道执行时会赋值当前渲染结果，在后续步骤中可以使用。
- `ShaderPass`：自定义着色器通道，可以传入自定义着色器作为参数，以生成一个高级、自定义的后期处理通道。
- `TAARenderPass`：也是一个全屏反锯齿效果。
- `TexturePass`：将合成器的当前状态保存为纹理，然后将其作为参数传入到其他的 `EffectComposer` 组合器中。
- `UnrealBloomPass`：该通道与 `Bloom` 类似，但是它实现的效果更接近于虚幻引擎的 `Bloom` 效果。

## media

### AudioListener

**属性**：

- `.context[AudioContext]`：`listener` 构造函数中的 `AudioContext`。
- `.gain[GainNode]`：使用 `AudioContext.createGain()` 创建 `GainNode`。
- `.filter[AudioNode]`：默认为 `null`。
- `.timeDelta[Number]`：`audio` 实体的时间差值，默认是 `0`。

**方法**：

- `.getInput()`：返回 `gainNode`。
- `.removeFilter()`：设置 `filter` 属性为 `null`。
- `.getFilter()`：返回filter属性的值。
- `.setFilter(value: AudioNode)`：设置 `filter` 属性的值。
- `.getMasterVolume()`: 返回音量。
- `.setMasterVolume(value: Number)`：设置音量。
