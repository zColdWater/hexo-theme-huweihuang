---
title: "[Android] Broadcast"
catalog: true
toc_nav_num: true
date: 2019-09-14 15:45:30
subtitle: "学习使用Broadcast"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/computer-1245714.jpg"
tags:
- Android
catagories:
- Android
---

Demo: https://github.com/zColdWater/broadcast-demo  

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/broadcast1.png" height="350" />

# Broadcast

> 广播现在只能动态注册了，不能静态注册了。

Android当中的广播分

* 全局广播: 发送广播可以给多个App接收到
* App内部广播: 只有本App内部才可以接收到


## 全局广播(新版本只支持动态注册)
> 按顺序操作

全局广播分为两种发送方式

* 无序广播: 比如2个接收者，发送广播后，他们部分先后会接收到。
* 有序广播: 比如2个接收者，发送广播后，是有先后顺序的，比如接收者1的priority属性值大于接收者2，那么1会先接到广播，并且还有权利截断广播，不继续传递给接收者2

1. 创建广播子类
```Java
    class NetworkReceiver extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            // 接收回调
            Log.d("NetworkReceiver","接到通知");
            Toast.makeText(context, "接到通知", Toast.LENGTH_SHORT).show();
        }
    }
```
2. 注册部分
```Java
        private IntentFilter intentFilter;
        private NetworkReceiver networkReceiver;

        // 创建动态通知
        intentFilter = new IntentFilter();
        intentFilter.addAction("broadcast.dynamic.demo.action");
        networkReceiver = new NetworkReceiver();
        registerReceiver(networkReceiver,intentFilter);
```
3. 取消注册部分
```Java
        //取消注册全局广播接收器
        unregisterReceiver(networkReceiver);
```

4. 调用部分
```java
        Button button1 = findViewById(R.id.button1);
        button1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Intent intent = new Intent("broadcast.dynamic.demo.action");
                
                // 发送无序通知
                sendBroadcast(intent);

                // 发送有序通知
                // 发送有序通知 第一个参数 intent 第二个参数 与权限相关 这里传null
                // 发送有序广播 需要注册广播的时候设置 priority 参数，在所有可以接收广播的receiver中，哪个
                // priority值大，哪个会先收到广播，并且还可以截断广播不给后面传递。
                sendOrderedBroadcast(intent,null);
            }
        });
```

## App内广播(只支持动态注册)

1. 创建广播子类
```Java
    class LocalBroadCastReceiver extends  BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            Log.d("LocalBroadCastReceiver","接到通知");
            Toast.makeText(context, "接到通知", Toast.LENGTH_SHORT).show();
        }
    }
```

2. 注册部分
```Java
        // 创建动态本地通知
        localBroadcastManager = LocalBroadcastManager.getInstance(this);
        localBroadCastReceiver = new LocalBroadCastReceiver();
        localIntentFilter = new IntentFilter("localBroadcast.static.demo.action");
        localBroadcastManager.registerReceiver(localBroadCastReceiver, localIntentFilter);
```

3. 取消注册部分
```Java
        //取消注册应用内广播接收器
        localBroadcastManager.unregisterReceiver(localBroadCastReceiver);
```

4. 调用部分
```Java
        Button button3 = findViewById(R.id.button3);
        button3.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Log.d("本地静态通知","发送通知");
                Intent intent = new Intent();
                intent.setAction("localBroadcast.static.demo.action");
                localBroadcastManager.sendBroadcast(intent);
            }
        });
```