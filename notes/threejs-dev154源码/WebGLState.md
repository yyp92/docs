# WebGLState

- [ ] 每个方法具体是做什么的待补充？

## 前言

`WebGLState.js` 是一个Three.js的模块，主要负责维护 WebGL 上下文的状态。它通过封装 WebGL 上下文中的一系列函数，提供了更方便、可靠和高效的方法来管理 WebGL 状态。该文中涉及到的功能包括：

- 维护 WebGL 状态的变量（如激活的纹理单元，混合模式，深度测试等）

- 管理着色器程序

- 启用/禁用 WebGL 上下文的功能（如混合、深度测试等）

- 配置 WebGL 的帧缓冲区

- 设置 WebGL 的渲染和裁剪视图

- 更改 WebGL 的渲染状态（如纹理单元，着色器程序等）

总之，`WebGLState.js` 文件是管理 WebGL 状态的关键模块，用于维护在渲染 Three.js 场景时使用的 WebGL 上下文的状态。

### 剪切

剪切（Clipping）是指在三维空间中限制几何图形显示的过程，只显示符合特定条件的部分。

在 WebGL 中，剪切功能可以用来限制三维场景中物体的显示范围。例如：您可以定义一个剪切区域，使得在该区域之外的物体不会被渲染。这可以用来实现视锥体的裁剪，从而提高渲染性能。

另外，剪切也可以用来实现特殊的效果，例如穿过物体的视角效果。在这种情况下，您可以通过定义剪切平面来削减物体的一部分，从而使得用户能够看到物体内部的内容。

### 混合

混合（Blending）是指在 WebGL 中合并两个图像的过程。它通过计算两个图像的像素值，生成一个新的合成图像。

混合功能在 WebGL 中有很多用途，其中最常见的是实现半透明效果。例如，您可以使用混合，让一个图像透过另一个图像，生成一个半透明效果。

此外，混合也可以用于实现许多其他效果。如火焰效果，烟雾效果等。例如，您可以使用混合，生成一个有火焰效果的图像，该图像由一个图像合成为火焰，并通过另一个图像产生透明效果。

## ColorBuffer

## DepthBuffer

## StencilBuffer

## createTexture

## bindFramebuffer

## drawBuffers

## equationToGL

## factorToGL

## setBlending

## setMaterial

## setFlipSided

## setCullFace

## setLineWidth

```js
function setLineWidth( width ) {
    if ( width !== currentLineWidth ) {
        if ( lineWidthAvailable ) gl.lineWidth( width );

        currentLineWidth = width;
    }
}
```

这段代码实现了设置折线宽度的功能，通过使用 WebGL 的 `gl.lineWidth()` 函数来设置线宽。

如果线宽已经是当前宽度，则不执行任何操作。

如果折线宽度变化。则使用  `gl.lineWidth()` 函数设置折线宽度。

当浏览器不支持 gl.lineWidth() 函数时，变量 lineWidthAvailable 为 false。此时不会执行任何操作。

## setPolygonOffset

```js
function setPolygonOffset( polygonOffset, factor, units ) {
    if ( polygonOffset ) {
        enable( gl.POLYGON_OFFSET_FILL );

        if ( currentPolygonOffsetFactor !== factor || currentPolygonOffsetUnits !== units ) {

            gl.polygonOffset( factor, units );

            currentPolygonOffsetFactor = factor;
            currentPolygonOffsetUnits = units;

        }
    }
    else {
        disable( gl.POLYGON_OFFSET_FILL );
    }
}
```

这是一个设置多边形偏移量的函数，当 polygonOffset 设置为 true 时，它会启用多边形偏移，并使用 factor 和 units 作为当前多边形偏移因子和单位。如果 polygonOffset 设置为 false，则禁用多边形偏移。当前多边形偏移因子和单位是在多边形偏移启用时才会被设置的。

## setScissorTest

```js
function setScissorTest( scissorTest ) {
    if ( scissorTest ) {
        enable( gl.SCISSOR_TEST );
    }
    else {
        disable( gl.SCISSOR_TEST );
    }
}
```

这段代码实现了三维图形状态管理中的 '剪切测试' 的开关。

如果 scissorTest 为 true，则执行 `enable( gl.SCISSOR_TEST)`开启剪切测试。

如果 scissorTest 为 false，则执行 `disable( gl.SCISSOR_TEST)` 关闭剪切测试。

剪切测试是一种图形状态，用于控制哪些像素能够被绘制到屏幕上。它使用一个剪切矩形来定义可绘制的区域。只有在剪切矩形内的像素才会被绘制到屏幕上，其他像素将被丢弃。

## activeTexture

用于激活WebGL渲染器上的纹理单元。

```js
function activeTexture( webglSlot ) {
    if ( webglSlot === undefined ) webglSlot = gl.TEXTURE0 + maxTextures - 1;

    if ( currentTextureSlot !== webglSlot ) {
        gl.activeTexture( webglSlot );
        currentTextureSlot = webglSlot;
    }
}
```

参数 `webglSlot` 是激活的纹理单元的编号，默认为 `gl.TEXTURE0 + maxTextures - 1` ，即激活最后一个纹理单元。每个纹理单元都可以独立地绑定一个纹理对象，因此在渲染多个纹理时，需要在 WebGL 环境中激活对应的纹理单元。

如果当前的纹理单元和要激活的纹理单元不同，则使用 `gl.activeTexture` 函数激活该纹理单元，并将当前纹理单元的值更新为要激活的纹理单元。 

## bindTexture

实现了一个用于绑定纹理的功能。

```js
function bindTexture( webglType, webglTexture, webglSlot ) {
    if ( webglSlot === undefined ) {
        if ( currentTextureSlot === null ) {

            webglSlot = gl.TEXTURE0 + maxTextures - 1;

        } else {

            webglSlot = currentTextureSlot;

        }
    }

    let boundTexture = currentBoundTextures[ webglSlot ];

    if ( boundTexture === undefined ) {
        boundTexture = { type: undefined, texture: undefined };
        currentBoundTextures[ webglSlot ] = boundTexture;
    }

    if ( boundTexture.type !== webglType || boundTexture.texture !== webglTexture ) {
        if ( currentTextureSlot !== webglSlot ) {

            gl.activeTexture( webglSlot );
            currentTextureSlot = webglSlot;

        }

        gl.bindTexture( webglType, webglTexture || emptyTextures[ webglType ] );

        boundTexture.type = webglType;
        boundTexture.texture = webglTexture;
    }
}
```

首先，通过检查参数 `webglSlot` 的值：

- 如果未定义，则使用 `gl.TEXTURE0 + maxTextures`。

- 查找当前绑定的纹理数组中是否已经绑定了纹理

- 如果没有，则创建一个新的绑定纹理对象并将其存储在当前绑定纹理数组中

最后，如果当前绑定的纹理类型与 `webglType`不同，或当前绑定的纹理与 `webglTexture`不同，则使用 `gl.bindTexture()` 函数将纹理绑定到指定的纹理槽。同时，将绑定的纹理的类型和纹理本身保存在当前绑定纹理对象中。

## unbindTexture

作用是解除WebGL纹理的绑定。

```js
function unbindTexture() {
    const boundTexture = currentBoundTextures[ currentTextureSlot ];

    if ( boundTexture !== undefined && boundTexture.type !== undefined ) {
        gl.bindTexture( boundTexture.type, null );

        boundTexture.type = undefined;
        boundTexture.texture = undefined;
    }
}
```

它从当前绑定的纹理绑定数组中检索当前激活的纹理单元，并通过调用 WebGL 的 bindTexture() 函数，将该单元绑定到 null 值，从而解除纹理绑定。

## compressedTexImage2D

```js
function compressedTexImage2D() {
    try {
        gl.compressedTexImage2D.apply( gl, arguments );
    }
    catch ( error ) {
        console.error( 'THREE.WebGLState:', error );
    }
}
```

这段代码是 WebGL 的 `compressedTexImage2D` 方法的封装，用于对纹理图像进行压缩。它在 `gl.compressedTexImage` 上调用了 apply 方法，并将 arguments 作为实参传递给 `gl.compressedTexImage`。如果再调用过程中抛出了异常。则在控制台输出错误信息。

## compressedTexImage3D

```js
function compressedTexImage3D() {
    try {
        gl.compressedTexImage3D.apply( gl, arguments );
    } catch ( error ) {
        console.error( 'THREE.WebGLState:', error );
    }
}
```

这段代码片段是对在 WebGL 中使用 3D压缩纹理的封装。

`compressedTexImage3D` 是 WebGL 中用于加载3D压缩纹理的方法，该方法可以接受多个参数。它被封装在这个函数中，用apply方法将它们应用于gl对象。

如果调用该方法发生错误，就会触发错误，并在控制台中输出一条错误信息，以便开发人员定位并解决问题。

## gl.compressedTexImage2D

`gl.compressedTexImage2D` 是 WebGL API 中的一个函数，它用于加载一个二维压缩纹理图像到当前绑定的二维纹理对象中。

它可以接受以下参数：

- target：表示纹理目标类型，一般设置为 gl.TEXTURE_2D。

- level：纹理的多级渐远细分图层的级别。

- internalFormat：压缩纹理图像的内部格式。

- width：压缩纹理图像的宽。

- height：压缩纹理图像的高。

- border：边界的宽度，一般设置为0。

- data：压缩纹理图像的数据。

通过使用压缩纹理图像，我们可以在不损失质量的情况下，使用更少的存储空间保存纹理。这有利于优化渲染性能，特别是在加载大型纹理的情况下。

这个函数通过在代码中使用 `try...catch` 语句来处理错误，如果出现错误，则在控制台中输出错误信息。

### gl.compressedTexImage3D

`gl.compressedTexImage3D` 是 WebGL API 中的一个方法，用于向一个3D纹理对象上传压缩纹理图像数据。该方法通过使用压缩纹理图像数据而不是原始图像数据来为 3D 纹理对象分配内存，这可以显著减少纹理图像数据的内存大小和访问时间，提高渲染性能。

参数说明：

- target：纹理目标（例如：gl.TEXTURE_3D）。

- level：纹理细节级别。

- internalformat：纹理图像的内部格式（例如，gl.COMPRESSED_RGB_S3TC_DXT1_EXT）。

- width：纹理图像的宽度。

- height：纹理图像的高度。

- depth：纹理图像的深度。

- border：纹理图像的边框宽度，始终为0。

- data：包含纹理图像数据的 ArrayBuffer 对象。

该方法可以在 Three.js 中与材质纹理结合使用，以在模型上绘制纹理。

## gl.texSubImage2D

`gl.texSubImage2D` 是 WebGL API 中的一个函数，它可以在已有的纹理图像数据上增加一个或多个2D图像数据子集。可以通过这个函数来对已有纹理图像进行部分更新。

参数说明：

- target：指定纹理目标，如：gl.TEXTURE_2D。

- level：指定目标纹理的细节级别。

- xoffset：指定子图像的左上角的横坐标。

- yoffset：指定子图像的左上角的纵坐标。

- width：指定子图像的宽度。

- height：指定子图像的高度。

- format：指定纹理数据格式。

- type：指定纹理数据类型。

- pixels：指定包含替代纹理数据的数组或ArrayBufferView对象。

这个函数被用来在已经激活了的纹理对象上对其子区域进行更新，从而避免对整个纹理对象进行重新绘制。

## gl.texSubImage3D

`gl.texSubImage3D`  是 WebGL API 的一个函数，用于更新一个 3D 纹理图像的数据。它的作用是对一个已经存在的 3D 纹理对象的某个部分进行更新，而不需要重新创建整个纹理对象。

参数说明：

- target：指定纹理类型，如 gl.TEXTURE_3D。

- level：指定要更新的mipmap层级。

- xoffset：指定更新的图像数据的左边界在纹理图像中的 x 坐标。

- yoffset：指定更新的图像数据的左边界在纹理图像中的 y 坐标。

- zoffset：指定更新的图像数据的左边界在纹理图像中的 z 坐标。

- width：指定更新的图像数据的宽度。

- height：指定更新的图像数据的高度。

- depth：指定更新的图像数据的深度。

- format：指定更新的图像数据的格式，如 gl.RGBA。

- type：指定更新的图像数据的数据类型，如：gl.UNSIGNED_BYTE。

- pixels：指定更新的图像数据。

使用 `gl.texSubImage3D` 可以更灵活地控制 3D 纹理的更新，可以节约更多的资源，并且可以使用更少的带宽来实现多纹理的应用。

## gl.compressedTexSubImage2D

`gl.compressedTexSubImage2D` 是 WebGL API 的一个函数，它的作用是向一个现有的二维纹理图像的一部分中传入压缩的图像数据。

在使用该函数之前，通常需要先使用 `gl.bindTexture` 绑定一个纹理对象，然后在使用该函数来向纹理图像的指定部分传入图像数据。

通过使用压缩的图像数据。可以减小纹理数据的存储空间，同时也可以提高加载纹理图像的速度。因此，使用 `gl.compressedTexSubImage2D` 可以起到优化纹理内存使用和提高纹理加载速度的效果。

## gl.compressedTexSubImage3D

`gl.compressedTexSubImage3D` 是 WebGL API 的一个函数，它的作用是向一个已存在的三维纹理图像中的某一部分填充压缩纹理数据。它允许在不需要完全重新创建三维纹理图像的情况下更新其部分数据，提高了效率。具体来说，它的输入参数包括三维纹理的目标，纹理的多重采样级别，更新的数据的 `x/y/z` 的起始偏移量，更新的数据的宽、高、深度，以及纹理数据的格式等。通过这些参数，可以在三维纹理中的某一部分更新纹理数据。

## gl.texStorage2D

`gl.texStorage2D` 是 WebGL API 的一个方法，它的作用是在不更新它的内容的情况下，预分配二维纹理存储空间。使用该方法可以避免多次重新分配纹理存储空间造成的性能损失。

通俗的说，使用 `gl.texStorage2D` 能够预先为二维纹理分配好存储空间，这样在以后纹理的使用过程中就不用再动态分配空间，从而提高了纹理的使用效率。

## gl.texStorage3D

`gl.texStorage3D` 是 WebGL API 的一个方法，用于在 GPU 中存储 3D 纹理数据。它的作用是用于在一次调用中预先分配内存，并将 3D 纹理数据存储在 GPU 中。

与其他纹理方法（例如 gl.texImage3D 和 gl.compressedTexImage3D）不同，gl.texStorage3D 只分配存储空间，并不上传数据。因此，在使用 gl.texStorage3D 之后，你仍然需要调用 gl.texSubImage3D 来将数据上传到 GPU 中。

效果上，使用 gl.texStorage3D 可以提高 3D 纹理的存储和使用效率，并避免因重复的分配和释放内存造成的性能问题。

## gl.texImage2D

`gl.texImage2D` 是 WebGL API 的一个方法，它用于为纹理对象配置二维纹理图像。这个方法可以在纹理对象创建时使用，也可以在以后更新纹理图像。

## gl.texImage3D

## gl.scissor

## updateUBOMapping

## uniformBlockBinding
