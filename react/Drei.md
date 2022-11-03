# Drei 

- Camera Controls
- Complex geometries
- Post-processing
- HTML implementation
- Loaders
- Environment settings
- Complex calculations
- Etc 



## OrbitControls 

Experience.js -> OrbitControls, extend, orbitControls -> useThree 제거 합니다.

``` javascript
export default function Experience() {
    return (
        <>
            <directionalLight position={ [1, 2, 3] } intensity={1.5} />
            <ambientLight intensity={ 0.5 } />

            <mesh position-x={ -2 } />
                <sphereGeometry />
                <meshStandardMaterial color="orange" />                
            <mesh />

            <mesh position-x={ -2 } scale={1.5} />
                <sphereGeometry />
                <meshStandardMaterial color="orange" />                
            <mesh />

            <mesh position-x={ -1 } rotation-x={-Math.PI * 0.5} scale={ 10 } />
                <sphereGeometry />
                <meshStandardMaterial color="orange" />                
            <mesh />
        </>
    )
}
```



## TransformControls 

TransformControls -> (위치, 회전 또는 size 조정) 할 수 있는 gizmo 추가 합니다.
`TransformControls` 가져옵니다 -> `@react-three/drei`

``` javascript
import { TransformControls, OrbitControls } from '@react-three/drei'

<TransformControls>
    <mesh position-x={ 2 } scale={ 1.5 }>
        <boxGeometry />
        <meshStandardMaterial color="mediumpurple" />
    </mesh>
</TransformControls>
```


## Fix the position 

Cube 에 있을 것으로 예상했던 위치를 처리 해 보자.
Cube 를 offset.

첫 번쨰 단순히 -> `position-x` 속성을 <mesh> 이동하는 것.

``` javascript
<TransformControls position-x={2}>
    <mesh scale={ 1.5 }>
        <boxGeometry />
        <meshStandardMaterial color="mediumpurple" />
    </mesh>
</TransformControls>
```


## PivotControls 

``` javascript
import {  PivotControls, TransformControls, OrbitControls } from '@react-three/drei'

<PivotControls>
    <mesh position-x={ -2 }>
        <sphereGeometry />
        <meshStandardMaterial color="orange" />
    </mesh>
</PivotControls>
```



## HTML 

Html -> 개체에 고정될 DOM 요소를 추가합니다.

``` javascript
<mesh position-x={ -2 }>
    <sphereGeometry />
    <meshStandardMaterial color="orange" />
    <Html>That s a sphere</Html>
</mesh>
```



## Class and style 

-> 스타일을 추가 할 수 있지만, CSS 에서 대상을 지정하기 위해 먼저 Class 를 추가할 것 입니다.

``` javascript
<Html
    position={ [1, 1, 0 ] }
    wrapperClass="label"
>
    That a sphere
</Html>
```

``` css
.label > div {
    position: absolute;
    background: #00000088;
    color: white;
    padding: 15px;
    white-space: nowrap;
    overflow: hidden;
    border-radius: 30px;
    user-select: none;
}
```


``` javascript
export default function Experience() {
    const sphere = useRef()

    return (
        <>
            <mesh ref={ sphere } position-x={ -2 }  />

            <mesh />
        </>
    )
}
```

-> `occlude` 속성에 배열을 보낼 수 있습니다.

``` javascript
<Html 
    position={ [1, 1, 0 ] }
    wrapperClass="label"
    center
    distanceFactor={ 8 }
    occlude={ [ sphere, cube ] } 
>
    That s a sphere
</Html>
```


## Text 

- 3D 텍스트를 만드는 방법을 알고 있지만, 3D 텍스트 Geometry 생성하는 데 한계가 있습니다.
    - 일부 글꼴은 보기에 좋지 않습니다.
    - 줄 바꿈을 지원하지 않습니다.
    - Geometry 생성에는 많은 GPU 리소스가 필요하며, 텍스트가 클수록 시간이 오래 걸립니다.



## SDF 글꼴 

글꼴의 경우 -> 수학적으로 계산할 수 없기 떄문에 훨씬 더 복잡합니다.
-> 글꼴에서 Texture 를 만드는 과정은 길고 복잡합니다.
다행히도 일부 개발자는 이미 `Troika` 라이브러리 -> `drei text` 도우미 해당 solution 을 구현하고 있습니다.



## 구현 

``` javascript
import { Text, Html, PrvotContorls, TransformControls, OrbitControls } from '@react-three/drei'

export default function Experience() {
    
    // ...
    return (
        <>
            <Text
                font="./bangers-v20-latin-regular.woff"
                fontSize={ 1 }
                color="salmon"
                position-y={ 2 }
                maxWidth={ 2 }
            >
            I Love R3F
            <meshNormalMaterial />
            </Text>
        </> 
    )
}
```


## Float 

-> 폰트를 풍선처럼 공중에 뜨거나 배 밖으로 뜨게 하려면 플로트 도우미를 사용할 수 있습니다.

``` javascript
import { Float, Text, Html, PivotControls, TransformControls, OrbitControls } from '@react-three/drei' 

<Float>
    <Text 
        font="./bangers-v20-latin-regular.woff"
        fontSize={ 1 }
        speed={ 5 } 
        color="salmon"
        position-y={ 2 }
        maxWidth={ 2 } 
        textAlign="center"  
        >
        I Love R3F
    </Text>
</Float>
```


## MeshReflectorMaterial 

WebGL 의 refraction 은 항상 복잡하지만, `MeshReflectorMaterial` 은 그렇지 않습니다.
`MeshReflectorMaterial` 다음에서 가져옵니다. -> `@react-three/drei`

``` javascript
<mesh position-y={-1} rotation-x={-Math.PI * 0.5 } scale={10}>
    <planeGeometry />
    <MeshReflectorMaterial />
</mesh>
```

