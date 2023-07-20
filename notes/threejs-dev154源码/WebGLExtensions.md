# WebGLExtensions

```js
function WebGLExtensions( gl ) {
    // 存储已获取的扩展对象的缓存
    const extensions = {};

    function getExtension( name ) {
        // 如果已经缓存了该扩展对象，则直接返回缓存的对象
        if ( extensions[ name ] !== undefined ) {
            return extensions[ name ];
        }

        let extension;

        switch ( name ) {
            // 获取深度纹理扩展
            case 'WEBGL_depth_texture':
                extension = gl.getExtension( 'WEBGL_depth_texture' ) || gl.getExtension( 'MOZ_WEBGL_depth_texture' ) || gl.getExtension( 'WEBKIT_WEBGL_depth_texture' );
                break;

            // 获取各向异性过滤扩展
            // 优先顺序为：EXT_texture_filter_anisotropic > MOZ_EXT_texture_filter_anisotropic > WEBKIT_EXT_texture_filter_anisotropic
            case 'EXT_texture_filter_anisotropic':
                extension = gl.getExtension( 'EXT_texture_filter_anisotropic' ) || gl.getExtension( 'MOZ_EXT_texture_filter_anisotropic' ) || gl.getExtension( 'WEBKIT_EXT_texture_filter_anisotropic' );
                break;

            // 获取S3TC压缩纹理扩展
            // 优先顺序为：WEBGL_compressed_texture_s3tc > MOZ_WEBGL_compressed_texture_s3tc > WEBKIT_WEBGL_compressed_texture_s3tc
            case 'WEBGL_compressed_texture_s3tc':
                extension = gl.getExtension( 'WEBGL_compressed_texture_s3tc' ) || gl.getExtension( 'MOZ_WEBGL_compressed_texture_s3tc' ) || gl.getExtension( 'WEBKIT_WEBGL_compressed_texture_s3tc' );
                break;

            // 更多的扩展支持
            case 'WEBGL_compressed_texture_pvrtc':
                extension = gl.getExtension( 'WEBGL_compressed_texture_pvrtc' ) || gl.getExtension( 'WEBKIT_WEBGL_compressed_texture_pvrtc' );
                break;

            // 使用名称直接获取扩展对象
            default:
                extension = gl.getExtension( name );

        }

        // 将扩展对象存储到缓存中
        extensions[name] = extension;

        return extension;

    }

    return {
        has: function ( name ) {
            return getExtension( name ) !== null;
        },

        init: function ( capabilities ) {
            if ( capabilities.isWebGL2 ) {

                getExtension( 'EXT_color_buffer_float' );

            } else {

                getExtension( 'WEBGL_depth_texture' );
                getExtension( 'OES_texture_float' );
                getExtension( 'OES_texture_half_float' );
                getExtension( 'OES_texture_half_float_linear' );
                getExtension( 'OES_standard_derivatives' );
                getExtension( 'OES_element_index_uint' );
                getExtension( 'OES_vertex_array_object' );
                getExtension( 'ANGLE_instanced_arrays' );

            }

            getExtension( 'OES_texture_float_linear' );
            getExtension( 'EXT_color_buffer_half_float' );
            getExtension( 'WEBGL_multisampled_render_to_texture' );
        },

        get: function ( name ) {
            const extension = getExtension( name );

            // 如果无法获取到扩展对象，则打印警告信息
            if ( extension === null ) {
                console.warn( 'THREE.WebGLRenderer: ' + name + ' extension not supported.' );
            }

            return extension;
        }
    };
}
```

上述代码定义了 `WebGLExtensions` 类，用于管理 WebGL 扩展对象。

每次调用 `WebGLExtensions` 工厂函数时，都会创建一个新的 `WebGLExtensions` 实例，并返回该实例的一个对象。该对象包含一个 `get` 方法，用于获取 WebGL 扩展对象。

在 `get` 方法中，首先通过检查缓存来判断是否已经获取过指定名称的扩展对象。如果已经缓存了该扩展对象，则直接返回缓存的对象。

如果尚未缓存该扩展对象，则根据名称使用 `gl.getExtension` 方法获取对应的 WebGL 扩展对象。不同的扩展名称有不同的处理方式，例如：

- 对于 'WEBGL_depth_texture'，直接使用 `gl.getExtension` 获取深度纹理扩展对象。
- 对于 'EXT_texture_filter_anisotropic'，按照指定的优先顺序尝试获取各向异性过滤扩展对象，如果无法获取则依次尝试下一个扩展。
- 对于 'WEBGL_compressed_texture_s3tc'，按照指定的优先顺序尝试获取 S3TC 压缩纹理扩展对象，如果无法获取则依次尝试下一个扩展。

如果无法获取到扩展对象，则打印一条警告信息。

最后，将获取到的扩展对象存储到缓存中，并返回该对象。

通过调用 `WebGLExtensions` 工厂函数，可以创建一个 `WebGLExtensions` 实例，然后通过调用其 `get` 方法来获取所需的 WebGL 扩展对象。这样就能方便地管理和使用 WebGL 扩展，为 Three.js 的渲染器添加额外的功能和效果。
