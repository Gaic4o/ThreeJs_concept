# Debug 

React 와 React Three Fiber 모두에서 개발자 생활을 최대한 쉽게 만드는 것이 중요하므로, 디버깅, 프레임 속도가 갑자기 떨어지는 이유로 -> 애플리케이션에 집중 할 수 있습니다.


## StrickMode 

React Three 프로젝트엣 꼭 추가해야 하는 것이 있습니다. 그것이 -> `StrickMode`

- Unused import
- Infinite render loop
- Forgotten useEffect dependencies
- Deprecated practices
- Etc 


``` javascript
import { StrickMode } from 'react'

root.render(
    <StrickMode>
        <Canvas
            camera={ { 
                fov: 45,
                near: 0.1,
                far: 50,
                position: [ -4, 3, 6 ]
            } }
        >
            <Experience />
        </Canvas>
    </StrickMode>
)
```


## Browser extendsion 

-> React Developer Tools 확장을 설치할 수 있습니다.
Chrome, Firefox 모두에서 사용 가능.

React 애플리케이션에서 Component Tab 으로 볼 수 있습니다.


## Leva 로 UI 디버그

``` javascript
import { useControls } from 'leva'
```

첫 번쨰 조정

``` javascript
export default function Experience() {
    const controls = useControls({ position: - 2 })
    console.log(controls.position)
}

<mesh position-x={position}>
```



## Range 

특정 속성을 가진 객체를 다음으로 전송하여 -> 수행할 수 position 있습니다.

``` javascript
const { position } = useControls({
    position: {
        value: -2,
        min: -4,
        max: 4,
        step: 0.01,
    }
})
```


## Vectors 

y축에서도 동일 수행 하려면 어떻게 해야 할가요?

``` javascript
const { position } = useControls({
    position: {
        value: { x: -2, y: 0 },
        step: 0.01 
    }
})

<mesh position={ [ position.x, position.y, 0 ] }>
```

z축도 가능 합니다.

``` javascript
const { position } = useControls({
    position: {
        value: { x: -2, y: 0, z: 0 },
        step: 0.01
    }
})

<mesh position={ [position.x, position.y, position.z ] }>
```


-> 3D 조이스틱이 그리 편리하지 않기 떄문에 의마기 있는 조이스틱으 잃어버림?
-> vector2 조정으로 돌아가 보기.

``` javascript
const { position } = useControls({
    position: {
        value: { x: -2, y: 0 },
        step: 0.01,
        joystick: 'invertY'
    }
})
```


## color 

Color 도 가능 합니다.

``` javascript
const { position, color } = useControls({
    color: '#ff0000'
})

<meshStandardMaterial color={color} />
```

다양한 색상 형식을 사용할 수 있습니다.

``` javascript
`rgb(255, 0, 0)`
`orange`
`hsl(100deg, 100%, 50%)`
`hsla(100deg, 100%, 50%, 0.5)`
`{ r: 200, g: 106, b: 125, a: 0.4 }`
```


## Boolean 

``` javascript
const { position, color, visible } = useControls({
    visible: true 
})

<mesh visible={visible} position={ [ position.x, position.y, 0 ] }>
```


## Interval

장면에서 사용하지 않을 것 이지만, 2개의 cursor 간격을 조절 설정 할 수 있습니다.

``` javascript
const { position, color, visible } = useControls({
    myInterval: {
        min: 0,
        max: 10,
        value: [4, 5]
    }
})
```



## Button 

-> 장면에서 사용하지 않을 것이지만, 클릭하면 함수를 호출하는 버튼을 만들 수 있습니다.

``` javascript
import { button, useControls } from 'leva'

const { position, color, visible } = useControls('sphere', {
    clickMe: button(() => { console.log('ok' ) } )
})
```


## Select 

-> `options` 속성에 배열을 설정하여 선택 입력을 만들 수 있습니다.

``` javascript
const { position, color, visible } = useControls('sphere', {
    choice: { options: [ 'a', 'b', 'c' ] }
})
```


## Folders 

-> `lii-gui` 와 마찬가지, 너무 많은 조정으로 인해 상황이 너무 복잡해지면 폴더로 정리할 수 있습니다.

``` javascript
const { position, color, visible } = useControls('sphere', {
    position: {
        value: { x: -2, y: 0 },
        step: 0.01,
        joystick: 'invertY'
    },
    color: 'orange',
    visible: true 
})
```


## Configuration 

먼저, 속성을 조정할 수 있도록 application 에 component 를 직접 추가해야 합니다.
`Experience` 내부에 `<Canvas>` 있고 모든 것이 `<canvas>` R3F 를 위한 것이기 떄문에 추가할 수 없습니다.

-> 대신, 우리는 그것을 index.js 외부 파일에 추가해야 합니다.

``` javascript
import { Leva } from 'leva' 

root.redner(
    <StrickMode>
        <Leva />
        <Canvas
            camera={ { 
                fov: 45,
                near: 0.1,
                far: 50,
                position: [ -4, 3, 6 ]
            } }
        >
        <Experience />
        </Canvas>
    </StrickMode>
)
```



## r3f-perf 로 모니터링

터미널에서 `r3f-perf` 추가합니다.

``` javascript
import { Rerf } from 'r3f-pref' 

export default function Experience() {
    // ...
    return (
        <>
            <Pref position="top-left" />
        </> 
    )
}
```

R3F-Pref 엄청난 양의 유용한 정보를 표시 합니다.
-> 호출 수, 메모리 사용량, 장면을 렌더링하는 데 걸린 시간 등을 액세스 할 수 있습니다.

``` javascript
const { perfVisible } = useControls({
    prefVisible: true 
})

{ prefVisible && <Pref position="top-left" /> }
```

