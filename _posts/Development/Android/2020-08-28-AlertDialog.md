---
layout : post
title : "AlertDialog"
subtitle : "Android AlertDialog 사용하기"
category : development
tags : android
---

# AlertDialog 와 Builder

기존에는 AlertDialog.Builder 를 사용해서 Title이나 Message, 버튼들을 넣고 .show() 메서드를 이용해 사용하는게 끝이었는데,

dismiss() 기능을 추가하려고 보니 가능하지가 않았다.



이와 관련하여 구글링을 조금 해보니 AlertDialog 와 AlertDialog.Bulder는 좀 차이점이 있는 것으로 확인 되었다.





---

### AlertDialog

```java
private AlertDialog alertbox;
```

AlertDialog 변수를 만든다.



### AlertDialog.Builder

```java
AlertDialog.Builder builder = new AlertDialog.Builder(Activity.this);
builder.setTitle(String)
builder.setMessage(String);
builder.setPositiveButton("BTN", new DialogInterface.OnClickListener(){
    @Override
    public void onClick(DialogInterface dialog, int which){
        
    }
});
```

평소처럼 Builder를 이용하여 타이틀/메세지/버튼 리스너 등을 정의한다.

그리고 `builder.show()` 를 통해 Dialog를 보여줄 수 있었다.



그러나 이 경우에는 `builder.dismiss()` 메서드가 먹히지 않아서 Dialog를 원하는 때에 제거할 수가 없었다.



따라서 위에 했었던 것 처럼 AlertDialog 변수를 하나 정의한 뒤에

```java
alertbox = builder.show();
```

위 같은 방식으로도 똑같이 Dialog를 보여줄 수 있고

```java
alertbox.dismiss();
```

`dismiss()`도 확실하게 적용되는 것을 확인할 수 있다.



---

### 마침

다른 사람들은 당연히 알고 있었을 수도 있었겠지만, 나는 이번에 처음으로 알게된 지식이었고 유용하게 사용할 수 있었다.

더 유용한 사용법이 있을 것 같다.