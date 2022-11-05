# Load models with R3F

-> <Pref /> -> `r3f-perf`


Clean up

-> Shadow 작동하는 지 확인하기 위해 존재했떤 `Experience.js` 와 `<mesh>` 큐브를 제거하고 시작 해 봅시다.

``` javascript
export default function Experience() {
    return (
        <>
            <Pref position="top-left" />

            <OrbitControls makeDefault />
            
            <directionalLight castShadow position={ [ 1, 2, 3 ] } intensity={ 1.5 } />
            <ambientLight intensity={ 0.5 } />

            <mesh receiveShadow position-y={ -1 } rotation-x={ -Math.PI * 0.5 } scale={ 10 }>
                <planeGeometry />
                <meshStandardMaterial color="greenyellow" />
            </mesh>
        </>
    )
}
```



## Loading a model 


하나는 `DRACO` 압축 (`hamburger-draco.glb`) 이고, 다른 하나 (`hamburger.glb`) 는 압축되지 않습니다. 
R3F 는 `useLoader` Loading 이라는 이름의 Hook 을 제공합니다.

`Experience.js` 가져 -> `useLoader` 가져오기.

``` javascript
import { useLoader } from '@react-three/fiber'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'

export default function Experience() {
    const model = useLoader(GLTFLoader, './hamburger.glb')
    console.log(model)
    // ...
}
```

<primitive> 을 -> model.scene 에 추가합니다.
scale 로 -> 크기를 줄일 수 있습니다.

``` javascript
export default function Experience() {
    const model = useLoader(GLTFLoader, './hamburger.glb')

    return (
        <>
            <primitive object={ model.scene } scale={ 0.35 } />
        </>
    )
}
```



## DRACO 

``` javascript
const model = useLoader(GLTFLoader, './hamburger-draco.glb')
```

DRACO 압축 모델은 -> 로드하려면 에러가 발생합니다.
-> DRACOLoader Class 를 인스턴스하고 `GLTFLoader` 인스턴스를 추가해야 합니다.

``` javascript
import { DRACOLoader } from 'three/examples/jsm/loaders/DRACOLoader.js'

const model = useLoader(
    GLTFLoader,
    './hamburger-draco.glb',
    (loader) => {
        const dracoLoader = new DRACOLoader()
        dracoLoader.setDecoderPath('./draco/')
        console.log(loader)
    }
)
```



## 지연 로딩

햄버거 모벨이 상당히 가볍고, 서버를 로컬에서 실행하고 있어서 그렇지만,
현재 R3F 는 -> `Experience` 장면에서 모든 것이 준비되지 않은ㄴ 한 렌더링 보류하고 있습니다.


더 큰 모델 로드

`이를 보다 명확하게 보기 위해 사실적 렌더링` -> 비행 헬멧으로 전환해보자.

``` javascript
const model = useLoader(
    GLTFLoader,
    './FlightHelmet/glTF/FlightHelmet.gltf',
    (loader) => {
        const dracoLoader = new DRACOLoader()
        dracoLoader.setDecoderPath('./draco/')
        loader.setDRACOLoader(dracoLoader)
    }
)
```


네트워크 조절

-> 이제 Load 하는 데, 시간이 걸리는 모델로 실제 경험을 시뮬레이션하기 위해 Network 조절을 활성화할 수 있습니다.



Suspence
 
지연 로딩 -> React Suspence 태그를 사용하면 쉽게 처리할 수 있습니다.

``` javascript
<Suspence>
    <Model />
</Suspence>
```


Suspence 를 대체를 설정할 수 있는 기능이 있습니다.
대체는 구성 요소가 준비되지 않은 경우(Model 이 로드 되는 동안) -> 사용자에게 표시 됩니다.
`fallback` 속성을 사용할 수 있습니다.

``` javascript
<Suspence 
    fallback={ <mesh position-y={ 0.5 } scale={ [ 2, 3, 2 ] } ><boxGeometry args={ [1, 1, 1, 2, 2, 2 ] } />
   <meshBasicMaterial wireframe color="red" /></mesh> } 
>
```

좀 더 코드를 축약 해 보자.


``` javascript
export default function Placeholder() {
    return (
        <>
            <mesh position-y={ 0.5 } scale={ [ 2, 3, 2 ] }>
                <boxGeometry args={ [ 1, 1, 1, 2, 2, 2 ] } />
                <meshBasicMaterial wireframe color="red" />
            </mesh>
        </mesh>
    )
}
```

--> 

``` javascript
<Suspence fallback={ <Placeholder /> } >
``` 






drei 를 사용한 GLTF 로딩 

`Drei` useGLTF 는 여러 로더 도우미를 구현 합니다.

``` javascript
import { useGLTF } from '@react-three/drei'

export default function Model() {
    const model = useGLTF('./hamburger.glb')
}
```

useLoader, GLTFLoader -> 가져오기 제. `DRACOLoader` 압축 버전을 어떻게 처리해야 할까?
useGLTF 에서는 DRACO 디코더를 제공할 필요는 없습니다.

``` javascript
const model = useGLTF('./hamburger-draco.glb')
```





Preloading 

-> 우리 모델은 구성 요소가 인스턴스화 될 떄만 로드를 시작합니다.
`Experience` 조건 없이 직접 모델을 추가했으므로 모델이 바로 load 되기 시작합니다.

``` javascript
export default function Hamburger({ ...props }) {
    // ...

}
useGLTF.preload('./hamburger-draco.glb')
```




Multiple instances 

-> Model.js Clone 가져오기.
`<primitive>` `<Clone>` 동일 속성으로 바꿉니다.

``` javascript
import { Clone, useGLTF } from '@react-three/drei'

<Clone object={ model.scene } scale={ 0.35 } position-x={ -4 } />
<Clone object={ model.scene } scale={ 0.35 } position-x={ 0 } />
<Clone object={ model.scene } scale={ 0.35 } position-x={ 4 } />
```


구성 요소에 대한 GLTF 

햄버거의 다른 부분을 조작하려면? -> 로드된 모델을 탐색 후 올바른 자식을 검색 하고, 어떤 방식으로든 저장하고 필요한 모든 것을 적용해야 합니다.

GLTF -> React THREE Fiber 하는 일.
