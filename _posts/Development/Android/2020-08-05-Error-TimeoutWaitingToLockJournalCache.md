---
layout : post
title : "Timeout waiting to lock journal cache error"
subtitle : "android studio error"
category : development
tags : android
---

# Android Studio - "Timeout waiting to lock journal cache" Error



![error01](..\..\..\assets\img\2020-08-05\error01.png)



람다함수를 쓰기 위해 Java 버전을 7에서 8로 업그레이드 한 뒤에 빌드 시 위와 같은 Error가 발생했다. 



구글링 결과 

안드로이드 스튜디오의 File 메뉴에서 



![error01](../../../assets\img\2020-08-05\error02.png)

`Invalidate Caches / Restart...` 를 선택하면 아래와 같은 창이 나오는데,



![error01](../../../assets\img\2020-08-05\error03.png)

여기서 가장 좌측에 있는 `Invaildate and Restart` 버튼을 눌러주면 해결된다.



여담으로 저걸 누르면 스튜디오가 종료됐다가 다시 실행되는데, 여기서 엄청난 렉(?)이 발생해서 화면이 멈춰버렸다. 진행되고 있는지 조차 알 수 없고 아무런 작동이 되지 않아 컴퓨터 강제 재부팅을 실시..

혹시 모르니까 중요한 파일을 저장해두고 수행하길 바람...!



재부팅 후에는 정상적으로 잘 되더라...