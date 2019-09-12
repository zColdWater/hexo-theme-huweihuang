---
title: "[Android] Intent-Activity"
catalog: true
toc_nav_num: true
date: 2019-09-12 15:00:30
subtitle: "如何使用Intent唤起Activity"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/computer-1245714.jpg"
tags:
- Android
catagories:
- Android
---

这篇文章主要介绍Intent在Activity当中的使用。

Demo放在: https://github.com/zColdWater/activity-intent-demo 

# Intent 

意图(Intent)是要执行的操作的抽象描述。它可用于Context#startActivity(Intent)启动Activity， broadcastIntent将其发送到任何感兴趣的BroadcastReceiver组件，和/ Context.startService(Intent)或 Context.bindService(Intent, ServiceConnection, int)与后台通信Service。  

意图(Intent)提供了一种工具，用于在不同应用程序中的代码之间执行延迟运行时绑定。它最重要的用途是唤起Activity，它可以被认为是Activity之间的粘合剂。它基本上是一个被动数据结构，包含要执行的动作的抽象描述。


# 使用方式

## 显式Intent跳转

```java

··· MainActivity
// 从 MainActivity 跳转到 IntentActivityOne
Intent intent = new Intent(MainActivity.this,IntentActivityOne.class);
startActivity(intent);
···

```

## 显式Intent跳转 携带参数

```java

··· MainActivity
// 从 MainActivity 跳转到 IntentActivityOne 并且携带参数 Key:Value
Intent intent = new Intent(MainActivity.this, IntentActivityTwo.class);
intent.putExtra("name","henry");
startActivity(intent);
···

··· IntentActivityTwo
// 获取参数
Intent intent = getIntent();
Bundle bundle = intent.getExtras();
if (null != bundle) {
    String name = bundle.getString("name");
    Toast.makeText(this, String.format("Name is %s",name), Toast.LENGTH_SHORT).show();
}
···

```

## 显式Intent跳转 携带参数 并且 回调参数
```java

··· MainActivity
// 从 MainActivity 跳转到 IntentActivityTree 传参数 并且 接收IntentActivityTree发来的回调
Intent intent = new Intent(MainActivity.this, IntentActivityTree.class);
intent.putExtra("name","henry");
startActivityForResult(intent,1);

// 重写系统接收Activity回调方法
@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    Log.d("Activity","onActivityResult");
    switch (resultCode) {
        case 1:
            Toast.makeText(this, String.format("接收到回调参数:%s",data.getStringExtra("name")), Toast.LENGTH_SHORT).show();
            break;
        default:
            break;
    }
}
···

··· IntentActivityTree
Intent intent = new Intent();
intent.putExtra("name", "Henry");
IntentActivityTree.this.setResult(1, intent);
IntentActivityTree.this.finish();
···

```

## 显式Intent跳转 携带Model数据结构参数

```java

··· MainActivity

// Staff Model Define
public class Staff implements Serializable {
    public String name;
    public void setName(String name) { this.name = name; }
    public String getName() { return name; }
}


// 从MainActivity跳转到IntentActivityFour 携带Model数据结构参数
Staff staff = new Staff();
staff.setName("Henry");
Intent intent = new Intent(MainActivity.this, IntentActivityFour.class);
intent.putExtra("staff",staff);
startActivity(intent);
···

··· IntentActivityFour
// 获取参数MainActivity传来的参数
Intent intent = getIntent();
Bundle bundle = intent.getExtras();
Staff staff = (Staff)bundle.getSerializable("staff");
Toast.makeText(this, String.format("staff name:%s",staff.name), Toast.LENGTH_SHORT).show();
···

```



## 隐式Intent跳转 携带Action

## 隐式Intent跳转 携带Action+Category

## 隐式Intent跳转 使用Data模式

## 隐式Intent跳转 系统应用 浏览器网页

## 隐式Intent跳转 系统应用 调用电话拨号（不需要拨号权限）

## 隐式Intent跳转 系统应用 调用电话直接拨号（需要拨号权限）

## 隐式Intent跳转 系统应用 调用短信程序（无需发送短信权限，接收者自填）

## 隐式Intent跳转 系统应用 调用短信程序（无需发送短信权限）

## 隐式Intent跳转 系统应用 调用邮件程序

## 隐式Intent跳转 系统应用 调用音乐播放器

## 隐式Intent跳转 系统应用 调用视频播放器

## 隐式Intent跳转 系统应用 调用搜索

