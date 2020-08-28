---
layout : post
title : "Android AlertDialogUtil"
subtitle : "AlertDialogUtil Class"
category : library
tags : lib-android
---

# Android AlertDialogUtil

AlertDialog를 운용하기 위한 Util Class 이다.



싱글톤 형식으로 구성되어 있으며,  미리 Context를 받아 저장해둬서 사용한다.

단순하게 Title과 Message를 매개변수로 받아서 표시해주는 함수를 포함하여 Consumer를 활용해 event도 가져올 수 있다.





LAST UPDATE : 2020-08-28

```java
import android.app.Activity;
import android.app.AlertDialog;
import android.app.NativeActivity;
import android.app.ProgressDialog;
import android.content.Context;
import android.content.DialogInterface;
import android.os.Build;
import android.os.Handler;

import java.util.function.Consumer;

public class AlertDialogUtil {

    Context context;
    Activity activity;
    ProgressDialog progressDlg = null;
    //
//    AlertDialog.Builder alertbox;
    private AlertDialog alertbox;
    private AlertDialog.Builder builder;

    //Singleton
    private AlertDialogUtil(){}
    public static AlertDialogUtil getInstance(Activity activity){
        LazyHolder.Instance.setActivity(activity);
        return LazyHolder.Instance;
    }
    public static AlertDialogUtil getInstance(){
        return LazyHolder.Instance;
    }
    private static class LazyHolder{
        private static final AlertDialogUtil Instance = new AlertDialogUtil();
    }


    /* ------------------------------------------------------------------------------------------- */
    //Method

    /**
     * Message만 있음
     * @param alertmsg
     */
    public void setAlertDialog(String alertmsg){
        builder = new AlertDialog.Builder(activity);
        builder.setMessage(alertmsg);
        builder.setPositiveButton("OK", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
            }
        });
        alertbox = builder.show();
    }


    /**
     * Message와 Title이 있음
     * @param alertmsg
     * @param alertTitle
     */
    public void setAlertDialog(String alertmsg, String alertTitle){
        builder = new AlertDialog.Builder(activity);
        builder.setTitle(alertTitle);
        builder.setMessage(alertmsg);
        builder.setPositiveButton("OK", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
            }
        });
        alertbox = builder.show();
    }


    /**
     * Message와 Title과 버튼 이름을 정할 수 있음
     * @param alertmsg
     * @param alertTitle
     * @param positiveName
     */
    public void setAlertDialog(String alertmsg, String alertTitle, String positiveName){
        builder = new AlertDialog.Builder(activity);
        builder.setTitle(alertTitle);
        builder.setMessage(alertmsg);
        builder.setPositiveButton(positiveName, new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
            }
        });
        alertbox = builder.show();
    }


    /**
     * Consumer 버전 Only Message
     * @param alertmsg
     * @param consumer
     */
    public void setAlertDialog(String alertmsg, Consumer consumer){
        builder = new AlertDialog.Builder(activity);
        builder.setMessage(alertmsg);
        builder.setPositiveButton("OK", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
                    consumer.accept("");
                }
            }
        });
        alertbox = builder.show();
    }

    /**
     * 포지티브 액션, Message, Title
     * @param alertmsg
     * @param alertTitle
     * @param consumer
     */
    public void setAlertDialog(String alertmsg, String alertTitle, Consumer consumer){
        builder = new AlertDialog.Builder(activity);
        builder.setTitle(alertTitle);
        builder.setMessage(alertmsg);
        builder.setPositiveButton("OK", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
                    consumer.accept("");
                }
            }
        });
        alertbox = builder.show();
    }


    /**
     * 포지티브 액션, Message, Title, Button Name
     * @param alertmsg
     * @param alertTitle
     * @param PositiveName
     * @param consumer
     */
    public void setAlertDialog(String alertmsg, String alertTitle, String PositiveName, Consumer consumer){
        builder = new AlertDialog.Builder(activity);
        builder.setTitle(alertTitle);
        builder.setMessage(alertmsg);
        builder.setPositiveButton(PositiveName, new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
                    consumer.accept("");
                }
            }
        });
        alertbox = builder.show();
    }

    /**
     * 포지티브/네거티브 액션, Message, Title, Button Name
     * @param alertmsg
     * @param alertTitle
     * @param PositiveName
     * @param NegativeName
     * @param consumerPositive
     * @param consumerNegative
     */
    public void setAlertDialog(String alertmsg, String alertTitle, String PositiveName, String NegativeName, Consumer consumerPositive, Consumer consumerNegative){
        builder = new AlertDialog.Builder(activity);
        builder.setTitle(alertTitle);
        builder.setMessage(alertmsg);
        builder.setPositiveButton(PositiveName, new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
                    consumerPositive.accept("");
                }
            }
        });
        builder.setNegativeButton(NegativeName, new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
                    consumerNegative.accept("");
                }
            }
        });
        alertbox = builder.show();
    }


    /**
     * Dialog Dismiss 함수
     */
    public void dismissAlertDialog(){
        if(alertbox != null && alertbox.isShowing()){
            alertbox.dismiss();
        }
    }
    
    public void setCancelable(boolean bool){
        builder.setCancelable(bool);
    }



    /* ------------------------------------------------------------------------------------------- */
    //Getter & Setter
    public void setActivity(Activity activity) {
        this.activity = activity;
    }
}

```

