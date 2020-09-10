---
layout : post
title : "Strategy Pattern"
subtitle : "전략 패턴"
category : development
tags : android
---

# Strategy Pattern(전략 패턴)

Strategy Pattern이란 내가 이해한 바에 의하면

"다양한 Strategy를 미리 생성해 놓고, 필요할 때마다 골라서 사용할 수 있게 해주는 패턴"이라고 할 수 있을 것 같다.

<br>

사실 Strategy Pattern은 디자인 패턴의 기본중의 기본이라는 말도 있었고

함수형 프로그래밍(JAVA8)을 사용하지 못할 경우에 대체적으로 사용할 수 있는 패턴을 찾다보니까 알게 되었다.

java8의 functional Interface 기능 중 하나인 `Consumer` 를 사용하기 위해서는 디바이스가 SDK 버전이 24이상 (N 이상) 이 되어야 하기 때문에 그 아래 버전을 호환하기 위해서는 대체제가 반드시 필요했다..

(현재 개발하고 있는 Kiosk도 안타깝게 24미만의 디바이스도 지원해야했었고..)

<br>

결국 생각해보면 Android 환경에서는 모든 버전을 호환해야하는 일이 많기 때문에 Java8을 사용하는 건 어렵다..

쓸 수 있는 경우라고 한다면 지원하는 디바이스를 N버전 이상으로 올려야만 가능하다는 점.

<br>

<br>

각설하고, 그래서 Strategy Pattern은 어떻게 사용하는가?



## 1. Strategy 생성

```java
public interface Strategy{
    void showToast();
}
```

일단, Interface를 생성하고 사용함 Method명을 정의한다.

여기서는 일단 Toast Message를 보여주는 기능을 할 `showToast()` Method를 정의해놨다.

<br>

## 2. Strategy Override

미리 생성해둔 Strategy Interface를 implements 한 class를 생성하거나 특정 class에서 interface 객체를 생성하여 Override 해줌

<br>

```java
public class StrategyClass implements Strategy{
    
    @Override
    public void showToast(){
        Toast.maketext(this, "TEXT", Toast.LONG).show();
    }
}
```

위는 인터페이스를 implements 하여 override 한 것이다. (위는 Context가 없어서 Toast에서 Error가 나겠지만 중요한건 아니므로 Pass.)

<br>

```java
Strategy strategy = new Strategy(){
    
    @Override
    public void showToast(){
        Toast.maketext(this, "TEXT", Toast.LONG).show();
    }
}
```

위는 특정 Class에서 Strategy 인터페이스 객체를 생성해 놓은 것이다.

해당 Class에서만 쓰는 Method 등을 이용하고 싶을 때는 이게 간편하다.

<br>

<br>

## 3. Strategy 전달(주입)

Strategy를 만들었으니, 실제로 해당 Method를 사용할 Class로 전달을 해줘야한다.

```java
public class User{
    Strategy strategy;
    public void setStrategy(Strategy strategy){
        this.strategy = strategy;
    }
    
    public void useStrategy(){
        strategy.showToast();
    }
}
```

실제 Strategy를 적용하는 Class는 대략적으로 위 처럼 사용하게 된다.

여기서는 Strategy 객체를 생성하고 해당 객체를 받아올 set Method를 정의해두었다.

이후에 기존 클래스에서 setStrategy Method를 통해 사용해야할 Strategy를 전달하면 된다.

<br>

혹은, 아래처럼 사용할 수 있다.

```java
public class User{
   
    public void useStrategy(Strategy strategy){
        strategy.showToast();
    }
}
```

useStrategy의 파라메터로 받아와서 쓰면 굳이 User Class 안에 Strategy 객체를 만들 필요가 없다.

굳이 저장해서 쓸 필요가 없는 경우에는 이렇게 간단하게 쓰는게 좋을 것 같다.

<br>

<br>

Client(전략을 생성하고 주입시키는 Class)에서는 생성한 Strategy를 User Class의 Method 형식에 맞게 호출하여 전달해주면 된다.

```java
public class Client{
    
    //미리 StrategyClass를 정의한 경우
    public void sendStrategy(){
        Strategy strategy = new StrategyClass();
        User user = new User();

        user.useStrategy(strategy);
	}
    
    //Strategy 객체를 생성한 뒤 전달하는 경우
    public void sendStrategy(){
        Strategy strategy = new Strategy(){

            @Override
            public void showToast(){
                Toast.maketext(this, "TEXT", Toast.LONG).show();
            }
        }
        
        User user = new User();
        user.useStrategy(strategy);
    }
}
```





## 마침

사실 Strategy Pattern은 알게 모르게 나도 유용하게 사용하고 있던 Pattern 이었다.

그럼에도 불구하고 이렇게 정리하는 작업을 하고 나니 조금 더 해당 Pattern에 이해도가 높아진 느낌이다.

<br>

전략 패턴은 앞으로도 Android를 개발하면서 유용하게 사용할 것 같다.

만약 대부분의 사람들이 SDK 버전이 24이상이 되어 Java8을 자유롭게 사용할 수 있는 때가 온다면 어떻게 될진 모르겠지만..

함수형 프로그래밍에 대해서 겉만 핥고 있으니 확실하게는 모르겠다.



끝.