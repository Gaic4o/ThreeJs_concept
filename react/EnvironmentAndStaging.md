# Environment and Staging 

## 배경색


CSS 로.

``` css
html,
body,
#root {
    position: fixed; 
    top: 0;
    left: 0;
    background: red;
}
```


Renderer 에서 setClearColor 사용.

`WebGLRenderer` 이라는 메소드가 있습니다.
<canvas> 에서 다양한 장면들을 렌더링 하기 전에 -> 색상으로 먼저 채워 줍니다.

`setClearColor` 로 색상을 첫 번째 매개변수, 두 번재 매개변수 알파 를 호출 할 수 있습니다. 

``` javascript
const created = () => {
    console.log('created')
    gl.setClearColor('#ff0000', 1)
}

root.render(
    <Canvas 
        camera={ { 
            fov: 45,
            near: 0.1,
            far: 200,
            position: [ -4, 3, 6 ]
        } }
        onCreated={ created }
    >
        <Experience />
    </Canvas>
)
```


장면 배경으로 

``` javascript
import * as THREE from 'three'

const created = ({ scene }) => {
    scene.background = new THREE.Color('#ff0000')
}
```



R3F 색상으로

-> Event 를 수신하고, Three 를 가져오는 대신, JSX 에서 직접 색상을 생성할 수 있습니다.
`THREE` 먼저 가져오고, `created` 함수 및 `onCreated` 속성을 정리하기.

``` javascript
import './style.css'
import ReactDOM from 'react-dom/client'
import { Canvas } from '@react-three/fiber'
import Experience from './Experience.js'

const root = ReactDOM.createRoot(document.querySelector('#root'))

root.render(
    <Canvas 
        camera={ { 
            fov: 45,
            near: 0.1,
            far: 200,
            position: [-4, 3, 6]
        } }
    >
    <color args={ [ '#ff0000' ] } attack="background" />
    <Experience />
    </Canvas>
)
```



## 불 

Three.js 조명은 R3F 에서 지원됩니다.

- ambientLight
- hemisphereLight
- directionalLight
- pointLight
- rectAreaLight
- spotLight


Light Helpers 

Three.js 에서 Light Helpers 를 사용할 수 있습니다.
useHelper - 광원에 대한 참조이고, 두 번째 매개변수는 Three.js 사용하려는 도우미 클래스 입니다.

``` javascript
export default function Experience() {
    const directionalLight = useRef()
    useHelper(directionalLight, THREE.DirectionalLightHelper, 1)
}
```



## Shadows 

장면을 보다 사실적으로 만들려면 -> 그림자가 필요합니다.



Default shadows 

그림자 렌더링 할려면 -> `WebGLRenderer` 에 추가 해 주면 됩니다. 

``` javascript
root.render(
    <Canvas 
        shadows
        camera={ { 
            fov: 45,
            near: 0.1,
            far: 50,
            position: [ -4, 3, 6 ]
        } }
    >
        <Experience />
    </Canvas>
)
```

그리고 나서 `Experience.js` castShadow 를 추가 합니다.

``` javascript
<directionalLight ref={ directionalLight } castShadow position={ [1, 2, 3] } intensity={ 1.5} />
```

`castShadow` 와 <mesh> 추가 합니다. (이러한 개체는 위에 아무것도 없으므로, 그림자만 draw 하면 됩니다.)

``` javascript
<mesh castShadow position-x={ -2 }>
    {/* */}
</mesh>
```

바닥에 추가 `receiveShadow` 합니다. (바닥에는 아래에 아무것도 없기 떄문에, 그림자만 받으면 됩니다.)

``` javascript
<mesh receiveShadow position-y={ - 1 } >
    {/* */}
</mesh>
```




Baking 

- 장면이 정적인 경우 (큐브가 회전하기 떄문에) -> `BakeShadows` 도우미를 추가할 수 있습니다.
그림자가 각 프레임이 아닌 한 번만 렌더링 됩니다.

``` javascript
import { BakeShadows, useHelper, OrbitControls } from '@react-three/drei'

export default function Experience() {
    return (
        <>
            <BakeShadows />
        </>     
    )
}
```



Configuring the shadows 

그림자를 조정 할 수 있습니다.
Shadow Map 이라고 부르는 특정 방식과 출력으로 렌더링 합니다. 

`directionalLight.shadow.mapSize.set(1024, 1024)`

``` javascript
<directionalLight
    ref={ directionalLight }
    position={ [ 1, 2, 3] }
    intensity={ 1. 5 }
    castShadow 
    shadow-mapSize={ [ 1024, 1024 ] } 
/>
```

near, far, top, right, bottom, left -> 속성을 사용할 수 있습니다.

``` javascript
<directionalLight
    ref={ directionalLight }
    position={ [ 1, 2, 3] }
    intensity={ 1. 5 }
    castShadow 
    shadow-mapSize={ [ 1024, 1024 ] } 
    shadow-camera-near={1}
    shadow-camera-far={10}
    shadow-camera-top={2}
/>
```


Soft shadows 

기본 그림자는 너무 선명합니다. -> 부드럽게 하는 방법이 있습니다. (PCSS (Percent Closer Soft Shadows ) 라는 하나의 기술.

``` javascript
import { softShadows, BakeShadows, useHelper, OrbitControls } from '@react-three/drei'

softShadows({
    frustum: 3.75;
    size: 0.005;
    near: 9.5;
    sampels: 17,
    rings: 11
})

export default function 
```




Accumulative Shadows 

`AccumulativeShadows` 그림자 렌더링을 누적 뒤, 각 렌더링 전에 조명을 무작위로 이동할 것 입니다.
-> 그림자가 다양한 각도의 여러 렌더로 구성되어 부드럽고 매우 사실적으로 보입니다.

``` javascript
<mesh position-y={ -1 } rotation-x={ -Math.PI * 0.5 } scale={ 10 }>
    {/* */}
</mesh>
```



Contact Shadows 

-> 마지막 그림자는 `ContactShadows` 입니다.
Three.js 기본 Shadows System 에 의존 x -> shadow 를 비활성화 합니다. 

``` javascript
<Canvas
    shadows={ false } 

>
    <Experience />
</Canvas>
```


