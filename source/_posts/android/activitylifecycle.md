---
title: "[Android] Activity 生命周期"
catalog: true
toc_nav_num: true
date: 2019-09-15 13:49:30
subtitle: "介绍 Activity 生命周期"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg"
tags:
- Android
catagories:
- Android
---

Demo: https://github.com/zColdWater/activity-lifecycle   

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/legend_cover.jpg" height="350" />


# 一，Activity的生命周期
MainActivity 测试代码
```Java
package com.example.activity_demo;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;

import android.content.Intent;
import android.os.Bundle;
import android.os.PersistableBundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;

public class MainActivity extends AppCompatActivity {

    // Activity第一次被创建的时候 回调
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Log.d("MainActivity","onCreate");

        if (savedInstanceState != null) {
            String data = savedInstanceState.getString("datakey");
            Log.d("MainActivity","datakey"+data);
        }

        Button btn = findViewById(R.id.button1);
        btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Intent intent = new Intent(MainActivity.this,DetailActivity.class);
                startActivity(intent);
            }
        });
    }

    // Activity由不可见到可见的时候 回调
    @Override
    protected void onStart() {
        super.onStart();
        Log.d("MainActivity","onStart");
    }

    // Activity正在运行的状态，必须是栈的最顶层。
    @Override
    protected void onResume() {
        super.onResume();
        Log.d("MainActivity","onResume");
    }

    // 准备去启动另外一个Activity的时候去调用
    @Override
    protected void onPause() {
        super.onPause();
        Log.d("MainActivity","onPause");
    }

    // Activity完全不可见的时候起调用，比如当弹出一个dialog的时候，会回调onPause并不会回调onStop
    @Override
    protected void onStop() {
        super.onStop();
        Log.d("MainActivity","onStop");
    }

    // Activity被销毁之前调用
    @Override
    protected void onDestroy() {
        super.onDestroy();
        Log.d("MainActivity","onDestroy");
    }

    // Activity由停止状态变为运行状态
    @Override
    protected void onRestart() {
        super.onRestart();
        Log.d("MainActivity","onRestart");
    }


    // Activity被回收之前会 回调
    @Override
    public void onSaveInstanceState(@NonNull Bundle outState, @NonNull PersistableBundle outPersistentState) {
        super.onSaveInstanceState(outState, outPersistentState);

        // 这里把数据保存起来，然后重启开启这个Activity的时候会带入到 onCreate的生命周期回调的参数里
        String data = "这里是假数据";
        outState.putString("datakey",data);
    }
}
```

# 二，Activity的返回栈模式
Manifest 备注四种模式返回栈描述

```XML
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.activity_demo">

    <application
        android:name=".MyApplication"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">


        <!--4种启动模式: 1.standard(默认) 2.singleInstance 3.singleTask 4.singleTop-->
        <!--standard: 每次开启都是一个新的Activity-->
        <!--singleTop: 当Activity已经是栈顶的时候，再开启相同的Activity，则不会生效，认为已经不需要再开启新的相同的Activity了-->
        <!--singleTask: 每次启动该Activity时系统首先会在返回栈种检查时候存在该Activity，如果存在，并把在这个Activity之前的所有Activity出栈-->
        <!--singleInstance: 共享栈，其实就是重新开辟一个全局的栈，其他应用也可以访问这个单独的栈，所有singleInstance的Activity都在这个特殊的栈里-->
        <activity
            android:name=".DetailActivity"
            android:launchMode="standard"
            >

        </activity>

        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />

    <uses-permission android:name="android.permission.INTERNET" />

</manifest>
```


