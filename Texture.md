# Textures 

Textures -> Geometry 에 덮을 이미지 입니다.

Color (or albedo) -> 가장 단순한 Texture, Texture 의 픽셀만 가져와 Geometry 에 적용합니다. 
Alpha -> 흰색이 표시되고, 검은색이 표시되지 않은 이미지. 
Height -> 약간의 릴리프를 만들기 위해 정점을 이동하는 이미지.
Normal -> 정점을 이동하지는 않지만, face이 다른 방향으로 향하고 있다고 생각하도록 빛을 유인합니다.
Ambient occlusion -> 틈새에_ 그림자를 가짜로 만드는 이미지.
Metalness -> 금속성(흰색), 비금속성(검은색) 부분을 지정하는 이미지. 
Roughness -> 거친 부분 (흰색), 부드러운곳(검은색) 지정. 
PBR -> 질감(특히 금속성 및 거칠기) PBR 원칙이라고 부릅니다. 



## 텍스처를 로드하는 방법.

이미지의 URL 가져오기.

텍스처를 로드하려면 이미지 파일의 URL 이 필요합니다.

``` javascript
import imageSource from './image.png'
console.log(imageSource)
```


네이티브 자바스크립트 사용.

-> 기본 Javascript 를 사용하는 경우 먼저 `Image` 인스턴스를 만든 뒤 -> load event 수신 해
src 속성을 변경하여 이미지 로드를 시작해야 합니다.

``` javascript
const image = new Image()
image.onload = () => {
    console.log('image loadede')
}
```

Three.js 에서 Textures 를 만들 떄 -> THREE.Textures 를 사용 합니다.

``` javascript
const image = new Image()
image.addEventListener('load', () => {
    const texture = new THREE.Texture(image)
})
image.src = '/textures/door/color.jpg'

// Texture 를 사용하려면 -> Material 에 map 속성를 선언 해 주면 됩니다. 

const material = new THREE.MeshBasicMaterial({ map: texture })
```



## Texture Loader 사용.

이전까지 new Image() Javascript 내장 함수를 사용 해왔지만, 좀 더 간편한 방법이 있습니다.
-> Three.JS 의 TextureLoader 를 사용하면 훨씬 간편하게 할 수 있습니다.

``` javascript
const textureLoader = new THREE.TextureLoader()

const texture = textureLoader.load(
    '/textures/door/color.jpg',
    () => {
        console.log('loading finished')
    },
    () => {
        console.log('loading progressing')
    },
    () => {
        console.log('loading error')
    }
)
```




## LoadingManager 사용.

이미지가 여러 개 있고, 모든 이미지가 로드 될 떄까지 그런 함수 -> `LoadingManager`
LoadingManager Class 의 인스턴스를 만든 뒤 `TextureLoader` 에 전달 합니다. 

``` javascript
const loadingManager = new THREE.LoadingManager()

loadingManager.onStart = () =>
{
    console.log('loading started')
}
loadingManager.onLoad = () =>
{
    console.log('loading finished')
}
loadingManager.onProgress = () =>
{
    console.log('loading progressing')
}
loadingManager.onError = () =>
{
    console.log('loading error')
}

const textureLoader = new THREE.TextureLoader(loadingManager)

const colorTexture = textureLoader.load('')
const alphaTexture = textureLoader.load('')
const heightTexture = textureLoader.load('')
const normalTexture = textureLoader.load('')

const material = new THREE.MeshBasicMaterial({ map: colorTexture })
```


## Minification filter 

Minification filter 텍스처의 pixel 이, render pixel 보다 작을 떄 발생합니다.
-> 즉 텍스처가 표면에 비해 너무 커서 덮습니다.

6 가지 가능한 값이 있습니다.

- THREE.NearestFilter
- THREE.LinearFilter
- THREE.NearestMipmapNearestFilter
- THREE.NearestMpampLinearFilter
- THREE.LinearMipmapNearestFilter
- THREE.LinearMipmapLinearFilter


기본값은 `THREE.LinearMipmapLinearFilter` 입니다.

``` javascript
colorTexture.minFilter = THREE.NearestFilter 
const colorTexture = textureLoader.load('/textures/checkerboard-1024x1024.png')
```



## Magnification filter 

Texture 의 픽셀이 렌더의 픽셀보다 클 떄 작동합니다.
-> 즉, 텍스처가 덮는 표면에 비해 너무 작습니다.

``` javascript
const colorTexture = textureLoader.load('/textures/checkerboard-8x8.png')
```

- THREE.NearestFilter
- THREE.LinearFilter

``` javascript
const colorTexture = textureLoader.load('/textures.minecraft.png')
colorTexture.generateMimaps = false
colorTexture.minFilter = THREE.NearestFilter 
```

