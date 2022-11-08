# Geometries 

Geometry? -> veretx shader 를 사용하여 Geometry(점선으로 -> 삼각형, 사각형 등) 3d 만들 수 있는데.
하지만 vertex shader 로 만들면 코드가 되게 늘어남 (몇 천 줄.. ) -> 이것을 Three.JS 라이브러리로.
Geometry 라는 것을 사용하여 -> 쉽게 3d 삼각형, 사각형, 원뿔 등을 만들 수 있습니다.

다양한 Geometry 가 있습니다.
BoxGometry, PlaneGeometry, CircleGeometry, ConeGeometry, RingGeometry ...


## Buffer Geometry 생성.

나만의 Buffer Geometry 를 생성 할 수 있습니다.
-> Geometry 를 직접 만들 수 있습니다.

``` javascript
// Create an empty BufferGeometry
const geometry = new THREE.BufferGeometry()
```