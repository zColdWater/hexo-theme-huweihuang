---
title: "[Android] Service"
catalog: true
toc_nav_num: true
date: 2019-09-14 10:50:30
subtitle: "简单使用Android中的Service"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/computer-1245714.jpg"
tags:
- Android
catagories:
- Android
---

Demo: https://github.com/zColdWater/ServiceDemo 
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/service5.jpeg" height="350" />

# 一, 准备工作

1. 先准备4个Button，分别是开启服务，关闭服务，绑定服务，执行IntentService。

2. 实现4个Button的Click回调。

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/service1.png" height="350" />

<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/service2.png" height="350" />


# 二, 开始写Service

Service有三种写法

* 后台运行和App无回调交互 (本文有介绍 类型一)
* 前台配合通知 类似下载通知栏效果 (本文没有介绍)
* 后台绑定和App有回调交互 (本文有介绍 类型二)

## 类型一: Service

#### 1.后台运行模式 无法与App进行交互

> 此种模式，一旦服务开启了，就和App没有关系了，就会一直后背运行，除非你手动停止服务。

1. 先创建MyService继承Service
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/service3.png" height="350" />

```Java
// 备注: 在Service里面调用 stopSelf() 方法就可以让服务停下来
public class MyService extends Service {

    public MyService() {}

    // 服务创建时候调用
    @Override
    public void onCreate() {
        super.onCreate();
        Toast.makeText(this, "服务创建", Toast.LENGTH_SHORT).show();
        Log.d("MyService","服务创建");
    }

    // 每次服务启动的时候调用
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        Toast.makeText(this,"服务开启",Toast.LENGTH_SHORT);
        Log.d("MyService","服务开启");
        return super.onStartCommand(intent, flags, startId);
    }

    // 服务销毁的时候调用
    @Override
    public void onDestroy() {
        super.onDestroy();
        Toast.makeText(this,"服务销毁",Toast.LENGTH_SHORT);
        Log.d("MyService","服务销毁");
    }

}
```

2. 在MainActivity的Button按钮回调里面`开启服务`和`关闭服务`。
```Java
// 开启服务 在后台一直运行
Button btn1 = findViewById(R.id.button1);
btn1.setOnClickListener(new View.OnClickListener() {
   @Override
   public void onClick(View view) {
       // 开启后台服务状态 无客户端交互 模式
       Intent intent = new Intent(MainActivity.this,MyService.class);
       startService(intent);
   }
});

// 停止服务 停止在后台一直运行的服务
Button btn2 = findViewById(R.id.button2);
btn2.setOnClickListener(new View.OnClickListener() {
   @Override
   public void onClick(View view) {
       // 停止后台服务状态 无客户端交互 模式
       Intent intent = new Intent(MainActivity.this,MyService.class);
       // 关闭Service
       stopService(intent);
   }
});
```

#### 2.后台绑定模式 可以与App进行交互

> 此种模式，服务绑定后，服务可以给App回调，App可以通知服务做一些逻辑。 

1. 在之前的MyService里面再添加一些方法，例如我们假设要做一个后台下载的抽象类的功能。

```Java

// 备注: 在Service里面调用 stopSelf() 方法就可以让服务停下来
public class MyService extends Service {

    private DownloadBinder mBinder = new DownloadBinder();

    class DownloadBinder extends Binder {
        public void startDownload() {
            Log.d("MyService","startDownload");
        }
        public int getProgress() {
            Log.d("MyService","getProgress");
            return 0;
        }
    }

    public MyService() {}

    // 服务创建时候调用
    @Override
    public void onCreate() {
        super.onCreate();
        Toast.makeText(this, "服务创建", Toast.LENGTH_SHORT).show();
        Log.d("MyService","服务创建");
    }

    // 每次服务启动的时候调用
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        Toast.makeText(this,"服务开启",Toast.LENGTH_SHORT);
        Log.d("MyService","服务开启");
        return super.onStartCommand(intent, flags, startId);
    }

    // 服务销毁的时候调用
    @Override
    public void onDestroy() {
        super.onDestroy();
        Toast.makeText(this,"服务销毁",Toast.LENGTH_SHORT);
        Log.d("MyService","服务销毁");
    }


    @Override
    public IBinder onBind(Intent intent) {
        Toast.makeText(this,"服务绑定",Toast.LENGTH_SHORT);
        Log.d("MyService","服务绑定");
        return mBinder;
    }

}

```

2. 在MainActivity的Button按钮回调里面`绑定服务`和`解绑服务`。

```Java
// 绑定服务 + 解绑服务
Button btn3 = findViewById(R.id.button3);
btn3.setOnClickListener(new View.OnClickListener() {
   @Override
   public void onClick(View view) {
       // 开启后台服务绑定模式 可以与客户端交互
       Intent intent = new Intent(MainActivity.this,MyService.class);
       // 绑定Service
       bindService(intent,connection,BIND_AUTO_CREATE);
       // 解绑Service
       unbindService(connection);
   }
});
```

## 类型二: IntentService

> IntentService是Service的子类，对Service做了一些方便的封装。 是更加方便的Service的用法，开启这个Service，Service回调在一个子线程，并且执行完这个子现成的任务，就会自动销毁。 非常适合一些需要在子线程处理的任务，处理完自己自己关闭的场景。

1. 创建一个MyIntentService继承IntentService
```Java
// IntentService 特性: 执行完任务 自动销毁
public class MyIntentService extends IntentService {

    public MyIntentService() {
        super("MyIntentService"); // 调用父类的有参构造函数
    }

    @Override
    protected void onHandleIntent(@Nullable Intent intent) {
        Log.d("MyIntentService","Thread id is " + Thread.currentThread());
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        Log.d("MyIntentService","onDestroy executed");
    }
}
```

2. Manifest文件里面添加Service标签
```XML
<service android:name=".MyIntentService"></service>
```

1. 在MainActivity的Button按钮回调里面`开启服务`
```Java
// 自动开启子线程的 IntentService 服务
Button btn4 = findViewById(R.id.button4);
btn4.setOnClickListener(new View.OnClickListener() {
   @Override
   public void onClick(View view) {
       Log.d("MainActivity","自动开启子线程的 IntentService 服务");
       // 开启后台服务绑定模式 可以与客户端交互
       Intent intent = new Intent(MainActivity.this,MyIntentService.class);
       startService(intent);
   }
});
```


