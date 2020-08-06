---
layout : post
title : "TedPermission"
subtitle : "퍼미션"
category : development
tags : android
---

# Android TedPermission

TedPermission 이란, 

Ted Park 이라는 개발자가 만든 라이브러리라고 한다.

출처: https://gun0912.tistory.com/61 [박상권의 삽질블로그]



퍼미션을 관리하는데 유용한 라이브러리로,  사용하기 위해서는 dependencies에

```java
implementation 'gun0912.ted:tedpermission:2.0.0'
```

위 코드 한 줄 추가해주면 된다.





그리고 권한요청할 Activity에서

```java
PermissionListener permissionlistener = new PermissionListener() {
    @Override
    public void onPermissionGranted() {
        Toast.makeText(Activity1.this, "권한 허가", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onPermissionDenied(ArrayList<String> deniedPermissions) {
        Toast.makeText(Activity1.this, "권한 거부\n" + deniedPermissions.toString(), Toast.LENGTH_SHORT).show();
    }


};
```

위와 같은 메서드를 오버라이드한다.



`onPermissionGranted()` 메서드는 권한이 모두 허용되고 나면 실행이 되고,

`onPermissionDenied()` 메서드는 요청했던 권한 중에서 거부당한 목록을 리턴해주는 역할을 한다.





실제로 권한을 요청하는 코드는 다음과 같다.

```java
        TedPermission.with(this)
                .setPermissionListener(permissionListener)
                .setRationaleMessage("권한 요청 메시지")
                .setDeniedMessage("거부했을 때 메시지")
                .setPermissions("android.permission.WRITE_EXTERNAL_STORAGE", "android.permission.READ_EXTERNAL_STORAGE")
                .check();
```



`setRationaleMessage()` 권한 요청 전, 메시지

`setRationaleConfirmText()`  권한 요청 시 다이얼로그의 '확인' 버튼 커스텀

`setDeniedMessage()` 권한 거부 시, 메시지

`setDeniedCloseButtonText()` 거부했을 때 다이얼로그의 '닫기' 버튼 커스텀

`setGotoSettingButton()` 권한 거부 시, 설정 화면으로 갈 수 있는 버튼 visibility

`setGotoSettingButtonText()` 설정화면으로 가는 버튼 커스텀





---

### 끝마침

이 라이브러리를 쓰는 사람이 더 있는지, 유명한지는 잘 모르겠다.

하지만 구글링해서 찾고 정상적으로 동작하는 것을 확인한 뒤에

난 퍼미션 관련해서는 이걸 계속 쓰면 되겠다고 생각을 했다.



물론 변화가 빠른 IT 특성상 나중에 상황이 달라질 수 있지만 그건 그때 가서 생각하면 되니까