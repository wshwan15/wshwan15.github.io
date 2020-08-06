---
layout : post
title : "Common Dependencies"
subtitle : "자주 쓰는 dependencies 정리"
category : library
tags : android
---

# Common build.gradle

안드로이드 개발 시 `build.gradle`에서 공통적으로 자주 사용하는 Dependencies를 정리해두는 Library이다.



#### LAST UPDATE 2020-08-06

<br>

###### android

```java
//DataBinding
dataBinding{
        enabled = true;
    }
```

<br>

###### Dependencies

```java
	//RecyclerView
	implementation "androidx.recyclerview:recyclerview:1.1.0"
    //MVVM
    implementation "android.arch.lifecycle:extensions:2.2.0"
    implementation "androidx.lifecycle:lifecycle-viewmodel:2.2.0"
    //Retrofit
    implementation "com.squareup.retrofit2:retrofit:2.5.0"
    implementation "com.google.code.gson:gson:2.8.5"
    implementation "com.squareup.retrofit2:converter-gson:2.5.0"
    implementation 'com.squareup.retrofit2:converter-jackson:2.2.0'
    implementation 'com.squareup.retrofit2:adapter-rxjava2:2.2.0'
    //TedPermission
    implementation "gun0912.ted:tedpermission:2.0.0"
    //Glide
    implementation 'com.github.bumptech.glide:glide:4.10.0'
    //LiveData
    implementation 'androidx.appcompat:appcompat:1.1.0'
    //RXjava
    implementation 'io.reactivex.rxjava2:rxandroid:2.0.1'
    implementation 'io.reactivex.rxjava2:rxjava:2.1.0'
```





