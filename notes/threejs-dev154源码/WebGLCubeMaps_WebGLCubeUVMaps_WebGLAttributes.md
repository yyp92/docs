# WebGLCubeMaps_WebGLCubeUVMaps_WebGLAttributes

## WebGLCubeMaps

用于将六面体反射/折射贴图（EquirectangularReflectionMapping 和 EquirectangularRefractionMapping）转换为立方体反射/折射贴图（CubeReflectionMapping 和 CubeRefractionMapping）。

- 先从 "../../constants.js" 引入四种映射的常量，以及 "../WebGLCubeRenderTarget.js" 引入WebGLCubeRenderTarget类。

- 然后定义一个 mapTextureMapping 函数，用于映射纹理。

- get函数用于得到立方体贴图，当传入的纹理是 EquirectangularReflectionMapping 或 EquirectangularRefractionMapping映射时，它会将其转换为 CubeReflectionMapping 和 CubeRefractionMapping。如果传入的纹理已经转换过，就直接从缓存中返回，如果没有转换过，就利用WebGLCubeRenderTarget 类进行转换。

- onTextureDispose 函数用于在纹理被销毁时，同时销毁立方体贴图。

- dispose 函数用于销毁 WeakMap，释放内存。

最后，返回一个包含 `get` 和 `dispose` 函数的对象，这两个函数是该类的公共接口。

```js
import { CubeReflectionMapping, CubeRefractionMapping, EquirectangularReflectionMapping, EquirectangularRefractionMapping } from '../../constants.js';
import { WebGLCubeRenderTarget } from '../WebGLCubeRenderTarget.js';

function WebGLCubeMaps( renderer ) {
    let cubemaps = new WeakMap();

    function mapTextureMapping( texture, mapping ) {
        if ( mapping === EquirectangularReflectionMapping ) {
            texture.mapping = CubeReflectionMapping;
        }
        else if ( mapping === EquirectangularRefractionMapping ) {
            texture.mapping = CubeRefractionMapping;
        }

        return texture;
    }

    function get( texture ) {
        if ( texture && texture.isTexture && texture.isRenderTargetTexture === false ) {
            const mapping = texture.mapping;

            if ( mapping === EquirectangularReflectionMapping || mapping === EquirectangularRefractionMapping ) {
                if ( cubemaps.has( texture ) ) {
                    const cubemap = cubemaps.get( texture ).texture;
                    return mapTextureMapping( cubemap, texture.mapping );
                }
                else {
                    const image = texture.image;

                    if ( image && image.height > 0 ) {
                        const renderTarget = new WebGLCubeRenderTarget( image.height / 2 );
                        renderTarget.fromEquirectangularTexture( renderer, texture );
                        cubemaps.set( texture, renderTarget );

                        texture.addEventListener( 'dispose', onTextureDispose );

                        return mapTextureMapping( renderTarget.texture, texture.mapping );
                    }
                    else {
                        // image not yet ready. try the conversion next frame
                        return null;
                    }
                }
            }
        }

        return texture;
    }

    function onTextureDispose( event ) {
        const texture = event.target;

        texture.removeEventListener( 'dispose', onTextureDispose );

        const cubemap = cubemaps.get( texture );

        if ( cubemap !== undefined ) {
            cubemaps.delete( texture );
            cubemap.dispose();
        }
    }

    function dispose() {
        cubemaps = new WeakMap();
    }

    return {
        get: get,
        dispose: dispose
    };
}

export { WebGLCubeMaps };
```

## WebGLCubeUVMaps

```js
import { CubeReflectionMapping, CubeRefractionMapping, EquirectangularReflectionMapping, EquirectangularRefractionMapping } from '../../constants.js';
import { PMREMGenerator } from '../../extras/PMREMGenerator.js';

function WebGLCubeUVMaps( renderer ) {
    let cubeUVmaps = new WeakMap();

    let pmremGenerator = null;

    function get( texture ) {
        if ( texture && texture.isTexture ) {
            const mapping = texture.mapping;

            const isEquirectMap = ( mapping === EquirectangularReflectionMapping || mapping === EquirectangularRefractionMapping );
            const isCubeMap = ( mapping === CubeReflectionMapping || mapping === CubeRefractionMapping );

            // equirect/cube map to cubeUV conversion

            if ( isEquirectMap || isCubeMap ) {
                if ( texture.isRenderTargetTexture && texture.needsPMREMUpdate === true ) {
                    texture.needsPMREMUpdate = false;

                    let renderTarget = cubeUVmaps.get( texture );

                    if ( pmremGenerator === null ) pmremGenerator = new PMREMGenerator( renderer );

                    renderTarget = isEquirectMap ? pmremGenerator.fromEquirectangular( texture, renderTarget ) : pmremGenerator.fromCubemap( texture, renderTarget );
                    cubeUVmaps.set( texture, renderTarget );

                    return renderTarget.texture;
                }
                else {
                    if ( cubeUVmaps.has( texture ) ) {
                        return cubeUVmaps.get( texture ).texture;
                    }
                    else {
                        const image = texture.image;

                        if ( ( isEquirectMap && image && image.height > 0 ) || ( isCubeMap && image && isCubeTextureComplete( image ) ) ) {
                            if ( pmremGenerator === null ) pmremGenerator = new PMREMGenerator( renderer );

                            const renderTarget = isEquirectMap ? pmremGenerator.fromEquirectangular( texture ) : pmremGenerator.fromCubemap( texture );
                            cubeUVmaps.set( texture, renderTarget );

                            texture.addEventListener( 'dispose', onTextureDispose );

                            return renderTarget.texture;
                        }
                        else {
                            // image not yet ready. try the conversion next frame
                            return null;
                        }
                    }
                }
            }
        }

        return texture;
    }

    function isCubeTextureComplete( image ) {
        let count = 0;
        const length = 6;

        for ( let i = 0; i < length; i ++ ) {
            if ( image[ i ] !== undefined ) count ++;
        }

        return count === length;
    }

    function onTextureDispose( event ) {
        const texture = event.target;

        texture.removeEventListener( 'dispose', onTextureDispose );

        const cubemapUV = cubeUVmaps.get( texture );

        if ( cubemapUV !== undefined ) {
            cubeUVmaps.delete( texture );
            cubemapUV.dispose();
        }
    }

    function dispose() {
        cubeUVmaps = new WeakMap();

        if ( pmremGenerator !== null ) {
            pmremGenerator.dispose();
            pmremGenerator = null;
        }
    }

    return {
        get: get,
        dispose: dispose
    };
}

export { WebGLCubeUVMaps };
```

## WebGLAttributes

```js
function WebGLAttributes( gl, capabilities ) {
    const isWebGL2 = capabilities.isWebGL2;

    const buffers = new WeakMap();

    function createBuffer( attribute, bufferType ) {
        const array = attribute.array;
        const usage = attribute.usage;

        const buffer = gl.createBuffer();

        gl.bindBuffer( bufferType, buffer );
        gl.bufferData( bufferType, array, usage );

        attribute.onUploadCallback();

        let type;

        if ( array instanceof Float32Array ) {
            type = gl.FLOAT;
        }
        else if ( array instanceof Uint16Array ) {
            if ( attribute.isFloat16BufferAttribute ) {
                if ( isWebGL2 ) {
                    type = gl.HALF_FLOAT;
                }
                else {
                    throw new Error( 'THREE.WebGLAttributes: Usage of Float16BufferAttribute requires WebGL2.' );
                }
            }
            else {
                type = gl.UNSIGNED_SHORT;
            }
        }
        else if ( array instanceof Int16Array ) {
            type = gl.SHORT;
        }
        else if ( array instanceof Uint32Array ) {
            type = gl.UNSIGNED_INT;
        }
        else if ( array instanceof Int32Array ) {
            type = gl.INT;
        }
        else if ( array instanceof Int8Array ) {
            type = gl.BYTE;
        }
        else if ( array instanceof Uint8Array ) {
            type = gl.UNSIGNED_BYTE;
        }
        else if ( array instanceof Uint8ClampedArray ) {
            type = gl.UNSIGNED_BYTE;
        }
        else {
            throw new Error( 'THREE.WebGLAttributes: Unsupported buffer data format: ' + array );
        }

        return {
            buffer: buffer,
            type: type,
            bytesPerElement: array.BYTES_PER_ELEMENT,
            version: attribute.version
        };
    }

    function updateBuffer( buffer, attribute, bufferType ) {
        const array = attribute.array;
        const updateRange = attribute.updateRange;

        gl.bindBuffer( bufferType, buffer );

        if ( updateRange.count === - 1 ) {
            // Not using update ranges

            gl.bufferSubData( bufferType, 0, array );
        }
        else {
            if ( isWebGL2 ) {
                gl.bufferSubData( bufferType, updateRange.offset * array.BYTES_PER_ELEMENT,
                    array, updateRange.offset, updateRange.count );
            }
            else {
                gl.bufferSubData( bufferType, updateRange.offset * array.BYTES_PER_ELEMENT,
                    array.subarray( updateRange.offset, updateRange.offset + updateRange.count ) );
            }

            updateRange.count = - 1; // reset range

        }

        attribute.onUploadCallback();
    }

    function get( attribute ) {
        if ( attribute.isInterleavedBufferAttribute ) attribute = attribute.data;

        return buffers.get( attribute );
    }

    function remove( attribute ) {
        if ( attribute.isInterleavedBufferAttribute ) attribute = attribute.data;

        const data = buffers.get( attribute );

        if ( data ) {
            gl.deleteBuffer( data.buffer );

            buffers.delete( attribute );
        }
    }

    function update( attribute, bufferType ) {
        if ( attribute.isGLBufferAttribute ) {
            const cached = buffers.get( attribute );

            if ( ! cached || cached.version < attribute.version ) {
                buffers.set( attribute, {
                    buffer: attribute.buffer,
                    type: attribute.type,
                    bytesPerElement: attribute.elementSize,
                    version: attribute.version
                } );
            }

            return;
        }

        if ( attribute.isInterleavedBufferAttribute ) attribute = attribute.data;

        const data = buffers.get( attribute );

        if ( data === undefined ) {
            buffers.set( attribute, createBuffer( attribute, bufferType ) );
        }
        else if ( data.version < attribute.version ) {
            updateBuffer( data.buffer, attribute, bufferType );


        data.version = attribute.version;
        }
    }

    return {
        get: get,
        remove: remove,
        update: update
    };
}


export { WebGLAttributes };
```
