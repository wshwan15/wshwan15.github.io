---
layout : post
title : "Serializable"
subtitle : "Android Serializable 사용하기"
category : development
tags : android
---

# Android Serializable

다른 Activity로 Intent할 때 객체를 넘겨주기 위해서 Serializable을 사용하였다.

이를 통해 Model 객체를 쉽게 전달할 수 있어서 간편하다.



<br>

---

### 사용법

사용하는 방법은 엄청나게 간단하다.

<br>

1) Model Class에 `Serializable` 인터페이스를 `implements` 해준다.

```java
public class ModelClass implements Serializable {
    .....
}
```





2) `putExtra` 메서드를 통해 데이터를 전달할 때, Value 값에 객체를 넣고 Serializable 타입으로 Casting 해주면 끝!

```java
intent.putExtra("object", (Serializable) modelClass);
```







---

### 마침



데이터를 이용할 때 주로 Model 객체를 사용하는 편인데, 위 기능을 통해서 보다 편리하게 데이터의 운용이 가능해졌다.