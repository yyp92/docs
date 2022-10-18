# 前端实现VR全景看房的方案

## 前言

![](../../\imgs\vr-1.png)

![](../../\imgs\vr-2.gif)

## 方案一：WebGL3D引擎

使用3D引擎先搭一个基本的3D场景，下面的演示使用three.js，同类的3D引擎我还调研过babylon.js，playcanvas，使用都差不太多，学会一个基本都通的

```js
var scene, camera, renderer;

function initThree(){
    // 场景
    scene = new THREE.Scene();
    // 镜头
    camera = new THREE.PerspectiveCamera(90, document.body.clientWidth / document.body.clientHeight, 0.1, 100);
    camera.position.set(0, 0, 0.01);
    // 渲染器
    renderer = new THREE.WebGLRenderer();
    renderer.setSize(document.body.clientWidth, document.body.clientHeight);
    document.getElementById("container").appendChild(renderer.domElement);
    // 镜头控制器
    var controls = new THREE.OrbitControls(camera, renderer.domElement);

    // 一会儿在这里添加3D物体

    loop();
}

// 帧同步重绘
function loop() {
    requestAnimationFrame(loop);
    renderer.render(scene, camera);
}

window.onload = initThree;
```

现在我们能看到一个黑乎乎的世界，因为现在`scene`里什么都没有，接着我们要把三维物体放进去了，使用3D引擎的实现方式无非都是以下几种

### 使用立方体（box）实现

> 这种方式最容易理解，我们在一个房间里，看向天花板，地面，正面，左右两面，背面共计六面。我们把所有六个视角拍成照片就得到下面六张图

![](../../\imgs\vr-3.png)

现在我们直接使用立方体（box）搭出这样一个房间

```js
var materials = [];  
// 根据左右上下前后的顺序构建六个面的材质集  
var texture_left = new THREE.TextureLoader().load( './images/scene_left.jpeg' );  
materials.push( new THREE.MeshBasicMaterial( { map: texture_left} ) );

var texture_right = new THREE.TextureLoader().load( './images/scene_right.jpeg' );  
materials.push( new THREE.MeshBasicMaterial( { map: texture_right} ) );

var texture_top = new THREE.TextureLoader().load( './images/scene_top.jpeg' );  
materials.push( new THREE.MeshBasicMaterial( { map: texture_top} ) );

var texture_bottom = new THREE.TextureLoader().load( './images/scene_bottom.jpeg' );  
materials.push( new THREE.MeshBasicMaterial( { map: texture_bottom} ) );

var texture_front = new THREE.TextureLoader().load( './images/scene_front.jpeg' );  
materials.push( new THREE.MeshBasicMaterial( { map: texture_front} ) );

var texture_back = new THREE.TextureLoader().load( './images/scene_back.jpeg' );  
materials.push( new THREE.MeshBasicMaterial( { map: texture_back} ) );

var box = new THREE.Mesh( new THREE.BoxGeometry( 1, 1, 1 ), materials );  
scene.add(box);
```

![](../../\imgs\vr-4.gif)

好，现在我们把镜头camera（也就是人的视角），放到box内，并且让所有贴图向内翻转后，VR全景就实现了。

```js
box.geometry.scale( 1, 1, -1 );
```

**现在我们进入了这个盒子！！**

![](../../\imgs\vr-5.gif)

threejs官方立方体全景示例

### 使用球体（sphere）实现

> 我们将房间360度球形范围内所有的光捕捉到一个图片上，再将这张图片展开为矩形，就能得到这样一张全景图片

![](../../\imgs\vr-6.png)

```js
// 节点数量越大，需要计算的三角形就越多，影响性能
var sphereGeometry = new THREE.SphereGeometry(/*半径*/1, /*垂直节点数量*/50, /*水平节点数量*/50);

var sphere = new THREE.Mesh(sphereGeometry);
// 用线框模式大家可以看得清楚是个球体而不是圆形
sphere.material.wireframe  = true; 
scene.add(sphere);
```

![](../../\imgs\vr-7.png)

现在我们把这个全景图片贴到这个球体上

```js
var texture = new THREE.TextureLoader().load('./images/scene.jpeg');
var sphereMaterial = new THREE.MeshBasicMaterial({map: texture});

var sphere = new THREE.Mesh(sphereGeometry,sphereMaterial);
// sphere.material.wireframe  = true;
```

![](../../\imgs\vr-8.gif)

和之前一样，我们把镜头camera（也就是人的视角），放到球体内，并且让所有贴图向内翻转后，VR全景就实现了

**现在我们进入了这个球体！！**

```js
var sphereGeometry = new THREE.SphereGeometry(/*半径*/1, 50, 50);
sphereGeometry.scale(1, 1, -1);
```

![](../../\imgs\vr-9.gif)

threejs官方球体全景示例

### 添加信息点

> 在VR全景中，我们需要放置一些信息点，用户点击之后做一些动作。

现在我们建立这样一个点的数组

```js
var hotPoints=[
    {
        position:{
            x:0,
            y:0,
            z:-0.2
        },
        detail:{
            "title":"信息点1"
        }
    },
    {
        position:{
            x:-0.2,
            y:-0.05,
            z:0.2
        },
        detail:{
            "title":"信息点2"
        }
    }
];
```

遍历这个数组，并将信息点的指示图添加到3D场景中

```js
var pointTexture = new THREE.TextureLoader().load('images/hot.png');
var material = new THREE.SpriteMaterial( { map: pointTexture} );

for(var i = 0; i < hotPoints.length; i++){
    var sprite = new THREE.Sprite( material );
    sprite.scale.set( 0.1, 0.1, 0.1 );
    sprite.position.set( hotPoints[i].position.x, hotPoints[i].position.y, hotPoints[i].position.z );

   scene.add( sprite );
}
```

**看到HOT指示图了吗？**

![](../../\imgs\vr-10.gif)

添加点击事件，首先将全部的sprite放到一个数组里

```js
sprite.detail = hotPoints[i].detail;
poiObjects.push(sprite);
```

然后我们通过射线检测（raycast），就像是镜头中心向鼠标所点击的方向发射出一颗子弹，去检查这个子弹最终会打中哪些物体。

![](../../\imgs\vr-11.gif)

```js
document.querySelector("#container").addEventListener("click",function(event){
    event.preventDefault();

    var raycaster = new THREE.Raycaster();
    var mouse = new THREE.Vector2();

    mouse.x = ( event.clientX / document.body.clientWidth ) * 2 - 1;
    mouse.y = - ( event.clientY / document.body.clientHeight ) * 2 + 1;

    raycaster.setFromCamera( mouse, camera );

    var intersects = raycaster.intersectObjects( poiObjects );
    if (intersects.length > 0) {
        alert("点击了热点"+intersects[0].object.detail.title);
    }
});
```

## 方案二：CSS3D

`threejs`等3d引擎太强大了，这些引擎的代码量都有大几百K，在今天的网速下显得无所谓，但在几年前我接到需求时仍然是重要的考量因素。既然我们只用到3D引擎的一点点功能，那么能否找到一个更加轻量的3D引擎呢。

有！css3d-engine，这个3d引擎只有`14kb`，并且在多个大牌商业项目中应用

- 淘宝造物节 https://shrek.imdevsh.com/show/zwj/

- adidas绝不凋谢 https://shrek.imdevsh.com/show/drose/

- adidas胜势全开 https://shrek.imdevsh.com/show/bbcny/

- adidas绝不跟随 https://shrek.imdevsh.com/show/crazylight/

### 使用skybox实现

```js
window.onload=initCSS3D;

function initCSS3D(){
    var s = new C3D.Stage();
    s.size(window.innerWidth, window.innerHeight).update();
    document.getElementById('container').appendChild(s.el);

    var box = new C3D.Skybox();
    box.size(954).position(0, 0, 0).material({
        front: {image: "images/scene_front.jpeg"},
        back: {image: "images/scene_back.jpeg"},
        left: {image: "images/scene_right.jpeg"},
        right: {image: "images/scene_left.jpeg"},
        up: {image: "images/scene_top.jpeg"},
        down: {image: "images/scene_bottom.jpeg"},

    }).update();
    s.addChild(box);

    function loop() {
        angleX += (curMouseX - lastMouseX + lastAngleX - angleX) * 0.3;
        angleY += (curMouseY - lastMouseY + lastAngleY - angleY) * 0.3;

        s.camera.rotation(angleY, -angleX, 0).updateT();
        requestAnimationFrame(loop);
    }

    loop();

    var lastMouseX = 0;
    var lastMouseY = 0;
    var curMouseX = 0;
    var curMouseY = 0;
    var lastAngleX = 0;
    var lastAngleY = 0;
    var angleX = 0;
    var angleY = 0;

    document.addEventListener("mousedown", mouseDownHandler);
    document.addEventListener("mouseup", mouseUpHandler);

    function mouseDownHandler(evt) {
        lastMouseX = curMouseX = evt.pageX;
        lastMouseY = curMouseY = evt.pageY;
        lastAngleX = angleX;
        lastAngleY = angleY;

        document.addEventListener("mousemove", mouseMoveHandler);
    }

    function mouseMoveHandler(evt) {
        curMouseX = evt.pageX;
        curMouseY = evt.pageY;
    }

    function mouseUpHandler(evt) {
        curMouseX = evt.pageX;
        curMouseY = evt.pageY;

        document.removeEventListener("mousemove", mouseMoveHandler);
    }
}
```

方案二的好处除了库很小以外，还是div+css来搭建三维场景的。但这个库的作者几乎不维护，遇到问题必须得自己想办法解决，比如使用在电脑上会看到明显的面片边缘

![](../../\imgs\vr-12.png)

但是在手机上浏览的话表现还是相当完美的

![](../../\imgs\vr-13.gif)

### 添加信息点

我们继续为它添加可交互的信息点

```js
var hotPoints=[
    {
        position:{
            x:0,
            y:0,
            z:-476
        },
        detail:{
            "title":"信息点1"
        }
    },
    {
        position:{
            x:0,
            y:0,
            z:476
        },
        detail:{
            "title":"信息点2"
        }
    }
];
```

```js
function initPoints(){
    var poiObjects = [];
    for (var i = 0; i < hotPoints.length; i++) {
        var _p = new C3D.Plane();

        _p.size(207, 162).position(hotPoints[i].position.x, hotPoints[i].position.y,hotPoints[i].position.z).material({
            image: "images/hot.png",
            repeat: 'no-repeat',
            bothsides: true,//注意这个两面贴图的属性
        }).update();
        s.addChild(_p);

        _p.el.detail = hotPoints[i].detail;

        _p.on("click", function(e){
            console.log(e.target.detail.title);
        })
    }
}
```

这样就可以显示信息点了，并且由于是div，我们非常容易添加鼠标点击交互等效果

![](../../\imgs\vr-14.png)

不过，`bothsides`属性为true时，背面的信息点图片是反的。

![](../../\imgs\vr-15.png)

所以我们这里要做一点处理，根据其与相机的夹角重置一下信息点的旋转角度。（`如果是那种怎么旋转都无所谓的图片，比如圆点则无需处理`）

```js
var r = Math.atan2(hotPoints[i].position.z-0,0-0) * 180 / Math.PI+90;
_p.size(207, 162).position(hotPoints[i].position.x,hotPoints[i].position.y,hotPoints[i].position.z).material({
    image: "images/hot.png",
    repeat: 'no-repeat',
    bothsides: false,
}).update();
```

### 需求升级了！

以上两个方案，我以为可以给客户交差了。但客户又提出了一些想法

- **全景图质量需要更高，但加载速度不允许更慢**

- **每个场景的信息点挺多的，坐标编辑太麻烦了**

![](../../\imgs\vr-16.gif)

## 方案三：pano2vr

![](../../\imgs\vr-17.png)

pano2vr是一款所见即所得的全景VR制作软件（正版149欧元），功能挺强大的，可以直接输出成HTML5静态网页，体验非常不错。

而其核心库`pano2vr_player.js`代码量也只有`238kb`。

![](../../\imgs\vr-18.png)

我们可以直接使用这个软件来可视化的添加信息点，输出成HTML5后，除了静态图片以外，所有配置信息都在这个`pano.xml`文件里

![](../../\imgs\vr-19.png)

### 修改信息点图片

整体的交互体验都非常好，但默认的信息点样式不喜欢，我们可以通过下面的代码来修改信息点图片

```js

```
