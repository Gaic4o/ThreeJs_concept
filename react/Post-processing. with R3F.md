# Post-processing with R3F

Implement

-> @react-three/postprocessing, postprocessing 

``` javascript
import { EffectComposer } from '@react-three/postprocessing'

export default function Experience() {
    return (
        <>
            <EffectComposer>
            </EffectComposer>
        </>
    )
}
```


Multisampling 

다양한 속성을 설정할 수 있는 <EffectComposer> -> `multisample`

``` javascripts{ 0 }>
</EffectComposer>
```


Vignette effect 

Vignette 효과는 -> renderer 의 모서리를 조금 더 어둡게 만듭니다.

``` javascript
import { Vignette, EffectComposer } from ''

<EffectComposer>
    <Vignetter 
        offset={ 0.3 }
        darkness={ 0.9 }
    />
</EffectComposer>
```


Blending 

blendFunction Vignette 내에 사용할 수 있는 이름이 지정된 특수 속성이 있지만, 다른 모든 효과에서도 사용 가능 합니다.
`npm install postprocessing@6.28` 

``` javascript
import { BlendFunction } from 'postprocessing'
console.log(BlendFunction)

<Vignette 
    offset={0.3}
    darkness={0.9}
    blendFunction={ BlendFunction.COLOR_BURN}
/>
```



Background bug 

vignette effect 는 배경에서 작동하지 않습닏가.
-> rendering 은 기본적으로 투명하고 거기에는 렌더링할 것이 없기 떄문입니다. (`색상을 추가하여 이 문제를 해결 가능 합니다.`)

``` javascript
export default function Experience() {
    return (
        <>
            <color args={ [ 'ffffff' ] } attack="background" />
        </>
    )
}
```


Glitch effect 

-> 영화의 해킹 장면과 같이 무작위로 화면 Glitch 를 만듭니다.

``` javascript
<EffectComposer multisampling={ 4 }>
    <Glitch 
        delay={ [ 0.5, 1 ] } 
        duration={ [ 0.1, 0.3 ] }
        strength={ [ 0.2, 0.4 ] } 
        />
</EffectComposer>
```


Noise effect 

화면에 노이즈 효과도 줄 수 있습니다.

``` javascript
<EffectComposer multisampling={ 4 }>
    <Noise />
</EffectComposer>
```