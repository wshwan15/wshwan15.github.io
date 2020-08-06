---
layout : post
title : "데이터바인딩"
subtitle : "DataBinding"
category : development
tags : android
---

# Android DataBinding

안드로이드 MVVM 아키텍쳐 패턴을 적용해 코드를 구조화하기 위해 꼭 필요한 데이터바인딩에 대해서 학습이 필요했고, 이를 정리하려고 한다.



## 1. build.gradle 설정

일단 먼저, 모듈 단위의 build.gradle에 데이터바인딩을 이용하겠다는 설정을 추가해야한다.

```java
android{
    dataBinding{
        enabled = true
    }
}
```





## 2. XML 설정

`<layout>` 태그를 이용해 코드를 감싸고

`<data>` 태그 내에 `<variable>` 을 추가해준다.

```xml
<data>
    <variable
          name="name"
          type="com.example.MainActivity"
    />
</data>
```

위와 같은 느낌으로 선언됨.

name은 변수명

type은 Java Class를 넣게 된다.



이렇게 나누는 궁극적인 목적은 XML에서 데이터 처리와 이벤트 처리를 맡아서 Java 파일에는 해당 데이터를 위한 작업 중심의 코드만 작성하기 위함이다.



이후 

```xml
<TextView
          android:text="@{name.nameResult}"/>
          />

<Button
        android:onClick="@{name::onButtonClick}"/>
        />
```

위와 같이 text 값을 넣거나 onClick  함수를 지정할 수도 있다.

함수는 람다식을 사용해서 `"@{()->name.onbuttonClick(name.nameResult)}"` 요런식으로도 사용 가능. 람다식이 더 편리한 것 같음.



values의 strings.xml 값을 사용할 수도 있다.

###### strings.xml

```xml
    <string name="counter">%s개</string>
```

###### layout.xml

```xml
        <TextView
            android:text="@{@string/counter(Integer.toString(VM.counter))}"
            />
```

위와 같은 방식이다.



 %s, %d 와 같은 값을 넣어주면 실제로 사용할 때 인자를 넘겨받아 표시해주게 된다.

printf 처럼 사용하면 된다고 생각하면 됨.





## 3. Java 설정

일단 XML 코드를 `<layout>` 태그로 감싸게 되면 Binding Class가 자동으로 생성된다.

생성되는 Class의 이름은 xml 파일 이름에 따라서 명명되는데, XML 파일 이름이 `activity_main.xml`이라면 Pascal Casing에 의해서 'ActivityMain'이 되고, 뒤에 Binding이 붙어 최종적으로 `ActivityMainBinding` 이라는 Class가 된다.



위에서 생성된 클래스의 객체를 선언하면 해당 객체를 통해 XML에 선언된 뷰 객체를 사용하거나 직접 획득할 수 있다.

```java
ActivityMainBinding binding;
```

위 처럼 자동으로 생성된 Class 객체를 생성.



위 XML의 TextView에 입력했던 `@{name.nameResult}` 은

name 객체의 type인 MainActivity Class의 nameResult 필드 값을 의미하며 해당 필드의 접근지정자는 반드시 `public` 으로 선언해야 작동한다.



그리고 해당 binding 객체를 이용해 X레이아웃 XML을 지정하고 초기화해주는 작업이 필요하다. 이렇게 해야 XML 을 초기화하고 해당 XML을 이용하기 위한 바인딩 클래스 객체를 반환 받을 수 있다.

```java
binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
```

마지막으로 XML에 `<variable>` 태그로 선언한 데이터를 이용하기 위해 

```java
binding.setname(this);
```

같이 등록해주면 된다.



---

#### ListView, RecyclerView, Fragment 에서 DataBinding 사용하기

위와 같이 직접 LayoutInflater를 이용하는 곳에서 바인딩 클래스를 이용하기 위해서는 바인딩 객체를 만들때 LayoutInflater 객체를 이용하면 된다.



```java
ActivityMainBinding binding = ActivityMainBinding.inflate(inflater, container, false);
```

처럼 사용하면됨.

따로 지정할 레이아웃 id를 인자로 넘겨주지 않아도 되는 이유는 바인드 클래스 이름이 이미 XML의 이름을 이용해 만들어진 것이기 때문이다.

<style color = blue>※ 자세한건 RecyclerView 부분 참고할 것!</style>



## 4. 다양한 타입의 DataBinding

#### (1) Model Class Binding

 모델 클래스는 POJO로 만들어지는데 필드는 모두 private으로 지정하고 getter, setter, 생성자만 잘 생성해주면 된다.

XML에서 variable 변수를 만들어서 사용하면 됨.



#### (2) Resource Binding

위에서 형식 문자를 사용한 것을 한번 설명해봤지만 @string 이라던지 @color 라던지의 리스소의 바인딩도 가능하다.

```xml
<TextView
          android:background="@{data.isErrorMsg ? @color/errorBgColor : @color/normalBgColor}"
          android:text = "@{@string/strResource}"
          />
```

위 처럼 사용 가능.

삼항 연산자도 사용이 가능하다.



#### (3) 컬렉션 타입 Binding

1. 배열 바인딩

   ```java
   intArray = new int[]{10, 20, 30};
   strArray = new String[]{"hello", "world"};
   ```

   위와 같은 배열들은 

   ```xml
   <TextView
             android:text = "@{Integer.toString(data.intArray[0])}"
             />
   <TextView
             android:text = "@{data.strArray[1]}"
             />
   ```

   이렇게 사용 가능하다.

   배열 사용법은 익숙한 그것과 같고 Java.lang 패키지에 포함된 클래스들은 바인딩 식에서도 바로 사용할 수 있다.



2. 리스트 데이터 바인딩

   ```java
   stringList = new ArrayList<>();
   stringList.add("1");
   stringList.add("2");
   
   userList = new ArrayList<>();
   userList.add(new User4("A", "B", "C"));
   userList.add(new User4("D", "E", "F"));
   ```

   이런 List 들도 별반 다르지 않다.

   

   ```xml
   <TextView
             android:text = "@{data.stringList.get(0)}"
             />
   <TextView
             android:text = "@{data.userList.get(0).firstName}"
             />
   ```



3. 맵 데이터 바인딩

   ```java
   map = new HashMap();
   map.put("one","A");
   map.put("two","B");
   ```

   맵이라고 뭐 다를 것은 없다.

   ```xml
   <TextView
             android:text="@{data.map.get(`one`)}"
             />
   ```

   요런 방식인데, 주의할 점이 있다.

   먼저 KEY 값을 "" 큰 따옴표로 묶게 된다면 큰 따옴표가 중복되기 때문에 불가능하다. 따라서 앞뒤를 작은 따옴표로 하거나 위처럼 ` 키를 사용한다.

   큰 따옴표를 `&quot;` 라고 치환해서 사용해도 문제 없다. 

   개인적으로 `를 사용하는 게 편리함



4. 기타 바인딩

   제네릭이나 함수를 사용할 수 있다.

   참고로 홑화살괄호를 사용하기 위해서는 `&lt;` `&gt;` 를 사용해서 해야함!!

   

## 5. BindingAdapter

BindingAdapter는 View의 Attribute(속성)을 Custom하는 방법이다.

Android 기본 UI들은 대부분의 BindingAdapter를 갖고 있지만, `DataBinding`을 사용하다보면 Java Code를 이용하지 않고서는 할 수 없는 상황이 발생할 때가 있다. 이런 경우 사용자정의 속성을 생성하여 쓰겠다는 것이다.



예시로, ImageView를 binding 한다고 가정을 한다면,

```xml
        <ImageView
            android:id="@+id/iv"
            android:layout_width="65dp"
            android:layout_height="65dp"
            app:imageResource="@{models.imageURL}"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            android:scaleType="centerCrop" />
```

위와 같은 XML이 구성이 된다.

`app:imageResource`는 int형 값 (R.drawable.img)에 대한 매치가 되는 속성.



하지만 만약, URL을 이용해서 이미지를 불러오는 방식이라면?

`Glide` 라이브러리를 사용해서  가져올 수 있는 것은 알았다. 그러나, 이는 XML 바인드가 불가능하고 Java Code 내에서 처리해줘야된다는 단점이 있다.



이럴때 BindingAdapter를 사용하면 된다.



---

###### @BindingAdpater 구성하기

먼저, 자바 클래스를 하나 생성하고 그 안에 @BindingAdapter 어노테이션을 이용하여 메서드를 작성해야한다.

```java
public class CustomBindingAdapter {

    @BindingAdapter("bind_image_url")
    public static void bindImageUrl(ImageView view, String res){
        Glide.with(view.getContext()).load(res).into(view);
    }

}
```



CustomBindingAdapter라는 자바 클래스에 bindImageUrl 메서드가 만들어졌다.

이 메서드는 어노테이션으로 @BindingAdatepr가 들어가며 괄호 안의 인자는 실제 XML에서 사용할 속성명이 된다.



편의상 속성명(스네이크 표기)과 함수명(카멜 표기)을 맞췄다.

함수의 첫번째 인자는 해당 View가 될 것이고, 뒤의 인자는 받아올 값이다.

String 형태의 URL을 가져올 것이기 때문에 String이 된다.



내용은 Glide를 이용해, URL을 ImageView에 매칭하는 로직으로 구성되어 있다.



이제 XML에서

```xml
        <ImageView
            android:id="@+id/iv"
            android:layout_width="65dp"
            android:layout_height="65dp"
            app:bind_image_url="@{models.imageURL}"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            android:scaleType="centerCrop" />
```

위와 같이 bind_image_url 이라는 속성을 추가하였고, 정상적으로 잘 동작한다.



이와 같이, 현재 Android 일반 View들의 속성이 내게 맞는 것이 없을 경우

@BindingAdapter 기능을 이용하여 Custom해서 사용하는 방법을 쓰면 간단하게 해결할 수 있다.



---

### 6. Event Binding

###### (1) 함수참조 방식

```xml
<Button
        android:onClick="@{handler::onClickHandler}"
        />
```

위와 같이 `::` 를 이용해서 함수를 지정하는 방식이다.

일반적으로 `.` 를 이용해서 지정하게 되면 컴파일 오류가 발생하게 된다.



단, 주의할 점은 해당 함수 (예시에서는 `onClickHandler`) 는 View 객체 하나를 매개변수로 반드시 가져야한다.

그렇지 않으면 컴파일 오류가 발생하게 됨.



이는 결국 onClick 속성의 한계이다.



###### (2) 리스너 이벤트 방식

람다식을 활용한 방식이다.

```xml
<Button
        android:onClick="@{()->handler.onClickListener(something, someone)}"
        />
```

위와 같은 람다식을 사용하여 함수를 지정하게 되면 함수참조 방식과는 달리 매개변수를 마음대로 가질 수 있다.

```xml
<Button
        android:onClick="@{(view)->handler.onClickListener(view, something, someone)}"
        />
```

View 값도 매개변수로 보낼 수 있음.





### 7. data 구성요소

`<data>` 태그 내에는 `<variable>`태그로 변수를 생성할 수 있었는데, 그 이외에도 다양한 태그를 사용할 수 있다.



1. import 태그

   이 태그는 Java 에서와 정말 비슷하게 동작한다. `<import>` 태그를 이용해 클래스를 선언하면 XML에서 사용할 수 있게 된다.

   ```xml
   <data>
       <import type="com.example.test.MainActivity" alias ="main"></import>
   </data>
   ```

   위와 같은 방식으로 사용한다.

   단, Java에서의 import와는 다르게 모두 포함하는 것을 의미하는  `*`  기호는 사용할 수 없다.

   `alias` 속성으로 name을 지정해서 데이터바인딩 식에 사용할 수 있다.

   

   참고로 java.lang 클래스는 굳이 import 하지 않아도 사용할 수 있음.



2. variable 태그

   위에서 몇번 설명했던 변수를 선언하는 태그이다.

   여기에 선언된 변수는 바인딩 객체에 자동으로 변수명에 해당하는 게터/세터 함수가 등록되고, 이를 통해 XML에서 데이터를 바인딩할 수 있다.





3. Context 객체 받기

   바인딩 식에서는 이미 Context 객체인 context가 내장 변수로서 존재한다.

   ```xml
   <TextView
   		android:text = "@{model.showToast(context)}"
             />
   ```

   따라서, 아무 선언도 필요 없이 위와 같은 방식으로 `context` 변수를 XML에서 매개변수로 사용할 수 있다.

   

   추가로 context를 활용한 대부분이 가능하니 아래와 같은 식도 쓸 수 있다.

   ```xml
   <TextView
   		android:text = "@{((AppCompatActivity)context).finish()}"
             />
   
   <TextView
   		android:text = "@{context.getResources().getString(R.string.hello)}"
             />
   ```

   MVVM을 적용하고 데이터바인딩을 사용하면서 점점 View와 관련된 로직이 XML로 이관되는 것을 느낄 수가 있다.

   

4. class 속성

   `<data>` 태그에는 class 속성을 넣을 수 있다. 이는, XML을 위해 자동으로 만들어지는 바인딩 클래스의 경로/이름을 지정할 수 있다.

   ```xml
   <data class="com.example.MyBindingClass"></data>
   ```

   이러면 원래 레이아웃의 파일명을 변경한 형식이 아닌, class에 명시되어 있는 파일명과 경로로 생성되게 된다.

   



5. include

   한 XML에 작성된 내용 중 일부분을 다른 XML로 작성해서 포함하는 include 기법이라는게 있다고 한다.

   하지만 유용하게 쓸 수 있을 것 같지는 않으니 이런게 있다는 것만 알아두고 필요할 때 다시 알아보는게 좋겠다.





### 끝마침

데이터바인딩을 처음 배울때는 까면 깔수록 계속 나오는 양파와 같았다.

기존에 사용하고 익숙하던 문법의 고정관념을 버리고 새롭게 데이터바인딩에 대한 체계를 섞어야 했으니..



그래도 지속적으로 학습하고 다양한 방면에서 사용하는 방법을 익히고 나니

확실히 데이터바인딩을 쓰면 코드가 간결해지고 직관적이고 효율적이게 된다는 것을 알게 되었다.

어차피 MVVM 아키텍쳐 구조를 적용하려면 무조건 도입해야하는 기술이기도 했고.. ㅎㅎ



물론 아직은 햇병아리 수준이지만 지속적으로 사용하면서 완벽하게 내 것으로 만들어봐야겠다.