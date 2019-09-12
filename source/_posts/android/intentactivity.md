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

这里需要注意的，隐式的Intent唤醒Activity需要配置在Manifest文件里面的Activity标签下配置`Intent-fliter`标签，才可以唤醒指定的Activity，一般用于唤醒其他App的应用。

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
```Java
··· MainActivity
// Activity 标签的 IntentFliter 除了添加指定的Action还必须要有Category默认的Default值
Intent intent = new Intent("action_intent_activity");
startActivity(intent);
···

··· Manifest
<activity android:name=".Activitys.IntentActivityOne" >
    <intent-filter>
        <action android:name="action_intent_activity" ></action>
        <category android:name="android.intent.category.DEFAULT"></category>
    </intent-filter>
</activity>
···

```

## 隐式Intent跳转 携带Action+Category
```Java

··· MainActivity
// Activity 标签的 IntentFliter 除了添加指定的Action还必须要有Category默认的Default值
Intent intent = new Intent("action_intent_activity1");
// add的Category(android.intent.custom) 不是必须参数,即使没有添加这个类别也是可以跳转的。
intent.addCategory("android.intent.custom");
startActivity(intent);
··· 

··· Manifest
<activity android:name=".Activitys.IntentActivityTwo" >
    <intent-filter>
        <action android:name="action_intent_activity1" ></action>
        <category android:name="android.intent.custom"></category>
        <category android:name="android.intent.category.DEFAULT"></category>
    </intent-filter>
</activity>
···

```

## 隐式Intent跳转 使用Data模式
```Java

··· MainActivity
//创建一个隐式的 Intent 对象，方法四：Date 数据
Intent intent = new Intent();
Uri uri = Uri.parse("content://com.example.intentdemo：8080/abc.pdf");
//注：setData、setDataAndType、setType 这三种方法只能单独使用，不可共用
//单独以 setData 方法设置 URI
//intent.setData(uri);
//单独以 seType 方法设置 Type
//intent.setType("text/plain");
//上面分步骤设置是错误的，要么以 setDataAndType 方法设置 URI 及 mime type
intent.setDataAndType(uri, "text/plain");
startActivity(intent);
··· 

··· Manifest
<activity android:name=".Activitys.IntentActivityFour">
            <intent-filter>
                <data
                    android:scheme="content"
                    android:host="com.example.intentdemo"
                    android:port="8080"
                    android:pathPattern=".*pdf"
                    android:mimeType="text/plain"/>
                <category android:name="android.intent.category.DEFAULT"></category>
            </intent-filter>
</activity>
···

```

## 隐式Intent跳转 系统应用 浏览器网页
```Java
//web浏览器
Uri uri= Uri.parse("http://www.baidu.com");
Intent intent = new Intent(Intent.ACTION_VIEW, uri);
startActivity(intent);
```

## 隐式Intent跳转 系统应用 地图
```Java
//打开地图查看经纬度
Uri uri = Uri.parse("geo:38.899533,-77.036476");
Intent intent = new Intent(Intent.ACTION_VIEW, uri);
startActivity(intent);
```

## 隐式Intent跳转 系统应用 调用电话拨号（不需要拨号权限）
```Java
Uri uri = Uri.parse("tel:10086");
Intent intent = new Intent(Intent.ACTION_DIAL, uri);//注意区别于下面4.4的action
startActivity(intent);
```

## 隐式Intent跳转 系统应用 调用电话直接拨号（需要拨号权限）
```Java
Uri uri = Uri.parse("tel:15980665805");
Intent intent = new Intent(Intent.ACTION_CALL, uri);//注意区别于上面4.3的aciton
startActivity(intent);
```

## 隐式Intent跳转 系统应用 调用短信程序（无需发送短信权限，接收者自填）
```Java
Intent intent = new Intent(Intent.ACTION_VIEW);
intent.putExtra("sms_body", "这里写短信内容");
intent.setType("vnd.android-dir/mms-sms");
startActivity(intent);
```

## 隐式Intent跳转 系统应用 调用短信程序（无需发送短信权限）
```Java
Uri uri = Uri.parse("smsto:10086");//指定接收者
Intent intent = new Intent(Intent.ACTION_SENDTO, uri);
intent.putExtra("sms_body", "你这个黑心运营商");
startActivity(intent);
```

## 隐式Intent跳转 系统应用 调用邮件程序
```Java
Intent intent = new Intent(Intent.ACTION_SENDTO);
intent.setData(Uri.parse("mailto:xxx@gmail.com"));
intent.putExtra(Intent.EXTRA_SUBJECT, "这是标题");
intent.putExtra(Intent.EXTRA_TEXT, "这是内容");
startActivity(intent);
```

## 隐式Intent跳转 系统应用 调用音乐播放器
```Java
Intent intent = new Intent(Intent.ACTION_VIEW);
//Uri uri = Uri.parse("file:///sdcard/xiong_it.mp4");
Uri uri = Uri.parse("file:///sdcard/xiong_it.mp3");
intent.setDataAndType(uri, "audio/mp3");
startActivity(intent);
```

## 隐式Intent跳转 系统应用 调用视频播放器
```Java
Intent intent = new Intent(Intent.ACTION_VIEW);
//Uri uri = Uri.parse("file:///sdcard/xiong_it.mp3");
Uri uri = Uri.parse("file:///sdcard/xiong_it.mp4");
intent.setDataAndType(uri, "video/mp4");
startActivity(intent);
```

## 隐式Intent跳转 系统应用 调用搜索
```Java
Intent intent = new Intent();
intent.setAction(Intent.ACTION_WEB_SEARCH);
intent.putExtra(SearchManager.QUERY, "android");
startActivity(intent);
```
