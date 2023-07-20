# WebGLTextures

- [ ] 待补充方法？？

WebGLTextures.js 是 Three.js 源代码中的一个js文件，主要包括 WebGL 渲染器如何管理纹理的内容。它定义了纹理类型、创建和更新纹理的方法以及如何处理纹理的不同属性，例如纹理过滤、循环模式、偏移和旋转等。

该文件中定义了一些常量和纹理类型，如：

- THREE.UnsignedByteType

- THREE.FloatType

- THREE.HalfFloatType

还定义了一些纹理过滤的方法，如：

- THREE.NearestFilter

- THREE.LinearFilter

- THREE.NearestMipMapNearestFilter

- THREE.LinearMipMapNearestFilter

该文件还定义了一些纹理循环模式，如：

- THREE.RepeatWrapping

- THREE.ClampToEdgeWrapping

- THREE.MirroredRepeatWrapping

该文件中的代码提供了创建纹理和管理纹理属性的方法，并允许用户在运行时对纹理进行更改，以实现动态效果。总之，WebGLTextures.js 是 Three.js 源代码中关于纹理的核心部分，涵盖了纹理的各种属性和方法，是Three.js的重要组成部分。

```js
const isWebGL2 = capabilities.isWebGL2;
const maxTextures = capabilities.maxTextures;
const maxCubemapSize = capabilities.maxCubemapSize;
const maxTextureSize = capabilities.maxTextureSize;
const maxSamples = capabilities.maxSamples;
const multisampledRTTExt = extensions.has( 'WEBGL_multisampled_render_to_texture' ) ? extensions.get( 'WEBGL_multisampled_render_to_texture' ) : null;
const supportsInvalidateFramebuffer = typeof navigator === 'undefined' ? false : /OculusBrowser/g.test( navigator.userAgent );

const _videoTextures = new WeakMap();
let _canvas;

const _sources = new WeakMap(); // maps WebglTexture objects to instances of Source

// cordova iOS (as of 5.0) still uses UIWebView, which provides OffscreenCanvas,
// also OffscreenCanvas.getContext("webgl"), but not OffscreenCanvas.getContext("2d")!
// Some implementations may only implement OffscreenCanvas partially (e.g. lacking 2d).

let useOffscreenCanvas = false;

try {
    useOffscreenCanvas = typeof OffscreenCanvas !== 'undefined'
        // eslint-disable-next-line compat/compat
        && ( new OffscreenCanvas( 1, 1 ).getContext( '2d' ) ) !== null;
} catch ( err ) {
    // Ignore any errors
}
```

它还定义了一些变量，如：

- isWebGL：表示是否为WebGL2.0。

- maxTextures：表示WebGL渲染器支持的最大纹理数量。

- maxCubemapSize：表示WebGL渲染器支持的最大立方体纹理大小。

- maxTextureSize：表示WebGL渲染器支持的最大纹理大小。

- maxSamples：表示WebGL渲染器支持的最大多重采样数量。

- multisampledRTTExt：表示是否支持WebGL_multisanpled_render_to_texture扩展。

- supportsInvalidateFramebuffer：表示是否支持InvalidateFramebuffer。

变量_videoTextures和_sources分别用于存储视频纹理和源对象与WebglTexture对象之间的映射关系。

最后，代码还尝试了使用OffscreenCanvas，并通过检测是否能够获取2d上下文来确定是否可以使用

## createCanvas

## resizeImage

## textureNeedsPowerOfTwo

## textureNeedsGenerateMipmaps

## generateMipmap

## getInternalFormat

## getMipLevels

## filterFallback

## onTextureDispose

## onRenderTargetDispose

这是一个事件回调函数，当一个渲染目标的dispose事件被触发时会执行。事件目标，即触发该事件的渲染目标，会从事件目标上删除这个回调函数。接着，它会调用函数deallocateRenderTarget()来释放对渲染目标的资源的占用。

```js
function onRenderTargetDispose( event ) {
    const renderTarget = event.target;

    renderTarget.removeEventListener( 'dispose', onRenderTargetDispose );

    deallocateRenderTarget( renderTarget );
}
```

## deallocateTexture

这是一段在Three.js WebGLTexture.js文件中的代码，主要功能是释放一个纹理的WebGL资源。

函数接收一个纹理作为参数，然后在内部通过一个全局的属性映射（properties）获取纹理的WebGL属性，如果该纹理的WebGL属性未初始化（即没有分配WebGL资源），函数直接返回。

然后通过纹理的源（source）和另一个全局的属性映射（_sources），取得该纹理对应的WebGL纹理对象，该WebGL纹理对象的使用次数减1.如果该WebGL纹理对象的使用次数为0，说明该WebGL纹理对象不再使用，则调用删除纹理的函数删除该WebGL纹理对象。如果该纹理对象的源不再使用任何WebGL纹理，则从_sources中删除该源对应的键值对。

最后，从属性映射中删除该纹理。

```js
function deallocateTexture( texture ) {
    const textureProperties = properties.get( texture );

    if ( textureProperties.__webglInit === undefined ) return;

    // check if it's necessary to remove the WebGLTexture object

    const source = texture.source;
    const webglTextures = _sources.get( source );

    if ( webglTextures ) {
        const webglTexture = webglTextures[ textureProperties.__cacheKey ];
        webglTexture.usedTimes --;

        // the WebGLTexture object is not used anymore, remove it

        if ( webglTexture.usedTimes === 0 ) {
            deleteTexture( texture );
        }

        // remove the weak map entry if no WebGLTexture uses the source anymore

        if ( Object.keys( webglTextures ).length === 0 ) {
            _sources.delete( source );
        }
    }

    properties.remove( texture );
}
```

## deleteTexture

这个函数实现了删除纹理的功能。

首先，它获取纹理对应的纹理属性，并且判断是否已经初始化过，如果没有初始化，就直接返回。

然后，它获取纹理的来源（source）以及与该来源相关的WebGL纹理，并将使用次数减1。当一个WebGL纹理的使用次数变为0时，它就可以被删除了。

最后，该函数删除了WebGL纹理对象，并从纹理来源的WebGL纹理映射中删除了该纹理。同时内存中的纹理数量也减少了1。

```js
function deleteTexture( texture ) {
    const textureProperties = properties.get( texture );
    _gl.deleteTexture( textureProperties.__webglTexture );

    const source = texture.source;
    const webglTextures = _sources.get( source );
    delete webglTextures[ textureProperties.__cacheKey ];

    info.memory.textures --;
}
```

## deallocateRenderTarget

这个函数主要负责释放一个renderTarget对象所占用的WenGL资源，以便管理内存。它首先通过renderTarget的纹理texture对象获取renderTarget对象的属性信息以及texture对象的属性信息，然后判断是否已经创建了WebGLTexture，如果有，则删除。随后判断renderTarget是否有depthTexture，如果有则释放depthTexture。接下来判断是否是一个立方体渲染目标，如果是，则需要删除6个framebuffer和对应的depthbuffer；如果不是，则只需要删除一个framebuffer，对应的depthbuffer以及多重采样的framebuffer。最后判断renderTarget是否是一个多目标渲染目标，如果是，则需要删除所有的附件纹理，最后释放掉renderTarget和纹理对象占用的内存。

```js
function deallocateRenderTarget( renderTarget ) {
    const texture = renderTarget.texture;

    const renderTargetProperties = properties.get( renderTarget );
    const textureProperties = properties.get( texture );

    if ( textureProperties.__webglTexture !== undefined ) {
        _gl.deleteTexture( textureProperties.__webglTexture );

        info.memory.textures --;
    }

    if ( renderTarget.depthTexture ) {
        renderTarget.depthTexture.dispose();
    }

    if ( renderTarget.isWebGLCubeRenderTarget ) {
        for ( let i = 0; i < 6; i ++ ) {
            _gl.deleteFramebuffer( renderTargetProperties.__webglFramebuffer[ i ] );
            if ( renderTargetProperties.__webglDepthbuffer ) _gl.deleteRenderbuffer( renderTargetProperties.__webglDepthbuffer[ i ] );
        }
    } 
    else {
        _gl.deleteFramebuffer( renderTargetProperties.__webglFramebuffer );
        if ( renderTargetProperties.__webglDepthbuffer ) _gl.deleteRenderbuffer( renderTargetProperties.__webglDepthbuffer );
        if ( renderTargetProperties.__webglMultisampledFramebuffer ) _gl.deleteFramebuffer( renderTargetProperties.__webglMultisampledFramebuffer );

        if ( renderTargetProperties.__webglColorRenderbuffer ) {
            for ( let i = 0; i < renderTargetProperties.__webglColorRenderbuffer.length; i ++ ) {
                if ( renderTargetProperties.__webglColorRenderbuffer[ i ] ) _gl.deleteRenderbuffer( renderTargetProperties.__webglColorRenderbuffer[ i ] );
            }
        }

        if ( renderTargetProperties.__webglDepthRenderbuffer ) _gl.deleteRenderbuffer( renderTargetProperties.__webglDepthRenderbuffer );
    }

    if ( renderTarget.isWebGLMultipleRenderTargets ) {
        for ( let i = 0, il = texture.length; i < il; i ++ ) {
            const attachmentProperties = properties.get( texture[ i ] );

            if ( attachmentProperties.__webglTexture ) {
                _gl.deleteTexture( attachmentProperties.__webglTexture );

                info.memory.textures --;
            }

            properties.remove( texture[ i ] );
        }
    }

    properties.remove( texture );
    properties.remove( renderTarget );
}
```

## allocateTextureUnit

这是Three.js 中 WebGLTexture.js 文件中的一个函数，该函数的作用是分配一个纹理单元。

在GPU中，一个纹理单元代表一个可以绑定纹理对象的空间。在Three.js中，该函数的目的是给每一个纹理分配一个不同的纹理单元。

该函数中定义了一个变量textureUnit，它代表当前可以分配的纹理单元。然后，通过一个判断语句判断是否已经超出GPU支持的最大纹理数。如果超出，则在控制台输出警告。

最后，纹理单元计数器textureUnits自增，并返回当前分配的纹理单元编号。

```js
function allocateTextureUnit() {
    const textureUnit = textureUnits;

    if ( textureUnit >= maxTextures ) {

        console.warn( 'THREE.WebGLTextures: Trying to use ' + textureUnit + ' texture units while this GPU supports only ' + maxTextures );

    }

    textureUnits += 1;

    return textureUnit;
}
```

## getTextureCacheKey

### setTexture2D

## setTexture2DArray

## setTexture3D

## setTextureCube

## wrappingToGL

## setTextureParaneters

## initTexture

## uploadTexture

## uploadCubeTexture

## setupFrameBufferTexture

## setupRenderBufferStorage
