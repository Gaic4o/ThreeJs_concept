# Lights
 

## AmbientLight 

AmbientLight 는 -> 전방향 조명을 적용합니다.

첫 번쨰 매개변수는 `color`
두 번째 매개변수는 `intensity`

``` javascript
const ambientLight = new THREE.AmbientLight(0xffffff, 0.5)
scene.add(ambientLight)

// Equals 
const ambientLight = new THREE.AmbientLight()
ambientLight.color = new THREE.Color(0xffffff)
ambientLight.intensity = 0.5
scene.add(ambientLight)
```



## DirectionalLight


태양 빛을 맞는 다고 생각하기. 

``` javascript
const directionalLight = new THREE.DirectionalLight(0x00fffc, 0.3)
scene.add(directionalLight)
```


## HemisphereLight
 
-> 하늘 색과, 땅 색을 따로 지정 해 줄 수 있습니다.

``` javascript
const hemipshereLight = new THREE.HemisphereLight(0xff0000, 0x000fff, 0.3); 
scene.add(hemipshereLight)
```

## PointLight

-> 라이터와 같습니다. 무한히 작고, 빛은 모든 방향으로 균일하게 퍼집니다.

``` javascript
const pointLight = new THREE.PointLight(0xff9000, 0.5)
scene.add(pointLight)
```


## RectAreaLight 

첫 번째 매개변수 color, 두 번쨰 매개변수 intensity, 3 번쨰 매개변수 width, 4 번쨰 매개변수 height. 

``` javascript
const rectAreaLight = new THREE.RectAreaLight(0x4e00ff, 2, 1, 1)
scene.add(rectAreaLight)
```

## SpotLight 

- color 
- intensity
- distance
- angle
- penumbra
- decay

``` javascript
const spotLight = new THREE.SpotLight(0x78ff00, 0.5, 10, Math.PI * 0.1, 0.25, 1)
spotLight.position.set(0, 2, 3)
scene.add(spotLight)
```


## Performance
 
Minmal cost 

- Ambient Light
- Hemisphere Light 

Moderate cost 

- Directional Light
- Point Light 

Hight cost 

- SpotLight
- RectAreaLight 