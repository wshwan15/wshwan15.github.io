---
layout : post
title : "Common BindingAdapter"
subtitle : "자주 쓰는 BindingAdapter 정리"
category : library
tags : lib-android
---

# Common BindingAdapter

안드로이드 개발 시 공통적으로 자주 사용하는 BindingAdpater를 정리해두는 Library이다.



#### LAST UPDATE 2020-08-07



###### BindingAdapter Class

```java
public class CustomBindingAdapter {

    /**
     * Out Of Memory 를 방지하기 위해 Glide를 사용하여 이미지 넣음
     */
    @BindingAdapter(value="bind_src")
    public static void bindSrc(ImageView view, Drawable res){
        Glide.with(view.getContext()).load(res).into(view);
    }
    
    
    /**
     * HTML 속성 적용 해줌
     */
    @BindingAdapter("bind_text")
    public static void bindText(TextView view, String res){
        view.setText(Html.fromHtml(res));
    }
    

}

```

