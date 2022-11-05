# Mouse Event

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
