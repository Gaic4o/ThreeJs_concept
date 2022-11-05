# 3D 텍스트

Three.js 환경에서 3D Text 를 만들어 봅시다.


Text3D 도우미

`TextGeometry` 를 사용해야 합니다.
운 좋게도 `drei` 에서 `Text3D` 구현하는 도우미를 제공합니다.
-> `Experience.js` 에서 `Text3D` 가져 옵니다.

``` javascript
export default function Experience() {
    return (
        <>
            <Perf position="top-left" />
            <OrbitControls makeDefault />

            <Text font="./fonts/helvetiker_regular.typeface.json">
                HELLO R3F
                <meshNormalMaterlal />
            </Text>
        </> 
    )
}
```


Centering 

Geometry 를 `drei-center` 도우미를 사용할 수도 있습니다.

``` javascript
import { Center, Text3D, OrbitControls } from '@react-three/drei'

<Center>
    <Text3D font="">
        HELLO R3F
        <meshNormalMaterial />
    </Text3D>
</Center>
```



Parameters 

`TextGeometry` 를 생성하는 데, 사용할 수 있는 모든 매개변수는 속성으로 설정할 수 있습니다.

``` javascript
<Center>
    <Text3D 
        font=""
        size={0.75}
        height={0.2}
        curveSegments={12}
        bevelEnabled
        bevelThickness={0.02}
        bevelSize={0.02}
        bevelOffset={0}
        bevelSegments={5}
        >
        HELLO R3F
        <meshNormalMaterial />
    </Text3D>
</Center>
```



Matcap Material 

이제 matcap 구현 차례 입니다.
`drei` 도우미를 사용할 것 입니다.

``` javascript
export default function Experience() {
    const matcapTexture = useMatcapTexture('7B5254_E9DCC7_B19986_C8AC91', 256)
    console.log(matcapTexture)
}
```

Texture 가 생겨나면 -> matcpaTexture 에 넣어주기.

``` javascript
<meshMatcapMaterial matcap={ matcpaTexture } />
```


도넛 

여러 도넛을 만들 떄는 (무작위로 배치) 
JSX 에서는 for 불가.

map 함수를 사용하기.

``` javascript
{ [...Array(100)].map((value, index) => {
    <mesh 
        key={ index } 
        position={ [
            (Math.random() - 0.5) * 10
            (Math.random() - 0.5) * 10
            (Math.random() - 0.5) * 10
        ] }
        scale={ 0.2 + Math.random() * 0.2 }
        rotation={ [ 
            Math.random() * Math.PI,
            Math.random() * Math.PI,
            0
        ] }
    >
        <torusGeometry args={ [ 1, 0.6, 16, 32 ] } />
        <meshMatcapMaterial matcap={ matcapTexture} />
    </mesh>
})}
```




## 최적화

- 최적화를 해 봅시다.


기하학

우린 2개 (도넛, 텍스트) 만 있어야 하지만, 많은 기하 도형을 가지고 있습니다. 
이 문제점을 해결하기 위해 3가지를 수행합니다.

- <torusGeometry> 를 도넛 외부헤 하나 만듭니다. (어떤 것과도 연결 x)
- useState 보관.
- <mesh> 에 다시 넣기.

``` javascript
const [toursGeometry, setToursGeometry] = useState()
```

``` javascript
<torusGeometry ref={ setToursGeometry } args={ [ 1, 0.6, 16, 32 ] }>
{ [...Array(100)].map((value, index) =>
    <mesh
        key={ index }
        geometry={ torusGeometry }
        position={ [
            (Math.random() - 0.5) * 10,
            (Math.random() - 0.5) * 10,
            (Math.random() - 0.5) * 10
        ] }
        scale={ 0.2 + Math.random() * 0.2 }
        rotation={ [
            Math.random() * Math.PI,
            Math.random() * Math.PI,
            0
        ] }
    >
        <meshMatcapMaterial matcap={ matcapTexture } />
    </mesh>
) }
```


재료 

텍스트와 도넛에 하나의 재료만 사용하도록 하자.

``` javascript
<torusGeometry ref={ setTorusGeometry } args={ [ 1, 0.6, 16, 32 ] } />
<meshMatcapMaterial matcap={ matcapTexture } />

<Center>
    <Text3D
        font=""
        size={ 0.75 }
        height={ 0.2 }
        curveSegments={ 12 }
        bevelEnabled
        bevelThickness={ 0.02 }
        bevelSize    
    >
    
    </Text3D>
</Center>
```