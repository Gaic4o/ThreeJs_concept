# Materials 

Materials 는 각 픽셀에 색상을 지정하는 데 사용합니다.
기존에는 픽셀에 색상을 지정하려면 fragment shader 에서 지정해야 하는데,
Three.JS 에서는 간편하게 내장 함수를 사용하여 쉽게 만들 수 있습니다.

3개의 Object 를 만들어서 테스트 해 보자.

``` javascript
const material = new THREE.MeshBasicMaterial()

const sphere = new THREE.Mesh(
    new THREE.SphereGeometry(0.5, 16, 16),
    material
)
sphere.position.x = -1.5

const plane = new THREE.Mesh(
    new THREE.PlaneGeometry(1, 1),
    material
)

const torus = new THREE.Mesh(
    new THREE.TorusGeometry(0.3, 0.2, 16, 32),
    material
)
torus.position.x = 1.5

scene.add(sphere, plane, torus)
```

여러 textures 를 텍스처를 테스트 해 볼 것이므로, Texture를 Loader 해 옵시다.

``` javascript
/**
 * Textures
 */ 
const textureLoader = new THREE.TextureLoader()

const doorColorTexture = textureLoader.load('/textures/door/color.jpg')
const doorAlphaTexture = textureLoader.load('/textures/door/alpha.jpg')
const doorAmbientOcclusionTexture = textureLoader.load('/textures/door/ambientOcclusion.jpg')
const doorHeightTexture = textureLoader.load('/textures/door/height.jpg')
const doorNormalTexture = textureLoader.load('/textures/door/normal.jpg')
const doorMetalnessTexture = textureLoader.load('/textures/door/metalness.jpg')
const doorRoughnessTexture = textureLoader.load('/textures/door/roughness.jpg')
const matcapTexture = textureLoader.load('/textures/matcaps/1.png')
const gradientTexture = textureLoader.load('/textures/gradients/3.jpg')
```


## MeshBasicMaterial 

-> 가장 기본적인 Material. 

opactiy 를 이용하여 투명도를 제어할 수 있느지만, THREE.js 에 opacity 를 사용한다고 알려야 하므로 -> transparent 를 true로 설정 해 주어야 합니다.

``` javascript
material.transparent = true 
material.opacity = 0.5
```

투명도를 작동하므로, `alphaMap` 속성을 사용하여, Texture로 투명도를 제어할 수 있습니다.

``` javascript
material.transparent = true
material.alphaMap = doorAlphaTexture 
```


Side 

THREE.FrontSide, THREE.BackSide 뿐만 아니라 -> side 면도 설정 할 수 있습니다.
THREE.DoubleSide 표시할 수 있습니다.

``` javascript
material.side = THREE.DoubleSide
```



## MeshNormalMaterial 

Texture 에 한 가지 색상만이 아니라 -> 보라색, 파란색, 녹색 등 여러 색을 칠할 수 있습니다.

``` javascript
const material = new THREE.MeshNormalMaterial()
```


## MeshMatcapMaterial 

-> MeshMatcapMaterial 은 매우 성능이 좋으면서도, 보기에 휼륭하기 떄문에 멋진 머터리얼 입니다.

``` javascript
const material = new THREE.MeshMatcapMaterial()
material.matcap = matcapTexture 
```


## MeshDepthMaterial 

-> 카메라 `near` 값에 가까우면, Geometry 를 흰색으로, 카메라 값 `far` 에 가까우면 검정색으로 Geometry 를 채색 합니다.

``` javascript
const material = new THREE.MeshDepthMaterial()
```

조명 효과를 볼 수 있습니다. -> AmbientLight, PointLight

``` javascript
/**
 * Light
 */ 
const ambientLight = new THREE.AmbientLight(0xffffff, 0.5)
scene.add(ambientLight) 

const pointLight = new THREE.PointLight(0xffffff, 0.5)
pointLight.position.x = 2
pointLight.position.y = 3
pointLight.position.z = 4
scene.add(pointLight)
```



## MeshLambertMaterial 

빛에 반응하는 첫 번쨰 Material 입니다.
-> 조명을 사용하는 가장 성능이 좊은 Material 입니다. 

``` javascript
const material = new THREE.MeshLambertMaterial() 
```


## MeshPhongMaterial 

MeshLambertMaterial 과 매우 유사하지만 -> Geometry 에 표면에서 빛 반사를 볼 수 있습니다.

``` javascript
const material = new THREE.MeshPhongMaterial()
```

`shininess` 로 빛의 반사를 제어 가능 합니다. -> 값이 높을 수록 빛이 더 반짝거립니다.
`specular` 로 사용하여 반사 색상을 변경할 수 있습니다.

``` javascript
material.shininess = 100
material.specular = new THREE.Color(0x1188ff)
```



## MeshToonMaterial 

MeshLambertMaterial 과 유사하지만 약간 만화 스타일입니다.

``` javascript
const material = new THREE.MeshToonMaterial()
```

채색에 더 많은 단계를 추가하려면 ? -> `gradientMap` 속성 사용하거나, `gradientTexture` 하고 로드한 것을 사용할 수 있습니다.

``` javascript
material.gradientMap = gradientTexture
```


## MeshStandardMaterial 

물리적 기반 렌더링 원칙을 사용합니다.
-> MeshLambertMaterial, MeshPhongMaterial 과 마찬가지 조명을 지원하지만, 더 현실적 알고리즘과 거칠기 및 금속성과 같은 더 나은 매개변수를 사용합니다.

``` javascript
const material = new THREE.MeshStandardMaterial()

material.metalness = 0.45
material.roughness = 0.65
```

## ShaderMaterial 및 RawShaderMaterial 

ShaderMaterial 과 RawShaderMaterial 은 -> 둘 다 자신만의 머터리얼을 만들 수 있습니다. 


## Environment map 

Material 에서도 Environment map  을 생성할 수 있습니다.
`TextureLoader` 대신 `CubeTextureLoader` 를 사용해야 합니다 -> (Environment map 을 사용할 경우)


``` javascript
const material = new THREE.MeshStandardMaterial()
material.metalness = 0.7
material.roughness = 0.2
gui.add(material, 'metalness').min(0).max(1).step(0.001)
gui.add(material, 'roughness').min(0).max(1).step(0.001)

const cubeTextureLoader = new THREE.CubeTextureLoader() 

const environmentMapTexture = cubeTextureLoader.load([
    '/textures/environmentMaps/0/px.jpg',
    '/textures/environmentMaps/0/nx.jpg',
    '/textures/environmentMaps/0/py.jpg',
    '/textures/environmentMaps/0/ny.jpg',
    '/textures/environmentMaps/0/pz.jpg',
    '/textures/environmentMaps/0/nz.jpg'
])
material.envMap = enviromentMapTexture 
```

