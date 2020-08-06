---
layout : post
title : "RecyclerView"
subtitle : "RecyclerView 연습"
category : development
tags : android
---

# RecyclerView

항상 간단한건 ListView를 쓰고 RecyclerView는 복붙해서만 쓰다가 제대로 정리해보고자 글을 쓰게 되었다.



---

### 1. Build.gradle 설정

```java
implementation 'com.android.support:recyclerview-v7:28.0.0'
```

위와 같은 dependencies 추가



---

### 2. XML

XML은 크게 RecyclerView가 들어가는 Main 레이아웃과 실제 표시되는 Adapter(Item) 레이아웃으로 나뉜다고 보고 있다.



먼저 RecyclerView는 설명할 것 없이 그냥 위치만 맞춰주고 id 설정해주면 끝.

이는 `DataBinding` 적용 시에도 같다.

```xml
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recycler"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        app:layout_constraintTop_toBottomOf="@id/title"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"/>
```



그리고 Adapter에 들어갈 Item 레이아웃도 XML 내에서 원하는 구도에 맞춰서 작성해주면 끝.

`DataBinding` 을 적용할 때에는 RecyclerView 만을 위해서 따로 적용해야하는 것은 없고 일반적인 데이터바인딩 그대로 구성하면 된다.

즉, data 태그 내에 Model 클래스 타입의 variable 을 추가해주고, 이를 적절하게 View에 bind 해주면 됨.



예제에서 유의해야할 것은

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

위와 같이 ImageView의 경우 R.drawable.img 등과 같은 int id 값이 들어가는 형식은 `app:imageResource`에 bind 해줘야됨.



다만, `android:src` 에 URL 들어가는 상황 같이 `Glide` 을 써야하는 등의 경우들에는 `BindingAdapter`로 View의 속성을 Custom해서 씀.



---

## 3. Java

JavaCode는 크게 4가지로 나뉘어 있다.

첫번째는, RecyclerView 객체를 생성하는 MainView

두번째는, RecyclerAdapter Class

세번째는, ViewHolder Class

네번째는, Model Class.



---

#### (1) MainView

###### 일반

```java
        ArrayList<Model> list = new ArrayList<>();

        for(int i=0; i<10; i++){
            list.add(new Model(i+"개", R.drawable.backup));
        }

        RecyclerView rv = findViewById(R.id.recycler);
        rv.setLayoutManager(new LinearLayoutManager(this));
        rv.setAdapter(new adapter(list));
```

보통 위와 같은 형식을 따를 것 같다.



###### DataBinding

```java
    ActivityMainBinding binding;
```

```java
        binding = DataBindingUtil.setContentView(this,R.layout.activity_main);
        binding.setMain(this);

        for(int i=0; i<10; i++){
            list.add(new Model(i+"개", R.drawable.backup));
        }

        binding.recycler.setLayoutManager(new LinearLayoutManager(this));
        binding.recycler.setAdapter(new adapter(list));
```

데이터바인딩 형식을 따라주면 똑같다.



여기서 한가지 추가할 사항으로는

```java
            for(int i=0; i<10; i++){
                MyAdapter.list.get(i).setTitle((i+10) + "개");
                MyAdapter.list.get(i).setImageURL(R.drawable.ic_launcher_background);
            }
```

만약 위처럼 adpater의 데이터를 변경했다면 이를 적용시키기 위해 LiveData를 쓰는 것인데,

사실 LiveData를 쓰지 않고 

```java
MyAdapter.notifyDataSetChanged();
```

를 한번 호출해주면 되기 때문에 패스!



#### (2) RecyclerAdapter Class

###### 일반

```java
    private class adapter extends RecyclerView.Adapter<MyViewHolder> {

        private ArrayList<Model> list;

        public adapter(ArrayList<Model> list) {
            this.list = list;
        }

        @NonNull
        @Override
        public MyViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
            View view = LayoutInflater.from(parent.getContext())
                    .inflate(R.layout.recycler_item, parent, false);
            return new MyViewHolder(view);
        }

        @Override
        public void onBindViewHolder(@NonNull MyViewHolder holder, int position) {
            Model model = list.get(position);
            holder.title.setText(model.getTitle());
            Glide.with(getApplicationContext()).load(model.getImageURL()).into(holder.iv);
        }

        @Override
        public int getItemCount() {
            return list.size();
        }

    }
```

RecyclerView Adapater의 포인트는

1. 생성자로부터 Model 클래스 인자를 받아오는 것. 

2. onCreateViewHolder 메서드에서 inflate 하고 ViewHodler로 return 해주는 것.
3. onBindViewHolder 메서드에서 비즈니스 로직을 코딩하는 것.



###### DataBinding

```java
    private class adapter extends RecyclerView.Adapter<MyViewHolder> {

        private ArrayList<Model> list;

        public adapter(ArrayList<Model> list) {
            this.list = list;
        }

        @NonNull
        @Override
        public MyViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
            RecyclerItemBinding binding =RecyclerItemBinding.inflate(LayoutInflater.from(parent.getContext()), parent, false);
            return new MyViewHolder(binding);
        }

        @Override
        public void onBindViewHolder(@NonNull MyViewHolder holder, int position) {
            Model model = list.get(position);
            holder.bind(model);
        }

        @Override
        public int getItemCount() {
            return list.size();
        }

    }
```

데이터바인딩 시에 inflate 하는 방식이 다르고,

중요한 포인트는 onBindViewHolder 메서드에서 ViewHodler 객체의 bind 메서드를 한번 호출하기만 하면 전부 매칭된다는 것이다.

물론 XML에서 수고를 한거긴 하지만 Java Code 상에서는 매우 깔끔한 형태를 지니게 되었다고 볼 수 있게 되었다.



---

#### (3) ViewHodler

###### 일반

```java
    private class MyViewHolder extends RecyclerView.ViewHolder{

        public TextView title;
        public ImageView iv;
        public MyViewHolder(@NonNull View itemView) {
            super(itemView);

            title = itemView.findViewById(R.id.title);
            iv = itemView.findViewById(R.id.iv);

        }
    }
```

뷰홀더는 최초 1회만 findViewById 하기 위한 클래스이다.

뷰홀더가 없으면 지속적으로 매칭하기 때문에 CPU 부하가 많이 발생하기 때문.



###### DataBinding

```java
    private class MyViewHolder extends RecyclerView.ViewHolder{

        RecyclerItemBinding binding;
        
        public MyViewHolder(RecyclerItemBinding binding) {
            super(binding.getRoot());
            this.binding = binding;
        }

        public void bind(Model model){
            this.binding.setModels(model);
        }
    }
```

바인딩 객체를 생성하고 bind 메소드를 만들어서 set 해준다. 이때, Model Class 객체를 인자로 받음

이러면 자동으로 연결 된다.

생성자의 인자와 super(binding.getRoot()) 도 유의할 것.



---

#### (4) Model Class

모델 클래스는 설명할 게 있을까? 다 똑같다.

```java
public class Model {

    private String title;
    private int ImageURL;

    public Model(String title, int imageURL) {
        this.title = title;
        ImageURL = imageURL;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public int getImageURL() {
        return ImageURL;
    }

    public void setImageURL(int imageURL) {
        ImageURL = imageURL;
    }
}

```



## 4. 끝맺음

여러모로 RecyclerView는 오래 사용했었으나, 제대로 구조를 이해하지 못했었는데.

데이터바인딩을 적용하면서 정리할 기회를 가질 수 있어서 좋았다.