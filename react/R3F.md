# R3F

React 환경에서는 React Three Fiber(R3F) 를 사용하는 것 입니다.

## React Three Fiber 섫정.

-> react-three/fiber 설치. 

Three.js 

``` javascript
const mesh = new THREE.Mesh()
mesh.position.set(1, 2, 3)
mesh.rotation.x = 0.5
mesh.geometry = new THREE.BoxGeometry(1, 1, 1)
mesh.material = new THREE.MeshBasicMaterial({ color: 'red' })

scene.add(mesh) 
```

R3F 

``` javascript
<mesh position={[ 1, 2, 3 ]} rotation-x={0.5}>
    <boxGeometry />
    <meshBasicMaterial color="red" />
</mesh>
```


----


Three.js

``` javascript
const group = new THREE.Group()
scene.add(group)

const mesh1 = new THREE.Mesh()
mesh1.geometry = new THREE.BoxGeometry(1, 1, 1)
mesh1.material = new THREE.MeshBasicMaterial({ color: 'red' })

const mesh2 = new THREE.Mesh()
mesh2.geometry = new THREE.SphereGeometry(0.5)
mesh2.material = new THREE.MeshBasicMaterial({ color: 'orange' })

group.add(mesh1, mesh2) 
```

R3F 

``` javascript
<group>
    <mesh>
        <boxGeometry />
        <meshBasicMaterial color="red" />
    </mesh>
    <mesh>
        <sphereGeometry />
        <meshBasicMaterial color="orange" />
    </mesh>
</group>
```

일단 문법은 이런 식으로 진행 됩니다.




## Canvas 


Canvas 캔버스를 생성하려면 종속성을 가져와야 합니다.
-> Canvas -> @react-three/fiber 

``` javascript
import { Canvas } from '@react-three/fiber' 

root.render(
    <>
        <Canvas></Canvas>
    </>
)
```


``` javascript
root.render(
    <>
        <Canvas>
            <mesh>
                <torusKnotGeometry />
                <meshNormalMaterial />
            </mesh>
        </Canvas>
    </> 
)
```



## Resizing 

<canvas> -> R3F 에 의해 생성된 요소 `<Canvas>` 상위 `#root` 요소의 크기를 조정합니다.

``` css
html,
body,
#root {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    overflow: hidden;
}
```



## Features 

큐브를 회전시키고 싶을 떄 -> `useFrame` Hook 을 사용하기.

``` javascript
import { useFrame } from '@react-three/fiber' 

export default function Experience() {
    useFrame(() => {
        console.log('tick');
    })
}
```

cube 를 rotation 속성을 업데이트 하려면 어떻게 해야 할까요?

``` javascript
import { useRef } from 'react';

export default function Experience() {
    const cubeRef = useRef()

    useFrame((state, delta) => {
        cubeRef.current.rotation.y += 0.01
    })

    return (
        <mesh ref={cubeRef} rotation-y={Math.PI * 0.25} position-x={2} scale={1.5}>
    )
}
```

`state` 카메라, 렌더러, 장면 등 Three.js
`delta` 는 마지막 프레임 이후 소요된 시간(초) 을 포함하여 직접 사용 가능 합니다. 




## Group

Group 를 사용하여 어떻게 해야 할까? -> Group 컴포넌트로 덮어주면 됩니다.

``` javascript
<group>
    <mesh>
    
    </mesh>

    <mesh>
    
    </mesh>
</group>
```


## OrbitControls 

장면 주위에서 Camera 를 회전하기 위해 -> `OrbitControls` 를 추가하고 싶습니다.

``` javascript
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'
import { extend, useFrame } from '@react-three/fiber' 

extend({ OrbitControls: OrbitControls })
```

``` javascript
import { useThree, extends, useFrame } from '@react-three/fiber'

export default function Experience() {
    const three = useThree()
    console.log(three)
}
```

Fiber 에는 여러 개 모든 것이 포함되어 있습니다.

- three.camera : PerspectiveCamera
- three.gl : WebGLRenderer 를 포함하는 domElement, 즉 <canvas>
- three.clock : clock 의 인스턴스.

``` javascript
export default function Experience() {
    const { camera, gl } = useThree() 

    return (
        <>
            <orbitControls args={ [ camera, gl.domElement ] } />
        </>
    )
}
```


## Light 


``` javascript
export default function Experience() {

    return (
        <>
            <directionalLight position={[ 1, 2, 3 ]} intensity={1.5} />  
        
            <group ref={ groupRef }>
                <mesh position-x={ - 2 }>
                    <sphereGeometry />
                    <meshStandardMaterial color="orange" />
                </mesh>

                <mesh ref={ cubeRef } rotation-y={ Math.PI * 0.25 } position-x={ 2 } scale={ 1.5 }>
                    <boxGeometry />
                    <meshStandardMaterial color="mediumpurple" />
                </mesh>
            </group>

            <mesh position-y={ - 1 } rotation-x={ - Math.PI * 0.5 } scale={ 10 }>
                <planeGeometry />
                <meshStandardMaterial color="greenyellow" />
            </mesh>
        </>
    )
}
```




## Custom Geometry 


``` javascript
export default function CustomObject() {
    return (
        <mesh>
            <boxGeometry />
            <meshBasicMaterial color="red" />
        </mesh>
    )
}

export default function Experience() {
    return (
        <>
            <CustomObject />
        </>
    )
}

```



## Positions array 

-> `verticesCount` 삼각형을 원하고, 삼각형 당 점이 10 필요합니다.

``` javascript
export default function CustomObject() {
    const verticesCount = 10 * 3 
    // ...
}
```

-> Float32Array 모든 정점 위치를 생성할 수 있으며, 배열 크기를 지정해야 합니다.

``` javascript
export default function CustomObject() {
    const verticesCount = 10 * 3
    const positions = new Float32Array(verticesCount * 3)
    
    for (let i = 0; i < verticesCount * 3; i++) {
        positions[i] = (Math.random() - 0.5) * 3 
    }
}
```



## BufferGeometry 및 BufferAttribute 

-> <boxGeometry>, <bufferGeometry>

``` javascript
<mesh>
    <bufferGeometry />
    <meshBasicMaterial color="red" />
</mesh>
```

``` javascript
<bufferGeometry>
    <bufferAttribute />
</bufferGeometry>
```

`attributes-position`, `BufferAttribute`에 첨부됩니다.
`<bufferAttribute>`

- count: 정점의 수.
- itemSize: 하나의 정점을 구성하는 배열의 항목 수.
- array: 실제 배열.

-> <bufferAttribute> 에서는 3가지 속성을 지정할 수 있습니다.

- count : 정점의 수.
- itemSize : 하나의 정점을 구성하는 배열의 항목 수.
- array : 실제 배열.

``` javascript
<bufferGeometry>
    <bufferAttribute 
        attach="attributes-position"
        count={verticesCount}
        itemSize={3}
        array={positions}  
    />
</bufferGeometry>
```


## 양면 Side 

-> `side` 속성을 값으로 추가할 수 있습니다. 

``` javascript
<meshBasicMaterial color="red" side={THREE.DoubleSide} />
```



## useMemo 로 정점 최적화.

`useMemo` 보내는 `React hook` 입니다.

``` javascript
export default function CustomObject() {
    
    const verticesCount = 10 * 3
    const positions = useMemo(() => {
        const positions = new Float32Array(verticesCount * 3) 

        for(let i = 0; i < verticesCount * 3; i++) {
            positions[i] = (Math.random() - 0.5) * 3 

            return positions;
        }
    })
    // ...
}
```



## Compute vertex normal 

`<meshStandardMaterial>` 대신 `<meshBasicMaterial>` -> 사용자 정의 개체를 테스트 해보자.

``` javascript
<mesh>
    {/* ... */}
    <meshStandardMaterial color="red" side={ THREE.DoubleSide } />
</mesh>
```

``` javascript
import { useRef, useMemo } from 'react'

export default function CustomObject() {
    const geometryRef = useRef() 

    geometryRef.current.computeVertexNormals() 

    return (
        <>
            <bufferGeometry ref={geometryRef}>
                {/* ... */}
            </bufferGeometry>
        </>
    )
}
```

``` javascript
import { useEffect, useRef, useMemo } from 'react'

export default function CustomObject() {
    
    const positions = useMemo(() => {
        // ... 
    })

    useEffect(() => {
        // ...
        geometryRef.current.computeVertexNormals()
    }, [ positions ])
}
```



## Canvas settings 

-> Canvas 구성 요소에서는 -> 장면, Camera, Renderer, incoding 등 많은 것을 설정합니다.
떄떄로 이러한 설정을 변경해야 합니다.

`<Canvas>` 에서 대부분 바로 속성을 사용하여 변경할 수 있습니다.

``` javascript
<Canvas camera={ {} }>
    <Experience />
</Canvas>
```

`fov` 객체 내, `near` 같은 Camera 기본 구조를 따라 매개변수를 보낼 수 있습니다.

``` javascript
<Canvas camera={ { fov: 45, near: 0.1, far: 200 } }>
```



## OrthographicCamera 로 변경.

orthographic 에 속성 추가 `<Canvas>`

``` javascript
<Canvas 
    orthographic
    camera={ { 
        fov: 45,
        near: 0.1, 
        far: 200,
        position: [ 3, 2, 6 ]
    } }
>
    <Experience />
</Canvas>
```

-> 함수를 만들어서 선언 해 주기.

``` javascript
const cameraSettings = {
    fov: 45,
    zoom: 100,
    near: 0.1,
    far: 200,
    position: [ 3, 2, 6 ]
}

root.render(
    <Canvas 
        orthoraphic
        camera={ cameraSettings }
    >
        <Experience />
    </Canvas>
)
```



## Camera Animation 

-> `<orbitControls>` -> 사용자 제어하도록 하는 대신 Camera 에 Animation 적용하는 방법.

``` javascript
useFrame((state, delta) => {
    console.log(state.camera)
    cubeRef.current.rotation.y += delta 

    const angle = state.clock.elapsedTime
    console.log(angle)
})
```


