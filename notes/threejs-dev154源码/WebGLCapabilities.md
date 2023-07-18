# WebGLCapabilities

threejs中的 `WebGLCapabilities`  文件主要处理管理和存储 `WebGL`渲染器的能力。它会检查渲染器的能力（例如，最多可以使用的灯光数量，纹理数量等），并基于这些能力设置一些默认值和属性。它还提供了检查某些功能是否受支持（例如：模板纹理）并且可以使用的方法。此文件中存储的信息在整个渲染器中使用，以决定可以使用可用硬件呈现什么内容和不能呈现什么内容。

## getMaxAnisotropy

```js
// 实现获取最大各向异性纹理程度的函数
function getMaxAnisotropy() {
    // 判断变量 maxAnisotropy 是否已经定义，如果定义就直接返回它
    if ( maxAnisotropy !== undefined ) return maxAnisotropy;

    // 判断 WebGL 环境是否支持各向异性纹理扩展（EXT_texture_filter_anisotropic）
    if ( extensions.has( 'EXT_texture_filter_anisotropic' ) === true ) {
        // 获取各向异性纹理扩展
        const extension = extensions.get( 'EXT_texture_filter_anisotropic' );

        // 使用 WebGL API gl.getParameter 获取各向异性纹理的最大值，并存储在变量 maxAnisotropy 中
        maxAnisotropy = gl.getParameter( extension.MAX_TEXTURE_MAX_ANISOTROPY_EXT );
    }
    else {
        // maxAnisotropy 置为 0
        maxAnisotropy = 0;
    }

    // 返回 maxAnisotropy
    return maxAnisotropy;
}
```

`EXT_texture_filter_anisotropic`扩展是 WebGL 中一个支持高质量纹理的扩展。它允许应用程序指定纹理的各向异性级别，以便纹理在模糊时仍然保持高质量。

在图形学中，各向异性指的是纹理的不均匀性，它可以用来描述纹理的分辨率和清晰度的不同。在没有各向异性的情况下，纹理会在模糊时变得很不清晰。使用各向异性可以使纹理在模糊时仍然保持高质量。

`EXT_texture_filter_anisotropic` 扩展的作用是提供一个机制来改善纹理过滤的质量，从而使图形看起来更加清晰。如果 WebGL 环境支持这个扩展，应用程序可以使用它来指定纹理的各向异性级别，从而提高图形的质量。

## getMaxPrecisio

```js
// 检测 webGL 环境中顶点和片段着色器的精度，并返回最高的可用精度
function getMaxPrecision(precision) {
    // 如果传入的精度 highp
    if ( precision === 'highp' ) {
        // 使用 gl.getShaderPrecisionFormat 方法检测顶点着色器和片元着色器是否支持高精度
        if (gl.getShaderPrecisionFormat( gl.VERTEX_SHADER, gl.HIGH_FLOAT ).precision > 0 &&
            gl.getShaderPrecisionFormat( gl.FRAGMENT_SHADER, gl.HIGH_FLOAT ).precision > 0) {
            // 返回高精度
            return 'highp';

        }

        // 将精度置为中等精度
        precision = 'mediump';
    }

    // 再次检测顶点着色器和片元着色器是否支持中等精度
    if ( precision === 'mediump' ) {
        if ( gl.getShaderPrecisionFormat( gl.VERTEX_SHADER, gl.MEDIUM_FLOAT ).precision > 0 &&
            gl.getShaderPrecisionFormat( gl.FRAGMENT_SHADER, gl.MEDIUM_FLOAT ).precision > 0 ) {

            // 返回中等精度
            return 'mediump';
        }

    }

    // 返回低精度
    return 'lowp';
}
```

> 这段代码的作用是确保应用程序使用 WebGL 环境中最高的可用精度来渲染图形。

## 检测 WebGL 环境的性能和特性，并初始化一些性能相关的参数

```js
const isWebGL2 = typeof WebGL2RenderingContext !== 'undefined' && gl.constructor.name === 'WebGL2RenderingContext';

let precision = parameters.precision !== undefined ? parameters.precision : 'highp';
const maxPrecision = getMaxPrecision( precision );

if ( maxPrecision !== precision ) {
    console.warn( 'THREE.WebGLRenderer:', precision, 'not supported, using', maxPrecision, 'instead.' );
    precision = maxPrecision;
}

const drawBuffers = isWebGL2 || extensions.has( 'WEBGL_draw_buffers' );

const logarithmicDepthBuffer = parameters.logarithmicDepthBuffer === true;

const maxTextures = gl.getParameter( gl.MAX_TEXTURE_IMAGE_UNITS );
const maxVertexTextures = gl.getParameter( gl.MAX_VERTEX_TEXTURE_IMAGE_UNITS );
const maxTextureSize = gl.getParameter( gl.MAX_TEXTURE_SIZE );
const maxCubemapSize = gl.getParameter( gl.MAX_CUBE_MAP_TEXTURE_SIZE );

const maxAttributes = gl.getParameter( gl.MAX_VERTEX_ATTRIBS );
const maxVertexUniforms = gl.getParameter( gl.MAX_VERTEX_UNIFORM_VECTORS );
const maxVaryings = gl.getParameter( gl.MAX_VARYING_VECTORS );
const maxFragmentUniforms = gl.getParameter( gl.MAX_FRAGMENT_UNIFORM_VECTORS );

const vertexTextures = maxVertexTextures > 0;
const floatFragmentTextures = isWebGL2 || extensions.has( 'OES_texture_float' );
const floatVertexTextures = vertexTextures && floatFragmentTextures;

const maxSamples = isWebGL2 ? gl.getParameter( gl.MAX_SAMPLES ) : 0;

return {
    isWebGL2: isWebGL2,

    drawBuffers: drawBuffers,

    getMaxAnisotropy: getMaxAnisotropy,
    getMaxPrecision: getMaxPrecision,

    precision: precision,
    logarithmicDepthBuffer: logarithmicDepthBuffer,

    maxTextures: maxTextures,
    maxVertexTextures: maxVertexTextures,
    maxTextureSize: maxTextureSize,
    maxCubemapSize: maxCubemapSize,

    maxAttributes: maxAttributes,
    maxVertexUniforms: maxVertexUniforms,
    maxVaryings: maxVaryings,
    maxFragmentUniforms: maxFragmentUniforms,

    vertexTextures: vertexTextures,
    floatFragmentTextures: floatFragmentTextures,
    floatVertexTextures: floatVertexTextures,

    maxSamples: maxSamples,
};
```
