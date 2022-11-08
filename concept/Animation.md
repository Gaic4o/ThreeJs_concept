# Animation

## requestAnimationFrame 사용.

requestAnimationFrame 은 Javascript 내장 함수 입니다.
이 code 를 실행시키미녀, 1초가 지날 떄 마다 console.log 계속해서 출력합니다.
-> `문제는 연속해서 루프가 생성되므로, Maximum callstack 이 발생하는 문제 입니다. -> 화면이 문제가 발생할 수 있습니다.`

``` javascript
/**
 * Animation 
 */ 
const tick = () => {
    console.log(tick); // 매초 마다 tick 가 출력 됩니다.
    window.requestAnimationFrame(tick); 
}
tick();
```

window.requestAnimationFrame 을 사용하여 -> mesh 를 rotation, position, scale 등 다양하게 사용 할 수 있습니다.

``` javascript
const animation = () => {
    
    // Update Object Rotation 
    mesh.rotation.y = 0.01

    // Renderer
    renderer.render(scene, camera) 

    // Call animation function 
    window.requestAnimationFrame(animation)
}
animation()
```

requestAnimationFrame 의 문제는 -> frame speed 높은 컴퓨터에서는 mesh 가 더 빠르게 동작하고, speed 가 낮은 컴퓨터에서는 mesh 가 느리게 동작합니다. 



## Frame speed 에 대한 적응.

일단 Animation 프레임 속도에 맞게 조정하려면? 마지막 tick 이후 시간이 얼마나 흘렀는 지 알아야 합니다.
먼저 frame 속도를 측정하는 방법이 필요합니다.

`Date.now()` 기본 Javascript 현재 타임스탬프를 가져오는 데 사용할 수 있습니다.

이전 프레임 타임스탬프에서 현재 스탬프를 뺴 호출할 수 있는 값을 얻고 `deltaTime` 객체에 애니메이션을 적용할 떄 이 값을 사용하는 것 입니다.

``` javascript
let time = Date.now()

const animation = () => {
    
    // Time
    const currentTime = Date.now()
    const deltaTime = currentTime - time 
    time = currentTime 

    // Update objects 
    mesh.rotation.y += 0.01 * deltaTime 

    // ...
}
animation();
```

이제는 마지막 frame 이후 소요된 시간을 기준으로 회전하므로, 컴퓨터 프레임 속도가 느리던 빠르던 속도에 관계없이 모든 컴퓨터에서 동일하게 적용 됩니다.



## THREE.Clock 사용.

Date.now() 를 사용할 순 있지만, 조금 사용하기는 복잡합니다.
Three 내장 함수 중 Clock 이라는 함수가 있습니다. (시간을 계산하는 Clock 이라는 내장 함수)

Clock 변수를 선언하고, `getElapsedTime()` 함수를 호출해 주기만 하면 -> Clock 생성된 후 몇 초가 지난는지 반환 됩니다.

``` javascript
const clock = new THREE.Clock()

const animation = () => {
    const elapsedTime = clock.getElapsedTime()

    mesh.rotation.y = elapsedTime 
}

animation()
```

정리 하자면..
Javascript RequestAnimationFrame 을 사용할 순 있지만 -> frame speed, GPU 점유율에 따라 어떤 것은 빠르게, 어떤 것은 느리게 동작 할 수 있습니다.
그러므로 -> Date.now() Javascript 내장 함수를 사용하거나, THREE.Clock (getElapsedTime) 를 사용할 수 있습니다.

