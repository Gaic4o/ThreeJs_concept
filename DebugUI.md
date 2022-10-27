# Debug UI 

HTML / CSS / JS 를 사용하여 자신만의 debug UI 를 만들 수 있지만, 여러 라이브러리가 만들어져 있습니다.

- dat.GUI 
- control-panel
- ControlKit 
- Uil
- Tweakpane 
- Guify
- Oui 

dat.GUI 는 -> 오랫동안 업데이트 되지 않았구, 일부 취약성 경고가 있습니다.
dat.gui 대체 할 수 있는 라이브러리 lil-gui 가 만들어 졌습니다.


## Dat.GUI 

``` javascript
npm install --save lil-gui 

import * as THREE from 'three';
import * as dat from 'lil-gui';

/**
 * Debug 
 */
const gui = new dat.GUI()
```


- Range (최소값과 최대값 숫자 경우)
- Color (다양한 색상)
- Text (간단 텍스트) 
- Checkbox (true, false)
- Select (목록 선택)
- Button (기능 trigger)
- Folder (요소가 너무 많은 경우 Folder 를 만들어서 사용합니다.)

간단 사용 예

``` javascript
gui
    .add(mesh.position, 'y')
    .min(-3)
    .max(3)
    .step(0.01)
    .name('elevation')
```