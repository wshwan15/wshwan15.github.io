---
layout : post
title : "ConstarintLayout"
subtitle : "콘스트레인트 적용하기"
category : development
tags : android
---

# Android Constraint Layout

항상 익숙하다는 핑계로 LinearLayout만 주구장창 써오다가, 리팩토링을 계기로 ConstraintLayout을 학습하게 되었다.



그리고 실제로 써보니까 훨씬 효율적인 것 같고 편리한 것 같은 느낌이 들었다. (사실 여기저기서 효율적이다고 해서 강제로 주입된 느낌일지도 모름..)



LinearLayout에서 할 수 있었던 것을 Constarint에서도 할 수 있도록 학습하는 시간이 필요할 것 같고, Linear보다 더 좋은 기술도 있으므로 익숙해지기만 한다면 Constarint를 사용하지 않을 이유는 없었다.



Constarint는 2016년에 나왔으며, Relative와 매우 유사하게 동작하는 것 같다.



---

### Constarint 문법



###### 1. constarint

```xml
<TextVIew
          app:layout_constarintLeft_toLeftOf="parent"
          app:layout_constarintLeft_toRightOf="parent"
          app:layout_constarintRight_toLeftOf="parent"
          app:layout_constarintRight_toRightOf="parent"
          app:layout_constarintTop_toTopOf="parent"
          app:layout_constarintTop_toBottomOf="parent"
          app:layout_constarintBottom_toTopOf="parent"
          app:layout_constarintBottom_toBottomOf="parent"
          />
```

Parent 혹은 View를 기준으로 상대적인 위치를 지정하는 방법이다.

2가지의 방향을 표시하는 것을 확인할 수 있는데, 앞에 나오는 방향 (constraintLeft 의 경우 Left)과 뒤에 나오는 방향 (toRightOf 의 경우 Right)은 각각 해당 View의 방향(Left)을 상대 View의 방향(Right)에 붙이라는 의미이다.



`android:layout_constarintBottom_toBottomOf="parent"` 예를 들어 해당 표시는 해당 View의 Bottom을 parent의 Bottom에 붙이라는 뜻.

parent는 상위 View(부모)를 의미하고 parent가 아닌 상대 View인 경우에는 id값을 지정하면 된다.

(방향을 나타내는 것은 이외에도 start, end, base가 있음. 조합 방식은 똑같다.)



만약, View를 중앙에 배치하고 싶다면 아래와 같이 상반되는 속성을 사용하면 된다.

```xml
<TextVIew
          app:layout_constarintLeft_toLeftOf="parent"
          app:layout_constarintRight_toRightOf="parent"
          />
```

이 경우, 해당 View는 왼쪽과 오른쪽을 모두 지정했고, 따라서 상대 View 사이 중앙에 위치하게 된다.



###### 2.margin

```xml
<TextView
	     android:layout_marginLeft="10dp"
         android:layout_goneMarginLeft="10dp"
          />
```

margin은 사실 Linear에서도 공통적으로 사용한다.

그럼에도 서술한 이유는 바로 상대 View가 `gone`상태가 되었을 때 적용되는 속성이 있기 때문이다.

이를 이용해, 상대 View가 있을 때의 margin과 사라졌을 떄(gone)의 margin을 지정할 수 있다.



###### 3. bias

```xml
<TextView
          app:layout_constraintHorizontal_bias="0.2"
          app:layout_constraintVertical_bias="0.2"
          />
```

1번에서 설명했던 Constraint 속성을 상반되게 적용하면 중앙에 배치된다는 것과 연관된 속성이다. `bias`는 그 상태에서 어느 쪽에 치우칠 지를 설정할 수 있다.



예를 들어 `app:layout_constarintVertical_bias="0.2"`를 지정한 경우, 좌측에서 20% 위치에서 나타나게 된다 (default는 정중앙이므로 50% 위치에 배치되었음)



###### 4. Ratio

```xml
<TextView
          android:layout_width="wrap_content"
          android:layout_height="0dp"
          app:layout_constraintDimensionRatio="1:1"
          />
```

Ratio는 View의 가로 세로 비율에 의한 크기를 지정할 수 있는 속성이다. 

먼저, `width`와 `height` 속성 중 하나에 크기를 지정하고 나머지 속성은 반드시 '0dp'로 지정해준다.

그리고 `app:layout_constraintDimensionRatio` 속성에 지정한 비율대로 View의 크기가 설정된다.



예를 들어, `width`가 100dp고 `height`가 0dp, `Ratio`가 1:1 이라면 View의 `width`와 `height`는 각각 100dp씩 설정된다.

`Ratio`가 1:2 라면 `width`는 100dp, `height`는 200dp가 된다.

<br>

여기서 추가로 `width`와 `height`를 모두 0dp로 지정할 수 있는데, 이 경우 `Ratio`만으로 크기가 지정되게 된다. 이때, W와 H를 이용해 무엇을 기준으로 할지 설정할 수 있다. 

("H, 2:1")은 Height를 기준으로 2:1, ("W, 1:2")는 Width를 기준으로 1:2 라는 의미



###### 5. weight

사실, wieght라는 속성이 있긴 하지만, 조금 다르게 사용한다.

상반된 constraint를 지정한 뒤에, `width` 혹은 `height`를 0dp로 지정하면 꽉 채우게 된다.



만약 한개의 경우라면 `wieght`가 몇이든 간에 같은 결과 값을 갖게 되니 상관없지만 

여러 개의 View를 지정하게 된다면

```xml
<TextView
          app:layout_constraintHorizontal_weight="1"
          app:layout_constraintVertical_weight="1"
          />
```

위 와 같은 속성을 사용할 수 있다.

수직일 경우 Vertical, 수평일 경우 Horizontal을 사용하면 됨

(이 경우에도 0dp로 해줘야 적용이 됨)





---

### 끝마침

위에서 설명한 것 이외의 대부분의 속성은 LinearLayout과 같다.



Constraint가 퍼포먼스가 더 좋다고 하니, 굳이 Linear를 쓸 필요는 없겠지.