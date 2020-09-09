---
layout : post
title : "Firebase Analytics"
subtitle : "Firebase Analytics"
category : development
tags : android
---

# Firebase Analytics

로그 기능을 구현하기 위해서 알아본 Analytics.

기존에 있었던 Google Analytics와는 다른 제품이다.

<br>

일단 내가 가장 필요했던 기능인 `Behaivor Flow` 기능은 'Firebase Analytics' 에는 없었다.

Event를 기록할 수는 있지만, BigQuery를 응용하지 않고서는 그저 event가 몇번 쌓였는지? 정도만 확인할 수 있는 것으로 보인다.

<br>

그리고 발생한 event들이 대쉬보드에 반영되기까지 시간이 좀 걸린다. (최대 하루정도?)



---

### Event 대쉬보드

![analytics1](/assets/img/2020-09-09/analytics1.png)

발생했던 Event를 위 처럼 볼 수는 있다.

기간내에 어떤 Event 종류가 몇명의 사용자에게 몇번 발생했는지 확인할 수 있는데, 내가 원하는 시간의 흐름대로 로그를 볼 수가 없다는 것.

<br>

![analytics2](/assets/img/2020-09-09/analytics2.png)

Event를 클릭하면 위 화면 같이 나온다. (event 별로 화면 UI 구성은 좀 다른 것 같음)

이미지 우측 아래에 표시되는 사용자 참여도는 어떤 클래스를 얼마나 점유했는가? 같은 정보인 것 같은데..

이런건 좀 재미있는 것 같다.



<br>

<br>

그리고 위 Event 대부분이 자동으로 수집된 Event이다. 내가 임의로 발생시켜본 것은 'select_content' 요거 2번 뿐.

자동으로 수집되는 이벤트 종류는 구글 공식 문서인

https://support.google.com/firebase/answer/6317485?hl=ko&ref_topic=6317484

위 문서 에서 볼 수 있다.

<br>

문서를 참고해보면 `screen_view` Event는 화면 전환이 발생한 뒤 기준 중 하나가 충족될 때 수집된다고 한다.

조건을 확인해보면 대충 Activity화면이 전환될 때 거의 수집될 것 같다.

<br>

---

### 마침

Firebase Analytics는 사실 당장 지금 필요한 기능은 아니지만 익혀두면 꽤 유용하게 쓸만한 기능들이 많아는 보인다.

만약, 제대로 사용할 거라면 `BigQuery`와 연동해서 쓰는게 좋겠지만 아무래도 유료다 보니까 고민이 필요할 듯 보인다.