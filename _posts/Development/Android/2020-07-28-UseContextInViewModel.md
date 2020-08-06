---
layout : post
title : "AndroidViewModel"
subtitle : "ViewModel에서 Context 사용"
category : development
tags : android
---

# 뷰모델에서 Context 사용하기
ViewModel을 사용할 때, Context가 필요하다면 View에서 매개변수로 주는 방법을 사용하고 있었다. 

그런데, 이 방식은 근본적으로 잘못된 방식이라는 것을 알았다.



AAC - ViewModel을 사용하는 이유 중 하나는 화면 변경 등이 발생하면 액티비티나 프레그먼트가 죽고 다시 살아나기 때문에 데이터를 잃는 현상에서 벗어날 수 있다는 장점.

즉, 라이프사이클과 데이터 유지가 가능하다는 점이 있는데, View에서 ViewModel에 Context를 전달하게 되면 액티비티가 종료되어 소멸될 때, 해당 Context 리소스를 정리할 수 없다고 한다. (GC에서도)



그럼 ViewModel에서 Context를 어떻게 써야되나 찾아봤는데 정보가 많이 없었다.

대부분은 AndroidViewModel을 사용해야된다는 정보였다. (ViewModel 대신 AndroidViewModel을 상속)

그러나 View에서 어떻게 해당 객체를 생성해야되는지 찾기가 힘들었다.



결국은 유튜브에서 관련 영상을 보고 알아낸 결과는 아래와 같다.

---

## 1. View에서 AndroidViewModel 객체 생성하기

```java
viewModel = new ViewModelProvider(this, new ViewModelProvider.AndroidViewModelFactory(getApplication())).get(Class.class);
```

 위와 같은 방식으로 생성하면 된다.



일단 `viewModel` 객체는 AndroidViewModel을 상속한 클래스 객체고, 뒷부분 Class.class에 들어갈 Class도 같은 클래스를 넣으면 된다.



정상적으로 해당 ViewModel에 Context가 들어감



```java
    public Class(@NonNull Application application) {
        super(application);
    }
```

AndroidViewModel을 상속하면 해당 클래스에 위와 같은 생성자를 생성할 수 있고,

getApplication() 으로 Context를 가져올 수 있음



---

## 2. Interface를 사용하여 Context가 필요한 작업은 View에서 처리하기

그러나 위 방식도 똑같이 Memory leak이 발생한다는 말이 있었다. 결국 똑같은 것인가..?

결국은 ViewModel에서 매개변수로 Context를 저장하는 일은 없어야 한다는 것이었다.



그래서 GitHub의 MVVM 예제를 훑어보다가 적당해보이는 것을 발견했다.

바로 Interface를 사용하는 것.



먼저 .Interface를 하나 생성한다.

```java
public interface MainToaster {

    void ShowToast(String msg);
}

```

스트링 매개변수 값을 토스트로 보여주기 위한 ShowToast 함수를 선언해준다.



그리고 View에서 해당 인터페이스를 implements 해주고 오버라이딩한다.

```java
    @Override
    public void showToast(String msg) {
        Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
    }
```

ViewModel 객체에서 추후에 생성할 함수(`setToaster()`)를 통해 연동해준다.

```java
viewModel.setToaster(this);
```

이렇게 View에서의 작업은 끝이 났고,

<br>

<br>

해당 함수를 사용할 ViewModel Class에서 인터페이스 객체를 생성해준다.

```java
private MainToaster toaster;
```

이후, 좀 전에 말했던 해당 객체를 초기화해주는 함수를 작성하고

```java
    void setToaster(MainToaster toaster) {
        this.toaster = toaster;
    }
```



해당 인터페이스의 객체를 이용해서 showToaster 함수를 쓰면 됨

```java
toaster.showToaster("Hello~World");
```

이러면 ViewModel에 Context를 인자로 전송하지 않아도 Context를 이용할 수 있다.







### 결론

ViewModel을 상속하고 해당 클래스에 Context를 보내는 방식이나

AndroidViewModel를 상속하고 Context를 얻는 방식이나

어쨋든 간에 Context를 참조하게 되는 것이니 Memory leak이 발생한다는 말을 들었다.

확실하겠지?



일단 그래서 최대한 Context를 참조하지 않도록 하고 Context가 필요한 작업들은 Interface를 이용해 View에서 하도록 해야될 것 같다.



요 부분은 좀 많은 연습이 필요할 것 같다.



끝.