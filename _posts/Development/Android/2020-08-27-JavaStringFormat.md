---
layout : post
title : "Java String format"
subtitle : "자바 스트링 포맷"
category : development
tags : android
---

# Java String format (for android)

현재 Java로 안드로이드 개발을 하고 있는데, `DataBidning` 을 사용하다가 보니 string 값에 %s, %d 와 같은 방식을 사용하여 실제 데이터를 집어 넣고는 했다.

그런데, 각기 다른 언어 타입의 경우 데이터를 넣어야 하는 순서가 달라져버려서 검색 끝에 찾아내어 이 문서를 작성하게 되었다.





### 1. 문자열 format

먼저 Java에서는 기본적으로 아래와 같이 쓸 수 있다.

```java
String.format("%s, %s", "DATA1", "DATA2");
```

위의 결과값은 `DATA1, DATA2` 가 나올 것이다.



참고로 %s는 문자열, %d는 정수, %x는 16진수, %o는 8진수, %f는 실수이다.





### 2. 문자열 width

문자열의 길이를 미리 정할 수 있다.

```java
String.format("%10s %10s", "DATA1", "DATA2");
```

위와 같이 사용하게 되면 길이를 10으로 정하게 된 것이고 데이터가 우측부터 채워진 뒤 남는 영역은 공백처리가 된다.

`%`를 공백이라고 한다면 `%%%%%DATA1 %%%%%DATA2` 와 같은 결괏값이 나오게 될 것이다.



참고로 우측부터가 아니라 좌측부터 채우고 싶을 경우에는 아래와 같이 `-` 기호를 이용하면 된다.

```java
String.format("%-10s %-10s", "DATA1", "DATA2");
```



추가로 정수형이라면 아래와 같이 공백이 아닌 0으로 채울 수 있다.

```java
String.format("%010s %010s", 19000, 2333333);
```

결과는 `0000019000 0002333333` 요런식이 될 것이다.

그리고 정수, 실수 등 숫자 타입은 `-` 기호를 사용할 수는 없음



위와 같은 기호들을 `flag`라고 하는데 다양한 기능을 가진 flag들이 많으니 추가로 찾아보면 좋을 것 같다.



### 3. 문자열 argument_index

내가 찾고 싶었던 기능인 index를 정해주는 기능이다.

```java
String.format("%2$s %1$s", "Lion", "Tiger");
```

위와 같이 데이터를 집어 넣게 되면 %와 $ 사이의 숫자의 순서대로 들어가게 된다.

결과는 `"Tiger Lion"` 이 될 것이다.







---

### 마침

사실 가장 찾고 싶었던 Index 기능을 찾아서 다른건 필요 없는 지식이긴 했지만

알아둬서 나쁠 것은 없지



참고 사이트 : http://blog.devez.net/100

