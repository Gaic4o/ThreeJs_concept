# Mouse Event with R3F 

Listening to click events 

마우스 이벤트 하는 것은 Javascript 바닐라로 사용할 떈 약간 까다로웠습니다.
Click Event 하는 것부터 시작 합시다.

``` javascript
export default function Experience() {
    // ...
    const eventHandler = () => {
        console.log('the event occured')
        cube.current.material.color.set(`hsl(${Math.random() * 360}, 100%, 75%)`)
    }

    return (
        <mesh ref={ cube } position-x={ 2 } scale={ 1.5 } onClick={ eventHandler } >
            <boxGeometry />
            <meshStandardMaterial color="mediumpurple" />
        </mesh>
    )
}
```


Event information

함수에 인자를 추가하여 event 관련 정보 access 할 수 있습니다.

``` javascript
const eventHandler = (event) => {
    console.log(event)
    cube.current.material.color.set(`hsl(${Math.random() * 360}, 100%, 75%)`)
}

console.log('---')
console.log('distance', event.distance) // Distance between camera and hit point
console.log('point', event.point) // Hit point coordinates (in 3D)
console.log('uv', event.uv) // UV coordinates on the geometry (in 2D)
console.log('object', event.object) // The object that triggered the event
console.log('eventObject', event.eventObject) // The object that was listening to the event (useful where there is objects in objects)

console.log('---')
console.log('x', event.x) // 2D screen coordinates of the pointer
console.log('y', event.y) // 2D screen coordinates of the pointer

console.log('---')
console.log('shiftKey', event.shiftKey) // If the SHIFT key was pressed
console.log('ctrlKey', event.ctrlKey) // If the CTRL key was pressed
console.log('metaKey', event.metaKey) // If the COMMAND key was pressed
```




Occluding 

큐브 2개가 있고, 카메라를 이동하여 큐브를 겹치게 만든 다음.
뒤에 있는 것을 클릭했는데 이벤트가 console.log 출력되는 것을 볼 수 있습니다.

-> `event.stopPropagation()` 를 호출 해 주면 됩니다.

``` javascript
<mesh position-x={ -2 } onClick={ ( event ) => event.stopPropagation() }>
    {/* */}
</mesh>
```



Cursor 

Cube 에 Cursor 를 만들어 줍시다.
Cube 객체를 Cursor 로 클릭할 수 있도록 마우스 버튼을 만들어 줍니다.
-> onPointerEnter, onPointerLeave

``` javascript
<mesh
    ref={ cube }
    position-x={ 2 }
    scale={ 1.5 }
    onClick={ eventHandler } 
    onPointerEnter={ () => { document.body.style.cursor = 'pointer' } }
    onPointerLeave={ () => { document.body.style.cursor = 'default' } } 
>
    <boxGeometry />
    <meshStandardMaterial color="mediumpurple" />
</mesh>
```

주의 할 점은 -> onPointerEnter, onPointLeave 에서도 겹친 상황에서 마우스 cursor 버튼이 클릭 되도록 하면 안되니까 -> `stopPropagation()` 를 사용해 주어야 합니다.

``` javascript
<mesh 
    position-x={ 2 }
    onClick={ (event) => event.stopPropagation() }
    onPointerEnter={ (event) => event.stopPropagation() } 
>
    <sphereGeometry />
    <meshStandardMaterial color="orange" />
</mesh>
```




Events on complex objects 

여러 개체로 구성된 항목에 click 를 테스트 하면 어떻할까?
햄배거로 테스트 해봅시다.

``` javascript
import { useGLTF, OrbitControls } from '@react-three/drei'

export default function Experience() {
    // ...
    const hamburger = useGLTF('./hamburger.glb')

    return (
        <>
            <primitive
                object={ hamburger.scene }
                scale={ 0.25 }
                position-y={ 0.5 }
                onClick={ ( event ) => {
                    console.log('click')
                    event.stopPropagation()
                } }
            />
        </>
    )
}
```


