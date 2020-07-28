```
layout : post
title : "AndroidViewModel"
subtitle : "ViewModel에서 Context 사용"
category : development
tags : android
```

# AndroidViewModel - 뷰모델에서 Context 사용하기



ViewModel을 사용할 때, Context가 필요하다면 View에서 매개변수로 주는 방법을 사용하고 있었다. 

그런데, 이 방식은 근본적으로 잘못된 방식이라는 것을 알았다.



AAC - ViewModel을 사용하는 이유 중 하나는 화면 변경 등이 발생하면 액티비티나 프레그먼트가 죽고 다시 살아나기 때문에 데이터를 잃는 현상에서 벗어날 수 있다는 장점.

즉, 라이프사이클과 데이터 유지가 가능하다는 점이 있는데, View에서 ViewModel에 Context를 전달하게 되면 액티비티가 종료되어 소멸될 때, 해당 Context 리소스를 정리할 수 없다고 한다. (GC에서도)



그럼 ViewModel에서 Context를 어떻게 써야되나 찾아봤는데 정보가 많이 없었다.

아니, AndroidViewModel을 써야된다는 정보는 많았지만 View에서 어떻게 해당 객체를 생성해야되는지 찾기가 힘들었다.



결국은 유튜브에서 관련 영상을 보고 알아내긴 했지만.



## View에서 AndroidViewModel 객체 생성하기

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



끝.