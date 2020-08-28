---
layout : post
title : "Wifi debug"
subtitle : "wifi를 통해 디버그하기"
category : development
tags : android
---

# Wifi Debug



안드로이드 스튜디오에서 wifi를 통해 디바이스를 debug 하는 방법에 대해 설명함

(참고 : https://thomass.tistory.com/11 )



<br>

---

### 1. Plugin

File - Settings - Plugins 경로에서 <b>WIFI ADB ULTIMATE </b> 를 Install 한 뒤, IDE를 재시작한다.



---

### 2. Device usb connect

안드로이드 디바이스의 개발자모드를 활성화한 뒤, cmd 창에서 `adb tcpip 5555` 를 입력한다.

단, 사전에 adb 환경변수 설정이 되어 있어야 편함



참고로, 5555는 임의의 포트이며 원하는 포트 번호로 설정해도 된다.



---

### 3. Device WIFI connect

플러그인 설치로 인하여 생성된 Android Studio 우측의 `WIFI ADB ULTIMATE` 탭을 켜면 IP와 포트를 입력할 수 있다.

해당 IP는 안드로이드 디바이스와 연결된 WIFI의 것을 입력한다. (PORT는 기존에 설정한 포트인 5555)

<br>

PC와 디바이스가 같은 와이파이 망을 사용하고 있다면 정상적으로 연결되고

`Manage devices`에 표시된다.