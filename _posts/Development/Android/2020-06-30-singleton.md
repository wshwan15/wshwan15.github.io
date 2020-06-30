---
layout: post
title: "싱글톤(Singleton) 패턴, LazyHolder"
subtitle: "싱글톤 패턴의 Thread safe를 위한 기법"
category: development
tags: android
---

# 싱글톤(Singleton) 패턴과 LazyHolder.

최근에 MVVM 공부하면서 Model 클래스를 싱글톤으로 만들다보니까 알게된 내용을 적어보려고 한다. 사실 기존에도 싱글톤 패턴은 안드로이드 개발할 때 가장 많이 사용하던 디자인 패턴이고 보통 다수의 클래스에서 쓰이는 전역 변수들을 모아놓은 static class 대신에 많이 사용했다. 전역 변수라고 하더라도 종류별로 싱글톤 객체로 만들어 놓는게 좀 더 깔끔하다는 생각이 들기도 했고 퍼포먼스나 OOP 적으로도 낫다는 평이 있어서이다.

그런데 싱글톤을 그냥 쓰면 멀티 스레드 환경에서 싱글톤 객체에 접근시 초기화 관련하여 문제가 발생할 수 있다고 들었다. 'Thread safe'가 안된다고 말하더라. 그래서 해결법을 찾아본 결과 'LazyHolder'라는 이디엄을 사용하기로 했다. LazyHolder는 `synchronized` 필요 없고, JAVA 버전에도 상관없고, 퍼포먼스도 뛰어나다는 평이 있기도하고 구현하기에도 되게 간단하고 깔끔하기 때문에 마음에 들었다.



## LazyHolder

```java
public class Singleton{
    private Singleton() {}
    public static Singleton getInstance(){
        return LazyHolder.INSTANCE;
    }
    
    private static class LazyHolder{
        private static final Singleton INSTANCE = new Singleton();
    }
}
```

코드를 보자면 위와 같이 간단한데 부가설명을 하자면 일단 싱글톤으로 만들 클래스에는 LazyHolder 클래스 변수가 없다. 따라서 해당 클래스를 로딩할 때 LazyHolder 클래스를 초기화하지 않는다. LazyHolder 클래스는 싱글톤 클래스의 getInstance() 메서드가 호출되고 LazyHodler.INSTANCE를 참조하는 순간에 로딩되며 초기화가 진행된다. 

클래스를 로딩하고 초기화하는 시점에서는 Thread-safe를 보장하기 떄문에 멀티스레딩 환경에서의 문제가 확실하게 해결되는 것.



앞으로 Singleton 패턴을 사용할 때에는 이 LazyHolder 이디엄을 적용해야겠다.