---
layout : post
title : "Consumer"
subtitle : "Android Consumer"
category : development
tags : android
---

# Android 함수형 인터페이스(For Lambda)

대표적인 인터페이스 5개 (Consumer, Supplier, Function, Operator, Predicate) 소개.



\1. Consumer : 매개변수O, 반환값X -> 추상메소드 제공.

\2. Supplier : 매개변수X, 반환값O

\3. Function : 매개변수O, 반환값O, 주로 매개변수를 반환값 타입으로 변환 후 반환

\4. Operator : 매개변수O, 반환값O, 주로 매개변수를 연산 후 결과값 반환 역할

\5. Predicate : 매개변수O, 반환값O, 주로 매개변수를 조사한 후 논리값 반환 역할



<br>

---

### 1. Consumer

Consumer는 람다식을 위한 함수형 인터페이스의 개념 중에 하나로 속하는 기법이다.

매개변수를 받아서 소비하는 일을 구현하는데, 나는 주로 매개변수로 Method를 전송할 필요가 있을 때 사용한다.





예를 들어 아래와 같은 Method가 있다고 한다면

```java
public void showToast(){
    Toast.makeText(this, "Hello~", Toast.LENGTH_SHORT).show();
}

public void showToast(String msg){
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}
```



이를 특정 Class 객체의 매개변수 (예를 들어 아래)로 전달하려면 어떻게 해야할까?

```java
    public void setAlertDialog(String alertmsg, Consumer consumer){
        builder = new AlertDialog.Builder(activity);
        builder.setMessage(alertmsg);
        builder.setPositiveButton("OK", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
                    consumer.accept("");
                }
            }
        });
        alertbox = builder.show();
    }
```



이미 코드에도 있지만 매개변수 타입을 `Consumer`로 하면 된다.



람다식을 사용해 아래와 같이 인자를 구성하면 됨

```java
AlertDialogUtil alertDialogUtil = AlertDialogUtil.getInstance();
alertDialogUtil.setAlertDialog("사용할 카드를 스마트폰 뒷면에 태깅해주세요.", "카드 읽기", "취소", (n)-> showToast());
```

`(n) -> showToast()` 혹은 `n -> showToast(n)` 과 같이 람다식을 사용하면 된다.



해당 인자를 받는 쪽에서

`consumer.accept("");` 혹은 `consumer.accept("message");` 와 같이 `accept` 메서드를 사용하면

`showToast()` 혹은 `showToast("message")` 를 호출한 것과 같아진다.



다른 Class Method 안에서 현재 Class Method를 사용하고 싶을 때

굳이 인터페이스를 따로 정의하거나 할 필요 없이 간단하게 사용할 수 있다는 점이 큰 강점이다.



---

### 2. Supplier 

---

### 3. Function 

---

### 4. Operator 

---

### 5. Predicate 







---

### 끝마침



참고 문서 : https://sas-study.tistory.com/106