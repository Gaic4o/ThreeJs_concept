# Fun and Simple Portfolio

`@react-three/drei` 사용하여 `OrbitControls` 이용하여 Camera 를 이동시킬 수 있습니다.
일단 배경을 만들어 보자.

``` javascript
export default function Experience() {
    return (
        <>
            <color args={ [ '#695b5b' ] } attach="background" />
        </>
    )
}
```

Implement the model 

``` javascript
import { useGLTF, OrbitControls } from '@react-three/drei'

export default function Experience() {
    const computer = useGLTF('')

    return (
        <>
            <primitive object={computer.scene} />
        </>
    )
}
```



Light and environment 

``` javascript
import { Environment, useGLTF, OrbitControls } from '@react-three/drei'

export default function Experience() {
    return <>
        <Environment preset="city" />
    </>     
}
```


Floating animation 

노트북 -> position 값을 약간 회전 시키자.

``` javascript

<Float>
    <primitive  
        object={ computer.scene }
        position-y={ -1.2 }
    />
</Float>
```

Float 을 이용하여 애니메이션을 주는 것도 좋지만...
-> 화면이 너무 많이 움직이면, 웹사이트 방문자가 상호 작용을 할 수 없게 만듭니다.

`rotationIntensity` -> 을 줘서 회전 강도를 줄입니다. 

``` javascript
<Float rotationIntensity={ 0.4 }>
    <primitive
        object={ computer.scene }
        position-y={ -1.2 }
    />
</Float>
```


카메라 및 컨트롤 

`drei 의 OrbitControls` 를 사용하고 있으면, 모델 전체를 회전할 수 있습니다.
`OrbitControls` 속성을 조정하는 대신 `drei` 도우미를 사용할 것. -> `PresentationControls`

`PresentationControls` 카메라 대신 model 를 조작할 수 있습니다. -> 모델을 회전 시키거나, 누르면 초기 위치로 돌아갑니다.


``` javascript
import { PresentationControls, Float, Environment, useGLTF, OrbitControls } from '@react-three/drei'

export default function Experience() {
    // ...

    return (
        <>
            <color args={ [ '#695b5b' ] } attach="background" />
            <Environment preset="city" />

            <PresentationControls>
                <Float rotationIntensity={ 0.4 }>
                    <primitive
                        object={ computer.scene }
                        position-y={ -1.2 }
                    />
                </Float>
            </PresentationControls>
            <>
        </>
    )
}
```




Global

-> 기본 모델에서 오른쪽으로 끌어다 놓는 방법으로 개체를 회전 시킬 수 있습니다.

``` javascript
<PresentationControls 
    global 
>
</PresentationControls>
```



회전 

`rotation` 속성을 사용하여 기본 회전을 변경할 수 있습니다.

``` javascript
<PresentationControls
    global
    rotation={ [ 0.13, 0.1, 0 ] }
>
    {/* */}
</PresentationControls>
```


Limits
 
-> 수평 및 수직으로 회전을 제한할 수 있습니다.
수직의 이름이 지정 `polar` 되고, 최소값과 최대값의 두 값 배열을 보내야 합니다.

``` javascript
<PresentationControls
    global
    rotation={ [ 0.13, 0.1, 0 ] }
    polar={ [ -0.4, 0.2 ] }
>
    {/* */}
</PresentationControls>
```


Spring configuration

- 애니메이션의 물리적 특성을 조정하고 싶습니다.
`Spring PresentationControls` 스프링의 속성으로 재생할 수 있습니다.

``` javascript
<PresentationControls
    global
    rotation={ [ 0.13, 0.1, 0 ] }
    polar={ [ -0.4, 0.2 ] }
    azimuth={ [ -1, 0.75] }
    config={ [ mass: 2, tension: 400 ] }
>

</PresentationControls>
```




Shadow 

장면에 사실감을 더하기 위해 Shadow 를 추가합니다.
-> 다양한 옵션을 사용할 수 있지만, 간단하게 유지하고 `ContactShadows` from `drei` 를 사용하겠습니다.
먼저 다음에서 가져 `ContactShadows` -> `@react-three/drei`

``` javascript
import { ContactShadows, PresentationControls, Float, Environment, useGLTF } from '@react-three/drei'

export default function Experience() {
    return (
        <PresentationControls
            global
            rotation={ [ 0.13, 0.1, 0 ] }
            polar={ [ -0.4, 0.2 ] }
            azimuth={ [ -1, 0.75] }
            config={ { mass: 2, tension: 400 } }
            snap={ { mass: 4, tension: 400 } }
        >
        {/* */} 
        </PresentationControls>
        <ContactShadows position-y={ -1.4 } />
    )
}
```





## iframe 

HTML/CSS 웹사이트를 추가할 차례 입니다.


HTML/CSS 웹사이트.

먼저 HTML/CSS 웹사이트가 필요합니다.

``` javascript
import { Html, ContactShadows, PresentationControls, Float, Environment, useGLTF } from '@react-three/drei'

<primitive
    object={ computer.scene }
    position-x={ - 1.2 }
    rotation-x={ 0.13 }
>
    <Html>
        <iframe src="https://bruno-simon.com/html/" />
    </Html>
</primitive>
```



Size 

이제 내부 콘텐츠를 보기 좋게 만들 크기를 결정 해야 합니다. -> `iframe 으로.`
iframe 에 적절하게 맞기를 원할 뿐 입니다.

콘텐츠를 사용하여 -> `wrapperClass` 속성을 추가 합니다. 

``` javascript
<Html
    transform
    wrapperClass="htmlScreen"
    distanceFactor={ 1.17 }
    position={ [ 0, 1.56, -1.4 ] }
    rotation-x={ -0.256 }
>
    {/* */}
</Html>
```

``` javascript
.htmlScreen iframe {
    width: 1024px;
    height: 670px;
    border: none;
    border-radius: 20px;
}
```



Screen light 

-> 랩톤 키보드에 반사된 빛을 볼 수 잇도록 화면 위치에 주황색 `RectAreaLight` 를 추가하여 더욱 선명도를 더할 것 입니다.

``` javascript
<Float rotationIntensity={ 0.4 } >
    <rectAreaLight
        width={ 2.5 }
        height={ 1.65 }
        intensity={ 65 }
        color={ '#ff6900' }
        rotation={ [ -0.1, Math.PI, 0 ] }
        position={ [ 0, 0.55, -1.15 ] }
    />
</Float>
```