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

```
