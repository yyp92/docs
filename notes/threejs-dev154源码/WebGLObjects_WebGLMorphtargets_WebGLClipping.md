# WebGLObjects_WebGLMorphtargets_WebGLClipping

## WebGLObjects

管理几何体对象和缓冲区对象，让渲染区的每一帧的缓冲区的数据跟几何体对象的数据保持一致。

```js
function WebGLObjects( gl, geometries, attributes, info ) {
    let updateMap = new WeakMap();

    function update( object ) {
        const frame = info.render.frame;

        const geometry = object.geometry;
        const buffergeometry = geometries.get( object, geometry );

        // Update once per frame

        if ( updateMap.get( buffergeometry ) !== frame ) {
            geometries.update( buffergeometry );

            updateMap.set( buffergeometry, frame );
        }

        if ( object.isInstancedMesh ) {
            if ( object.hasEventListener( 'dispose', onInstancedMeshDispose ) === false ) {
                object.addEventListener( 'dispose', onInstancedMeshDispose );
            }

            if ( updateMap.get( object ) !== frame ) {
                attributes.update( object.instanceMatrix, gl.ARRAY_BUFFER );

                if ( object.instanceColor !== null ) {
                    attributes.update( object.instanceColor, gl.ARRAY_BUFFER );
                }

                updateMap.set( object, frame );
            }
        }

        if ( object.isSkinnedMesh ) {
            const skeleton = object.skeleton;

            if ( updateMap.get( skeleton ) !== frame ) {
                skeleton.update();

                updateMap.set( skeleton, frame );
            }
        }

        return buffergeometry;
    }

    function dispose() {
        updateMap = new WeakMap();
    }

    function onInstancedMeshDispose( event ) {
        const instancedMesh = event.target;

        instancedMesh.removeEventListener( 'dispose', onInstancedMeshDispose );

        attributes.remove( instancedMesh.instanceMatrix );

        if ( instancedMesh.instanceColor !== null ) attributes.remove( instancedMesh.instanceColor );
    }

    return {
        update: update,
        dispose: dispose
    };
}


export { WebGLObjects };
```

## WebGLMorphtargets

主要管理形变。

```js
import { FloatType } from '../../constants.js';
import { DataArrayTexture } from '../../textures/DataArrayTexture.js';
import { Vector4 } from '../../math/Vector4.js';
import { Vector2 } from '../../math/Vector2.js';

function numericalSort( a, b ) {
    return a[ 0 ] - b[ 0 ];
}

function absNumericalSort( a, b ) {
    return Math.abs( b[ 1 ] ) - Math.abs( a[ 1 ] );
}

function WebGLMorphtargets( gl, capabilities, textures ) {
    const influencesList = {};
    const morphInfluences = new Float32Array( 8 );
    const morphTextures = new WeakMap();
    const morph = new Vector4();

    const workInfluences = [];

    for ( let i = 0; i < 8; i ++ ) {
        workInfluences[ i ] = [ i, 0 ];
    }

    function update( object, geometry, program ) {
        const objectInfluences = object.morphTargetInfluences;

        if ( capabilities.isWebGL2 === true ) {
            // instead of using attributes, the WebGL 2 code path encodes morph targets
            // into an array of data textures. Each layer represents a single morph target.

            const morphAttribute = geometry.morphAttributes.position || geometry.morphAttributes.normal || geometry.morphAttributes.color;
            const morphTargetsCount = ( morphAttribute !== undefined ) ? morphAttribute.length : 0;

            let entry = morphTextures.get( geometry );

            if ( entry === undefined || entry.count !== morphTargetsCount ) {
                if ( entry !== undefined ) entry.texture.dispose();

                const hasMorphPosition = geometry.morphAttributes.position !== undefined;
                const hasMorphNormals = geometry.morphAttributes.normal !== undefined;
                const hasMorphColors = geometry.morphAttributes.color !== undefined;

                const morphTargets = geometry.morphAttributes.position || [];
                const morphNormals = geometry.morphAttributes.normal || [];
                const morphColors = geometry.morphAttributes.color || [];

                let vertexDataCount = 0;

                if ( hasMorphPosition === true ) vertexDataCount = 1;
                if ( hasMorphNormals === true ) vertexDataCount = 2;
                if ( hasMorphColors === true ) vertexDataCount = 3;

                let width = geometry.attributes.position.count * vertexDataCount;
                let height = 1;

                if ( width > capabilities.maxTextureSize ) {
                    height = Math.ceil( width / capabilities.maxTextureSize );
                    width = capabilities.maxTextureSize;
                }

                const buffer = new Float32Array( width * height * 4 * morphTargetsCount );

                const texture = new DataArrayTexture( buffer, width, height, morphTargetsCount );
                texture.type = FloatType;
                texture.needsUpdate = true;

                // fill buffer

                const vertexDataStride = vertexDataCount * 4;

                for ( let i = 0; i < morphTargetsCount; i ++ ) {
                    const morphTarget = morphTargets[ i ];
                    const morphNormal = morphNormals[ i ];
                    const morphColor = morphColors[ i ];

                    const offset = width * height * 4 * i;

                    for ( let j = 0; j < morphTarget.count; j ++ ) {
                        const stride = j * vertexDataStride;

                        if ( hasMorphPosition === true ) {
                            morph.fromBufferAttribute( morphTarget, j );

                            buffer[ offset + stride + 0 ] = morph.x;
                            buffer[ offset + stride + 1 ] = morph.y;
                            buffer[ offset + stride + 2 ] = morph.z;
                            buffer[ offset + stride + 3 ] = 0;
                        }

                        if ( hasMorphNormals === true ) {
                            morph.fromBufferAttribute( morphNormal, j );

                            buffer[ offset + stride + 4 ] = morph.x;
                            buffer[ offset + stride + 5 ] = morph.y;
                            buffer[ offset + stride + 6 ] = morph.z;
                            buffer[ offset + stride + 7 ] = 0;
                        }

                        if ( hasMorphColors === true ) {
                            morph.fromBufferAttribute( morphColor, j );

                            buffer[ offset + stride + 8 ] = morph.x;
                            buffer[ offset + stride + 9 ] = morph.y;
                            buffer[ offset + stride + 10 ] = morph.z;
                            buffer[ offset + stride + 11 ] = ( morphColor.itemSize === 4 ) ? morph.w : 1;
                        }
                    }
                }

                entry = {
                    count: morphTargetsCount,
                    texture: texture,
                    size: new Vector2( width, height )
                };

                morphTextures.set( geometry, entry );

                function disposeTexture() {
                    texture.dispose();

                    morphTextures.delete( geometry );

                    geometry.removeEventListener( 'dispose', disposeTexture );
                }

                geometry.addEventListener( 'dispose', disposeTexture );
            }

            let morphInfluencesSum = 0;

            for ( let i = 0; i < objectInfluences.length; i ++ ) {
                morphInfluencesSum += objectInfluences[ i ];
            }

            const morphBaseInfluence = geometry.morphTargetsRelative ? 1 : 1 - morphInfluencesSum;

            program.getUniforms().setValue( gl, 'morphTargetBaseInfluence', morphBaseInfluence );
            program.getUniforms().setValue( gl, 'morphTargetInfluences', objectInfluences );

            program.getUniforms().setValue( gl, 'morphTargetsTexture', entry.texture, textures );
            program.getUniforms().setValue( gl, 'morphTargetsTextureSize', entry.size );
        } else {
            // When object doesn't have morph target influences defined, we treat it as a 0-length array
            // This is important to make sure we set up morphTargetBaseInfluence / morphTargetInfluences

            const length = objectInfluences === undefined ? 0 : objectInfluences.length;

            let influences = influencesList[ geometry.id ];

            if ( influences === undefined || influences.length !== length ) {
                // initialise list

                influences = [];

                for ( let i = 0; i < length; i ++ ) {

                    influences[ i ] = [ i, 0 ];

                }

                influencesList[ geometry.id ] = influences;
            }

            // Collect influences

            for ( let i = 0; i < length; i ++ ) {
                const influence = influences[ i ];

                influence[ 0 ] = i;
                influence[ 1 ] = objectInfluences[ i ];
            }

            influences.sort( absNumericalSort );

            for ( let i = 0; i < 8; i ++ ) {
                if ( i < length && influences[ i ][ 1 ] ) {
                    workInfluences[ i ][ 0 ] = influences[ i ][ 0 ];
                    workInfluences[ i ][ 1 ] = influences[ i ][ 1 ];
                } else {
                    workInfluences[ i ][ 0 ] = Number.MAX_SAFE_INTEGER;
                    workInfluences[ i ][ 1 ] = 0;
                }
            }

            workInfluences.sort( numericalSort );

            const morphTargets = geometry.morphAttributes.position;
            const morphNormals = geometry.morphAttributes.normal;

            let morphInfluencesSum = 0;

            for ( let i = 0; i < 8; i ++ ) {
                const influence = workInfluences[ i ];
                const index = influence[ 0 ];
                const value = influence[ 1 ];

                if ( index !== Number.MAX_SAFE_INTEGER && value ) {
                    if ( morphTargets && geometry.getAttribute( 'morphTarget' + i ) !== morphTargets[ index ] ) {
                        geometry.setAttribute( 'morphTarget' + i, morphTargets[ index ] );
                    }

                    if ( morphNormals && geometry.getAttribute( 'morphNormal' + i ) !== morphNormals[ index ] ) {
                        geometry.setAttribute( 'morphNormal' + i, morphNormals[ index ] );
                    }

                    morphInfluences[ i ] = value;
                    morphInfluencesSum += value;
                } else {
                    if ( morphTargets && geometry.hasAttribute( 'morphTarget' + i ) === true ) {
                        geometry.deleteAttribute( 'morphTarget' + i );
                    }

                    if ( morphNormals && geometry.hasAttribute( 'morphNormal' + i ) === true ) {
                        geometry.deleteAttribute( 'morphNormal' + i );
                    }

                    morphInfluences[ i ] = 0;
                }
            }

            // GLSL shader uses formula baseinfluence * base + sum(target * influence)
            // This allows us to switch between absolute morphs and relative morphs without changing shader code
            // When baseinfluence = 1 - sum(influence), the above is equivalent to sum((target - base) * influence)
            const morphBaseInfluence = geometry.morphTargetsRelative ? 1 : 1 - morphInfluencesSum;

            program.getUniforms().setValue( gl, 'morphTargetBaseInfluence', morphBaseInfluence );
            program.getUniforms().setValue( gl, 'morphTargetInfluences', morphInfluences );
        }
    }

    return {
        update: update 
    };
}


export { WebGLMorphtargets };
```

## WebGLClipping

裁剪功能，全部的裁剪和局部裁剪。

```js
import { Matrix3 } from '../../math/Matrix3.js';
import { Plane } from '../../math/Plane.js';

function WebGLClipping( properties ) {
    const scope = this;

    let globalState = null,
        numGlobalPlanes = 0,
        localClippingEnabled = false,
        renderingShadows = false;

    const plane = new Plane(),
        viewNormalMatrix = new Matrix3(),

        uniform = { value: null, needsUpdate: false };

    this.uniform = uniform;
    this.numPlanes = 0;
    this.numIntersection = 0;

    this.init = function ( planes, enableLocalClipping ) {
        const enabled =
            planes.length !== 0 ||
            enableLocalClipping ||
            // enable state of previous frame - the clipping code has to
            // run another frame in order to reset the state:
            numGlobalPlanes !== 0 ||
            localClippingEnabled;

        localClippingEnabled = enableLocalClipping;

        numGlobalPlanes = planes.length;

        return enabled;
    };

    this.beginShadows = function () {
        renderingShadows = true;
        projectPlanes( null );
    };

    this.endShadows = function () {
        renderingShadows = false;
    };

    this.setGlobalState = function ( planes, camera ) {
        globalState = projectPlanes( planes, camera, 0 );
    };

    this.setState = function ( material, camera, useCache ) {
        const planes = material.clippingPlanes,
            clipIntersection = material.clipIntersection,
            clipShadows = material.clipShadows;

        const materialProperties = properties.get( material );

        if ( ! localClippingEnabled || planes === null || planes.length === 0 || renderingShadows && ! clipShadows ) {
            // there's no local clipping

            if ( renderingShadows ) {
                // there's no global clipping

                projectPlanes( null );
            } else {
                resetGlobalState();
            }
        } else {
            const nGlobal = renderingShadows ? 0 : numGlobalPlanes,
                lGlobal = nGlobal * 4;

            let dstArray = materialProperties.clippingState || null;

            uniform.value = dstArray; // ensure unique state

            dstArray = projectPlanes( planes, camera, lGlobal, useCache );

            for ( let i = 0; i !== lGlobal; ++ i ) {
                dstArray[ i ] = globalState[ i ];
            }

            materialProperties.clippingState = dstArray;
            this.numIntersection = clipIntersection ? this.numPlanes : 0;
            this.numPlanes += nGlobal;
        }
    };

    function resetGlobalState() {
        if ( uniform.value !== globalState ) {
            uniform.value = globalState;
            uniform.needsUpdate = numGlobalPlanes > 0;
        }

        scope.numPlanes = numGlobalPlanes;
        scope.numIntersection = 0;
    }

    function projectPlanes( planes, camera, dstOffset, skipTransform ) {
        const nPlanes = planes !== null ? planes.length : 0;
        let dstArray = null;

        if ( nPlanes !== 0 ) {
            dstArray = uniform.value;

            if ( skipTransform !== true || dstArray === null ) {
                const flatSize = dstOffset + nPlanes * 4,
                    viewMatrix = camera.matrixWorldInverse;

                viewNormalMatrix.getNormalMatrix( viewMatrix );

                if ( dstArray === null || dstArray.length < flatSize ) {
                    dstArray = new Float32Array( flatSize );
                }

                for ( let i = 0, i4 = dstOffset; i !== nPlanes; ++ i, i4 += 4 ) {
                    plane.copy( planes[ i ] ).applyMatrix4( viewMatrix, viewNormalMatrix );

                    plane.normal.toArray( dstArray, i4 );
                    dstArray[ i4 + 3 ] = plane.constant;
                }
            }

            uniform.value = dstArray;
            uniform.needsUpdate = true;
        }

        scope.numPlanes = nPlanes;
        scope.numIntersection = 0;

        return dstArray; 
    }
}


export { WebGLClipping };
```
