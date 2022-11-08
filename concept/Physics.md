# Physics 

Javascript 여러 물리 엔진 중 Canon.js 를 사용합니다.

``` javascript
import CANNON from 'cannon'
```


먼저 CANNON 를 사용하기 전에 World 를 호출 해주어야 합니다.

``` javascript
/**
 * Physics
 */
const world = new CANNON.World()

world.gravity.set(0, -9.82, 0)
```

장면에는 이미 Mesh 가 생성되어 있지만, -> Cannon.js World 에서도 Material 를 생성 해 주어야 합니다.

``` javascript
const sphereShape = new CANNON.Sphere(0.5)
const sphereBody = new CANNON.Body({
    mass: 1,
    position: new CANNON.Vec3(0, 3, 0),
    shape: sphereShape 
})

world.addBody(sphereBody)
```


## Cannon.js 및 Three.js 장면 업데이트.

60fps 실행되기를 원하므로 -> 1 / 60.
-> 프레임이 높거나 낮거나 동일 속도로 작동합니다.

``` javascript
const clock = new THREE.Clock()
let oldElapsedTime = 0

const tick = () => {
    const elapsedTime = clock.getElapsedTime()
    const deltaTime = elapsedTime - oldElapsedTime
    oldElapsedTime = elapsedTime 

    // Update physics 
    world.step(1 / 60, deltaTime, 3)
}

sphere.position.x = sphereBody.position.x
sphere.position.y = sphereBody.position.y
sphere.position.z = sphereBody.position.z 
```


구가 밑으로 떨어지는 데, 바닥에서 붙이고 멈춰야 하는 데, 
바닥에 깔리는 게 아니라, 바닥을 무시하고 추락합니다.
-> 왜냐? Three.js 세계에서는 존재하지만, Cannon.js 세계에서는 없기 떄문입니다.

``` javascript
const floorShape = new CANNON.Plane()
const floorBody = new CANNON.Body()
floorBody.mass = 0
floorBody.addShape(floorShape) 
world.addBody(floorBody)
```

하지만 공이 바닥으로 떨어지는 게 아닌, -> 카메라 방향으로 구가 이동합니다.
Three.js 에서 바닥을 회전한 것처럼 회전해야 합니다.

Cannon.js 사용한 회전은 `Quaternion` 을 사용해야 하기 떄문에, Three.js 보다 약건 더 어렵습니다.

``` javascript
floorBody.quaternion.setFromAxiosAngle(new CANNON.Vec3(-1, 0, 0), Math.PI * 0.5)
```


## Contact material 

보시다시피 공이 튀기지 않습니다. -> 재질을 만들어 줘야 합니다.
구와 바닥의 재질을 만들어 줍니다. 

``` javascript
const concreteMaterial = new CANNON.Material('concrete')
const plasticMaterial = new CANNON.Material('plastic')
```

-> friction 계수 (얼마나 문지르는가)
-> restitution 계수 (얼마나 바운스) 

``` javascript
const concretePlasticContactMaterial = new CANNON.ContactMaterial(
    concreteMaterial,
    plasticMateral,
    {
        friction: 0.1,
        restitution: 0.7
    }
)
world.addContactMaterial(concretePlasticContactMaterial)
```

``` javascript
const sphereBody = new CANNON.Body({
    material: plasticMateral
})

const floorBody = new CANNON.Body()
floorBody.material = concreteMaterial 
```



## Apply forces 

Body 에 힘을 적용하는 방법에는 여러 가지가 있습니다.
도미노를 민다고 생각하면 쉬움.

`applyImpulse, applyForce` 와 비슷하지만, 속도를 변경하는 힘을 추가하는 대신 속도에 직접 적용합니다.
`applyLocalForce, applyForce` 동일 하지만, 좌표는 Body 에 대해 local 입니다. 
`applyLocalImpulse, applyImpulse` 와 동일 하지만, 좌표는 Body 에 대해 local 입니다.

``` javascript
sphereBody.applyLocalForce(new CANNON.Vec3(150, 0, 0), new CANNON.Vec3(0, 0, 0));

const tick = () => {
    // Update physics 
    sphereBody.applyFoce(new CANNON.Vec3(-0.5, 0, 0), sphereBody.position)
    world.step(1 / 60, deltaTime, 3
    )
}
```



## 여러 개체 처리


기능으로 자동화.

-> Three.js, Cannon.js 모두 추가하는 함수로 구를 만드는 방법을 개선 해 보자.

``` javascript
/**
 * Utils 
 */ 
const createSphere = (radius, position) => {

    // Three.js mesh
    const mesh = new THREE.Mesh(
        new THREE.SphereGeometry(radius, 20, 20),
        new THREE.MeshStandardMaterial({
            metalness: 0.3,
            roughness: 0.4,
            envMap: environmentMapTexture,
            envMapIntensity: 0.5 
        })
    )
    mesh.castShadow = true 
    mesh.position.copy(position)
    scene.add(mesh)

    // Cannon.js body
    const shape = new CANNON.Sphere(radius) 

    const body = new CANNON.Body({
        mass: 1,
        position: new CANNON.Vec3(0, 3, 0),
        shape: shape, 
        material: defaultMateral 
    })
    body.position.copy(position)
    world.addBody(body) 
}

createSphere(0.5, { x: 0, y : 3, z : 0})
```



## Use an array of objects (갹채 배열 사용) 

이 부분을 처리하는 위해 -> 업데이트해야 하는 모든 개체의 배열을 만듭니다.
Mesh 및 Body 를 해당 배열에 추가 합니다.

``` javascript
const objectsToUpdate = []

const createSphere = (radius, position) => {
    // ...
    // Save in objects to update
    objectsToUpdate.push({
        mesh: mesh,
        body: body
    })
}

objectsToUpdate.push({ mesh, body })

const tick = () => {
    // ...
    world.step(1 / 60, deltaTime, 3)
    for(const object of objectsToUpdate) {
        object.mesh.position.copy(object.body.position)
    }
}
```




## Dat.GUI 에 추가

`createSphere` Dat.GUI 에 버튼을 추가 해 보자.
-> 첫 번쨰 매개변수 gui.add(...) 객체여야 하고, 두 번째 매개변수 속성 이름이어야 합니다.

``` javascript
const gui = new dat.GUI()
const debugObject = {}

debugObject.createSphere = () => {
    createSphere(0.5, { x: (Math.random() - 0.5) * 3, y : 3, z : (Math.random() - 0.5) * 3 })
}
gui.add(debugObject, 'createSphere')
```


