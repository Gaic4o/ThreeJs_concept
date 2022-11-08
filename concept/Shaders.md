# Shaders 

좌표, mesh, camera, 시야, 색상, 텍스처, 조명, 안개 등 매개변수들을 -> data 를 Shader 로 보냅니다. 
그런 다음 GPU 에서 처리 합니다.


## 정점 셰이더 

vertex Shader 는 Geometry 정점을 배치하는 것 입니다.
-> vertex 위치, mesh 변형 (위치, 회전 및 크기와 같은), 카메라 정보(위치, 회전 및 시야와 같은) 보내는 것.
그런 다음 GPU 는 정점 Shader 모든 정보를 처리 합니다. 

vertex shader 간에 변경되지 않는 이러한, 유형의 데이터를 `uniform` 이라고 합니다.


## 프래그먼트 셰이더 

vertex -> fragment 이동하는 것을 `varying` 이라고 합니다. 
vertex shader 는 픽셀에 정점을 지정합니다.
fragment shader 는 픽셀에 색상을 지정합니다.

각 정점 사이에서 변경되는 데이터를 attribute vertex shader 에서만 사용할 수 있습니다.


## 요약 

vertex shader -> 정점 배치.
fragment shader -> pixel 색상을 지정.

fragment shader 는 vertex shader 에 실행 됩니다.

각 정점 사이에 변경되는 데이터를 `attribute` -> vertex shader 에서만 사용 가능.
정점 사이에 변경되지 않는 데이터를 (예: mesh 위치 또는 색상) 를 `uniform` 이라고 하며, vertex shader 와 fragment shader 모두에서 사용 가능합니다.
vertex -> fragment 로 varying 을 사용하여 데이터를 옮길 수 있습니다.




## RawShaderMaterial 로 첫 번쨰 셰이더 만들기.

ShaderMaterial, RawShaderMaterial -> Shader 셰이더 Material 사용가능.

``` javascript
const material = new THREE.RawShaderMaterial({
    vertexShader: '',
    fragmentShader: ''
}) 
```


## 다른 파일에서 Shader 분리.

vertexShader, fragmentShader -> Material 에 일일히 코드를 작성해 주면 나중에 유지보수 하기 복잡해 지니.
-> Shader file 을 따로 만들어 분리 하자.(호출 하는 형식)

``` javascript
const material = new THREE.RawShaderMaterial({
    vertexShader: textVertexShader,
    fragmentShader: testFragmentShader,
    wireframe: true
})
```



## gl_Position

gl_Position (화면에 vertex 의 위치를 할당 합니다.)

``` javascript
void main() {
    gl_Position = projectionMatrix * viewMatrix * modelMatrix * vec4(position, 1.0);
    gl_Position.x += 0.5;
    gl_Position.y += 0.5;
}
```


## Position attributes 

vertex `position` 을 검색합니다.

``` javascript
attribute vec3 position; 
```

-> 동일 코드가 기하학의 모든 vertex에 적용된다는 것을 기억하자.
vertex 은 정점 사이에서 변경되는 유일 변수 입니다.

동일 vertex shader 가 각 shader 에 적용되고, `position` 속성에는 해당 특정 vertex의 `x`, `y`, `z` 좌표가 포함됩니다.

``` c++
gl_Positon = /* ... */ vec4(position, 1.0);
```

## Matrices uniforms 

우린 3개의 행렬이 있으며 -> `uniform` 을 사용하여 검색합니다.

``` c++
uniform mat4 projectionMatrix;
uniform mat4 viewMatrix;
uniform mat4 modelMatrix; 
```

-> Mesh `modelMatrix` 기준으로 모든 변형을 적용 합니다.
`Mesh` 의 size 조정, 회전 또는 이동 하면 이러한 변환 `(modelMatrix, position)` 이 포함되고 적용됩니다. 

-> `viewMatrix` Camera 를 기준으로 변형을 적용합니다, Camera 를 왼쪽으로 회전하면, vertex 이 오른쪽에 있어야 합니다.
-> 마지막 `projectionMatrix` 으로 size를 최종 클립 공간 좌표로 변환합니다.

``` c++
uniform mat4 projectionMatrix;
uniform mat4 modelViewMatrix;

attribute vec3 position; 

void main() {
    vec4 modelPosition = modelMatrix * vec4(position, 1.0);
    vec4 viewPosition = viewMatrix * modelPosition;
    vec4 projectedPosition = projectionMatrix * viewPosition;
    gl_Position = projectedPosition;
}
```



## Understanding the fragment shader 

fragment Shader 는 -> Geometry 모든 fragment 에 적용됩니다.

`precision mediump float`; 

float 말고도 다른 가능한 값이 있습니다.

`highp`
`mediump`
`lowp`

highp -> 성능이 저하 될 수 있습니다. 
lowp -> 정밀도가 부족 해 버그 발생할 수 있습니다.
mediump -> 정점 shader 의 정밀도를 설정할 수도 있지만 필수는 아닙니다.


gl_FragColor
-> color 에 대한 것 `gl_FragColor` 같지만, `gl_Position` 이미 선언되어 있으며, `main` 함수에서 할당 해야 합니다.
`vec4` -> 빨강, 녹색, 파랑 등 (r, g, b, a) 입니다.

``` c++
gl_FragColor = vec4(0.5, 0.0, 1.0, 1.0);
```

``` c++
const material = new THREE.RawShaderMaterial({
    vertexShader: testVertexShader,
    fragmentShader: testFragmentShader,
    transparent: true
})
```


## Attributes 

-> Attributes 각 정점 사이에서 변경되는 값 입니다.
각 정점의 좌표 `position` 를 포함하는 이름이 지정된 속성이 하나 더 있습니다.

`z` 각 정점에 임의의 값에 추가하고, 이 값에 따라 축 에서 vertex 를 이동합니다.

``` c++
const count = geometry.attributes.position.count
const randoms = new Float32Array(count) 

// 배열 임의 값 채우기.
for (let i = 0; i < count; i++) {
    randoms[i] = Math.random()
}
```

마지막으로 BufferAttribute 에서 해당 배열을 사용 후 -> Geometry 속성에 추가 합니다.

``` c++
geometry.setAttribute('aRandom', new THREE.BufferAttribute(randoms, 1))
```


``` c++
attribute float aRandom;

void main() {
    modelPosition.z += aRandom * 0.1;
}
```



## Varyings 

이제 aRandom 속성으로 조각 조각마다 color 를 지정하려고 합니다.
-> 안타깝게도 fragment shader 에서 속성을 직접 사용할 수 없습니다.

다행이도`vertex shader` 에서 `varyings` 이라는 것으로 `shader` 끼리 데이터를 보내는 방법. 

``` c++
precision mediump float; 

varying float vRandom;

void main () {
    // ..
    vRandom = aRandom;
    gl_FragColor = vec4(0.5, vRandom, 1.0, 1.0);
}
```



## Uniforms 

Shader 에서 매개변수 사용.
-> veretx, fragemnt 모두 에서 uniform 을 사용 가능.

나만의 uniform 을 만들어보기.

``` c++
const material = new THREE.RawShaderMaterial({
    vertexShader: testVertexShader,
    fragmentShader: testFragmentShader,
    uniforms: {
        uFrequency: { value: 10 }
    }
})
```

``` c++
uniform mat4 projectionMatrix;
uniform mat4 viewMatrix;
uniform mat4 modelMatrix;
uniform float uFrequency;

attribute vec3 position;

void main() {
    modelPosition.z += sin(modelPosition.x * uFrequency) * 0.1
}
```


## Textures 

Texture 도 -> uniform 으로 불러올 수 있습니다.

``` c++
const flagTexture = textureLoader.load('/textures/flag-french.jpg')

const material = new THREE.RawShaderMaterial({
    uniforms: {
        uTexture: { value: flagTexture }
    }
})
```

``` c++
attribute vec2 uv;

varying vec2 vUv;

void main() {
    // ...
    vUv = uv;
}
```

이제 `vUv` Shader 에서 다양한 값을 검색 뒤, `uniform` 을 검색 후, `uTexture`, 결국 다음을 사용하여 색상을 얻을 수 있습니다.

``` c++
precision mediump float;

uniform vec3 uColor;
uniform sampler2D uTexture;

varying vec2 vUv;

void main() {
    vec4 textureColor = texture2D(uTexture, vUv);
    gl_FragColor = textureColor;
}
```

## Color variations 





## ShaderMaterial 



