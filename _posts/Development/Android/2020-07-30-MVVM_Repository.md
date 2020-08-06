---
layout : post
title : "MVVM에서의 Repository 역할"
subtitle : "Repositroy 정리하기"
category : development
tags : android
---

# Android Repository Pattern with RxJava

Repository 패턴은 데이터(Model)와 비즈니스 로직(ViewModel)을 분리시키기 위한 패턴이다.

도메인(ViewModel)이 필요한 데이터를 Repository에 요청하면 Repository는 도메인이 요청한 데이터를 가공해서 전달해주는 역할이다.



즉, 만약 Model(DataSource)의 변경이 발생하더라도 Repository의 수정만 하면 되고 실제 ViewModel의 로직은 변경할 필요가 없게 되는 것.



때문에 Client는 Repository 인터페이스를 의존하기 때문에 테스트에도 용이하다.



결론은, Repository 패턴은 Presentation Layer(ViewModel)과 Data Layer(DataSource, Model)의 Coupling(결합도)를 낮춰주는 효과를 가질 수 있는 것이고 추가로 언제나 일관된 인터페이스를 갖고 있기에 변경에 용의하다는 것이다.



이어지는 얘기로 당연하게도 같은 형식의 데이터를 가져오려고 할때, 코드의 중복도 없앨 수 있다. (여러 ViewModel이 같은 Repository를 바라볼 수 있으므로.)



---

### How To Use

단순하게 생각하면 ViewModel과 데이터를 가져오는 작업(Room/Retrofit 등) 사이에 중재를 할 수 있는 클래스를 하나 두면 된다고 생각한다.



ViewModel 에서는 필요한 Repository 객체를 생성해서 가공된 데이터를 가져오면 되는 것이고

Repository는 ViewModel에서 필요한 양식의 데이터를 가져와서 가공하면 된다.



그 중에서 내가 앞으로 사용할 코드는 약간의 `RxJava`를 이용하여 구현한다.



###### ViewModel

```java
    UserRepository mUserRepository = new UserRepository();
```

```java
        mUserRepository.getVersion()
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(
                item -> {
                    ///
                    Observable.just("Succeed."+item.webnautes.get(0).check).subscribe(s->navigator.showToast(s));
                },
                error -> {
                    Observable.just("Failed").subscribe(s->navigator.showToast(s));
                }
        );
```



먼저 ViewModel은 위와 같은 느낌이 된다.

`mUserRepository`라는 Repository 객체를 하나 초기화하고, 해당 Repository의 getVersion() 메소드를 호출한다.

`subscribeOn`과 `observeOn` 은 io와 스레드를 선택하는 메소드이고 .subscribe 메서드에서 가져온 데이터를 활용해 비즈니스 로직을 구성할 수 있다.

`item`이 return 받은 Single 클래스의 객체(후에 설명)이고, 뒤에 바로 `error`가 발생했을 때의 코드도 구현할 수 있다.



###### Repository

```java
    RetrofitCall RC = new RetrofitCall();

    public Single<ParentVersionModel> getVersion() {

        return RC.call_single;
    }
```



Repository는 매우 심플하다고 생각되는게, 그저 Data를 받아오는 작업을 분리해놓은 것이다.

retrofit2 를 이용한 RetrofitCall 객체를 생성하고 `Single<ParentVersionModel>` 이라는 리턴 값을 가진 `getVersion()` 메서드를 생성하였다.

(자세한 것은 Retrofit 에서 설명되어짐)

그리고 이전의 ViewModel 코드에서 `getVersion()` 메서드를 호출하여 데이터가 담긴 객체를 받고 처리하게 된다.



###### Model

```java
public class VersionModel {

    @SerializedName("check")
    public String check;

    public String checked;
}
```

```java
public class ParentVersionModel {

    public List<VersionModel> webnautes;

    public ParentVersionModel(List<VersionModel> webnautes) {
        this.webnautes = webnautes;
    }
}
```

모델은 2가지로 나뉘어 있다. 받아오는 데이터의 형식에 따라 정의되어 진다.

예시 코드는 String type의 check와 checked 라는 필드를 가진 `VersionModel`, 그리고 해당 클래스를 List로 가지는 `ParentVersionModel`



###### DataSource

```java
    RetrofitService networkService = RetrofitHelper.getRetrofit().create(RetrofitService.class);
    public Single<ParentVersionModel> call_single = networkService.getVersions();
```



위 코드로 이루어진 RetrofitCall Class인데, 사실 이를 Repository에 바로 올릴지 말지가 고민이었다.

그러나 만약 Single 객체가 늘어나게 될 경우 어떻게 될 것인가를 생각하고 나서 그냥 나누기로 결정하였다.

나머지는 Retrofit과 관련된 내용이므로 자세한 내용은 Retrifit 문서 참고



---

### 끝마침

Repository 패턴의 개념 자체는 그렇게 어렵지 않았다. 하지만 RxJava가 섞이니까 좀 많이 헤메인 것 같다.

어쩌면 내가 잘못 이해하고 있는 부분이 있을 수 있다. 그런 부분이 있다면 차후 수정해나가야겠지