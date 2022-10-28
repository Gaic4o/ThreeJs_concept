# Camera 

Camera 


ArrayCamera 

http://www.gisdeveloper.co.kr/?p=11365
-> 여러 카메라를 사용해서 동시에 여러 장면을 렌더링 하는 데 사용합니다.

StereoCamera 

https://stackoverflow.com/questions/61166661/three-js-how-to-create-mirror-image-for-the-entire-scene
-> 시차 효과를 사용하기 위해 똑같은 방향의 두 대의 카메라를 동시에 렌더링하는 데 사용 됩니다. (VR) 

CubeCamera 

https://discourse.threejs.org/t/cubetexture-from-cubecamera-used-as-an-environment-map-on-a-mesh-appears-to-shift-based-on-roughness/27121
-> Enviroment map (앞, 뒤, 왼쪽, 오른쪽, 위, 아래) 향한 렌더를 만든다고 생각하면 됩니다. 


OrthographicCamera 

https://learn.microsoft.com/ko-kr/dotnet/api/system.windows.media.media3d.orthographiccamera?view=windowsdesktop-6.0
-> 요소는 카메라로부터 거리에 관계없이 동일 크기를 가지고 있습니다. 


PerspectiveCamera 
-> 흔히들 사용하고 있는 실제 카메라로 시뮬레이션 한 것.





## Built-in controls 


DeviceOrientationControls 

-> 장치, OS, 브라우저 허용하는 경우 장치 방향을 자동으로 검색하고 카메라를 회전하는 것. 


FlyControls 

https://www.youtube.com/watch?v=rRsKKfXXU5Y

-> 우주선에 있는 것처럼 카메라를 움직일 수 있습니다. (3축 모두 회전 가능, 전진 및 후진 모두 가능) 


FirstPersonControls 

-> FlyControls 와 같음, 위쪽 축만 고정되어 있음. 


PointerLockControls 


OrbitControls 

https://www.youtube.com/watch?v=REqsqClCHuQ&t=412s 

-> 마우스롤 이용하여 쉽게 회전하거나 측면으로 이동하거나 할 수 있습니다.


TrackballControls

-> OrBitControls 와 같지만, 장면이 거꾸로 되어도 카메라로 계속 회전하고 이동할 수 있습니다.


TransformControls


DragControls 

-> 카메라와 아무 관련 x, 카메라 향하는 방향에서 개체를 drag 해서 이동 시킬 수 있습니다.

 