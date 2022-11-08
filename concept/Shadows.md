# Shadows 

Mesh 뒷면에 그림자를 만들어 준다고 생각하면 됩니다.
그림자 맵을 활성화 하기 위해서는 -> renderer 를 활성화 해야 합니다.

``` javascript
renderer.shadowMap.enabled = true 
```

개체가 속성으로 그림자를 투사 할 수 있는 지 -> `castShadow`
개체가 속성으로 그림자를 받을 수 있는 지 -> `receiveShadow` 
-> 활성화 시켜야 합니다.

``` javascript
sphere.castShadow = true
plane.receiveShadow = true 
```



## 그림자 맵 최적화

렌더링의 경우 크기를 지정해야 합니다.
-> 기본적으로 shadow map size 는 512x512 성능 상의 이유로만 사용 됩니다.
    -> 개선할 수 있지만, map mapping 2의 거듭제곱 값이 필요합니다.

``` javascript
directionalLight.shadow.mapSize.width = 1024
directionalLight.shadow.mapSize.height = 1024
```



## Near and far 

Three.js 는 Camera 를 사용하여 shadow map 렌더링을 수행합니다.
-> 이러한 카메라는 우리가 이미 사용한 카메라와 동일 속성을 가지고 있습니다.
    -> `그림자 품질이 향상되지는 않지만, 그림자가 보이지 않거나 그림자가 갑자기 잘리는 버그를 수정 할 수 있습니다.`

``` javascript
const directionalLightCameraHelper = new 
THREE.CameraHelper(directionalLight.shadow.camera)
scene.add(directionalLightCameraHelper)

directionalLight.shadow.camera.near = 1
directionalLight.shadow.camera.far = 6
```


## Amplitude 진폭

우린 DirectionalLight 를 사용하고 있기 떄문에, Three.js 는 `OrthorgaphicCamera` 를 사용하고 있습니다.
우린 카메라가 각 면에서 볼 수 있는 거리를 제어 top, right, bottom, left 속성을 사용할 수 있습니다.

``` javascript
directionalLight.shadow.camera.top = 2
directionalLight.shadow.camera.right = 2
directionalLight.shadow.camera.bottom = -2
directionalLight.shadow.camera.left = -2
directionalCameraHelper.visible = false 
```


## Blur 

`radius` 속성을 사용하여 그림자 흐름(Blur)을 제어할 수 있습니다.

``` javascript
directionalLight.shadow.radius = 10
```

Shadow map algorithm

THREE.BasicShadowMap : 성능은 높지만, 품질이 좋지 않습니다.
THREE.PCFShadowMap : 성능은 떨어지지만, 가장자리는 부드럽습니다.
THREE.PCFSoftShadowMap : 성능은 떨어지지만, 가장자리는 더 부드럽습니다.
THREE.VSMShadowMap : 성능이 낮고 제약이 많으며, 문제가 발생할 수 있습니다.

Shadow Map 을 변경하려면 -> renderer.shadowMap.type 속성을 업데이트 하자.
더 나은 품질을 위해 `THREE.PCFShadowMap` 을 사용할 수 있습니다.

``` javascript
renderer.shadowMap.type = THREE.PCFSoftShadowMap
```


## SpotLight 

-> SpotLight 에서는 속성 4가지가 필요합니다. (castShadow, true, target, scene)

``` javascript
const spotLight = new THREE.SpotLight(0xffffff, 0.4, 10, Math.PI * 0.3)

spotLight.castShadow = true

spotLight.position.set(0, 2, 2)
scene.add(spotLight)
scene.add(spotLight.target)

const spotLightCameraHelper = new 
THREE.CameraHelper(spotLight.shadow.camera)
scene.add(spotLightCameraHelper)
```

장면이 너무 밝으면 다른 조명 강도를 줄일 수 있습니다.

``` javascript
const ambientLight = new THREE.AmbientLight(0xffffff, 0.4)
const directionalLight = new THREE.DirectionalLight(0xffffff, 0.4)
```




## PointLight 

그림자를 지원하는 마지막 조명 PointLight 를 사용 해 보자.

``` javascript
const pointLight = new THREE.PointLight(0xffffff, 0.3)

pointLight.castShadow = true 

pointLight.position.set(-1, 1, 0)
scene.add(pointLight) 

const pointLightCameraHelper = new 
THREE.CameraHelper(pointLight.shadow.camera)
scene.add(pointLightCamerasHelper)
```

