# WebGLRenderer

## 创建canvas

```js
// 命名空间
// createElementNS
var canvas = document.createElementNS('http://www.w3.org/1999/xhtml', 'canvas')

// createElement
var canvas = document.createElement('canvas')
```

## getCantext

```js
// 该函数试图从 canvas 元素中获取 webGL 上下文
function getContext( contextNames, contextAttributes ) {
    // 循环枚举上下文名称数组中的每个元素
    for ( let i = 0; i < contextNames.length; i ++ ) {
        // 获取当前上下文名称
        const contextName = contextNames[ i ];

        // 尝试使用当前上下文名称获取 webGL 上下文
        const context = canvas.getContext( contextName, contextAttributes );

        // 如果上下文不为 null，则直接返回该上下文
        if ( context !== null ) return context;
    }

    // 如果循环结束后仍未获取到上下文，则返回 null
    return null;
}
```

## contextAttributes

```js
// 标记是否是isWebGLRenderer 
this.isWebGLRenderer = true

const contextAttributes = {
    // 透明度
    alpha: true,
    // 是否启用深度缓存
    depth,
    // 是否启用模板缓冲
    stencil,
    // 抗锯齿
    antialias,
    // 像素数据是否预先乘以alpha，提高半透明图像的渲染效率
    premultipliedAlpha,
    // 是否渲染完成之后保留渲染缓冲
    preserveDrawingBuffer,
    // 性能设置
    powerPreference,
    // 检测性能是否支持    
    failIfMajorPerformanceCaveat,
};

// 设置 canvas 属性做一个标识
canvas.setAttribute( 'data-engine', `three.js r${REVISION}` );
```

> powerPreference
> 
> - default：代表渲染过程可以使用默认的系统配置，由系统决定如何使用性能
> 
> - high-performance：渲染过程需要尽量多的性能
> 
> - low-power：渲染过程需要尽量少的性能

## getShaderPrecisionForma

```js
// Some experimental-webgl implementations do not have getShaderPrecisionFormat

if ( _gl.getShaderPrecisionFormat === undefined ) {

    _gl.getShaderPrecisionFormat = function () {

        return { 'rangeMin': 1, 'rangeMax': 1, 'precision': 1 };

    };

}
```

这段代码是用于判断 WebGL 的实现是否有 getShaderPrecisionForma 方法，如果没有改方法，就为其赋予一个默认的返回值，返回的是一个对象，其中包含 rangeMin 和 rangeMax 属性，都是1，代表最小范围和最大范围，以及一个 precision 属性，也是 1 ，代表精度。这段代码的作用是为了兼容某些不支持 getShaderPrecisionForma 方法的 WebGL 实现。

## initGLContext

```js
function initGLContext() {
    extensions = new WebGLExtensions( _gl );

    capabilities = new WebGLCapabilities( _gl, extensions, parameters );

    extensions.init( capabilities );

    utils = new WebGLUtils( _gl, extensions, capabilities );

    state = new WebGLState( _gl, extensions, capabilities );

    info = new WebGLInfo( _gl );
    properties = new WebGLProperties();
    textures = new WebGLTextures( _gl, extensions, state, properties, capabilities, utils, info );
    cubemaps = new WebGLCubeMaps( _this );
    cubeuvmaps = new WebGLCubeUVMaps( _this );
    attributes = new WebGLAttributes( _gl, capabilities );
    bindingStates = new WebGLBindingStates( _gl, extensions, attributes, capabilities );
    geometries = new WebGLGeometries( _gl, attributes, info, bindingStates );
    objects = new WebGLObjects( _gl, geometries, attributes, info );
    morphtargets = new WebGLMorphtargets( _gl, capabilities, textures );
    clipping = new WebGLClipping( properties );
    programCache = new WebGLPrograms( _this, cubemaps, cubeuvmaps, extensions, capabilities, bindingStates, clipping );
    materials = new WebGLMaterials( _this, properties );
    renderLists = new WebGLRenderLists();
    renderStates = new WebGLRenderStates( extensions, capabilities );
    background = new WebGLBackground( _this, cubemaps, cubeuvmaps, state, objects, _alpha, premultipliedAlpha );
    shadowMap = new WebGLShadowMap( _this, objects, capabilities );
    uniformsGroups = new WebGLUniformsGroups( _gl, info, capabilities, state );

    bufferRenderer = new WebGLBufferRenderer( _gl, extensions, info, capabilities );
    indexedBufferRenderer = new WebGLIndexedBufferRenderer( _gl, extensions, info, capabilities );

    info.programs = programCache.programs;

    _this.capabilities = capabilities;
    _this.extensions = extensions;
    _this.properties = properties;
    _this.renderLists = renderLists;
    _this.shadowMap = shadowMap;
    _this.state = state;
    _this.info = info;
}
```

本段代码是实现初始化 WebGL 上下文所必须的代码，包含了创建和初始化一系列 WebGL 功能对象的操作。这些功能对象分别用于处理 WebGL 缓冲、访问扩展、处理WebGL状态、信息、属性、纹理、立方体贴图、顶点属性、顶点绑定状态、几何、对象、形变目标、剪切、程序缓存、材质、渲染列表、渲染状态、背景、阴影图以及 uniform 分组等。

最后还创建了两个 WebGL 缓冲渲染器：bufferRenderer 和 indexedBufferRenderer，分别用于管理 WebGL 缓冲。

最后将这些功能对象绑定到了 WebGLRenderer 对象上，使得其他模块可以使用。

WebGL 缓冲渲染器是指在 WebGL 绘图中用于管理缓冲数据的模块。

`BufferRenderer` 管理的是单纯的缓冲数据，也就是在 WebGL 中绘制几何图形是需要使用的顶点坐标、颜色等数据。

`IndexedBufferRenderer` 管理的是索引缓冲数据，它和 BufferRenderer 的区别在于，索引缓冲数据不仅包含顶点坐标等数据，还包含一个顶点索引数组，这个数组用于指定顶点之间的关系，使得 WebGL 知道如何绘制几何图形。

总而言之， `BufferRenderer` 和 `IndexedBufferRenderer` 的主要区别在于它们管理的缓冲数据是否带有顶点索引。
