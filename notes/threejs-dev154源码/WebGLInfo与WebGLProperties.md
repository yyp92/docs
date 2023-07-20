# WebGLInfo与WebGLProperties

## WebGLInfo

WebGLInfo.js 是 Three.js 中的一个辅助类，用于跟踪 WebGL 渲染信息。它由一个函数构造，该函数以 WebGL 渲染上下文为参数。该类对象包含内存和渲染对象，其中内存对象跟踪几何体和纹理数量，而渲染对象跟踪帧数、调用数、三角函数、点数和线数。

update 函数用于在每次渲染呼叫时更新渲染信息，其中包括渲染模式和实例数。根据模式，该函数更新渲染对象中的三角函数、点数、线数等。

reset 函数用于重置渲染对象中的所有数据，以便在每次帧渲染后进行跟踪。

最后，该类返回一个对象，该对象包含内存和渲染对象，以及自动重置，重置和更新函数。

```js
function WebGLInfo( gl ) {
    const memory = {
        geometries: 0,
        textures: 0
    };

    const render = {
        frame: 0,
        calls: 0,
        triangles: 0,
        points: 0,
        lines: 0
    };

    function update( count, mode, instanceCount ) {
        render.calls ++;

        switch ( mode ) {

            case gl.TRIANGLES:
                render.triangles += instanceCount * ( count / 3 );
                break;

            case gl.LINES:
                render.lines += instanceCount * ( count / 2 );
                break;

            case gl.LINE_STRIP:
                render.lines += instanceCount * ( count - 1 );
                break;

            case gl.LINE_LOOP:
                render.lines += instanceCount * count;
                break;

            case gl.POINTS:
                render.points += instanceCount * count;
                break;

            default:
                console.error( 'THREE.WebGLInfo: Unknown draw mode:', mode );
                break;
        }
    }

    function reset() {
        render.calls = 0;
        render.triangles = 0;
        render.points = 0;
        render.lines = 0;
    }

    return {
        memory: memory,
        render: render,
        programs: null,
        autoReset: true,
        reset: reset,
        update: update
    };
}
```

## WebGLProperties

管理属性。

```js
function WebGLProperties() {
    let properties = new WeakMap();

    function get( object ) {
        let map = properties.get( object );

        if ( map === undefined ) {
            map = {};
            properties.set( object, map );
        }

        return map;
    }

    function remove( object ) {
        properties.delete( object );
    }

    function update( object, key, value ) {
        properties.get( object )[ key ] = value;
    }

    function dispose() {
        properties = new WeakMap();
    }

    return {
        get: get,
        remove: remove,
        update: update,
        dispose: dispose
    };
}
```
