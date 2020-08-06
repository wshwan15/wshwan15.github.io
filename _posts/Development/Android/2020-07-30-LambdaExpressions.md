---
layout : post
title : "Lambda"
subtitle : "람다식 사용하기"
category : development
tags : android
---

# Android 람다식(Lambda Expressions) 

람다식은 기존 함수의 사용법을 좀 더 간결하게 바꿔주는 방식이다.

다만 무명함수가 되기 때문에 자주 재사용해야하는 함수는 쓰지 않는 것이 좋음.



"식별자 없이 실행이 가능한 함수" 라고 보통 정의를 하는 것 같다.

Java 8 부터 지원하게 되었다.



---

#### 람다식 사용법

```md
(매개변수) -> {실행문}
```

람다식은 위와 같은 형식으로 구성되어 있다.



```java
public static void main(String[] args){
    exec((i,j)->{
        return i+j;
    });
}
```

위와 같은 방식으로 사용하면 된다.

인자 i와 j 값을 받아서

더해주는 값을 return 하게 되는 람다 함수.





### 끝마침

`DataBinding` 사용하기 위해 XML에서 이벤트 리스너를 등록할 때 람다식을 이용해서 사용을 많이 하기 때문에

한번 알아보았다.



람다식을 쓰는 것이 코드 효율성 증진에 영향을 크게 미치지는 않는다고 생각하기 때문에

DataBinding 을 위한 XML 함수 등록 이외에 쓰기 딱 맞는 상황이 나오지 않고서야 사용할 것 같진 않다.