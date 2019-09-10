---
title: "[Android] Gradle"
catalog: true
toc_nav_num: true
date: 2019-09-06 13:00:30
subtitle: "了解Gradle基本字段意思"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/knowledge-min.png"
tags:
- Android
catagories:
- Android
---

# Gradle文件前言
> 查看官方文档请移步到 ---> [官方文档](https://docs.gradle.org/current/dsl/)

让我用一句话来描述，就是项目的配置文件, 本文以Android项目为例。


# 了解Gradle

## 1. As生成有关的Gradle的几个文件
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/gradle1.png" height="350" />
如上图, 标准的 As 项目中, 包含三大部分:

* Top-level Gradle:用于配置所有的 Module 属性
* Moudle-level Gradle: 配置独立 Moudle 的属性
* Gradle Wrapper: 用于统一编译环境, 一般供 CI 使用

### Top-level Build Script
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/gradle2.png" height="350" />
有几个概念, 都来自于 [Gradle Reference](https://docs.gradle.org/current/dsl/):

gradle script 都是 configuration scripts. 这话的意思是说, 运行起来后, 每个脚本文件最终都会对应到一个程序中的对象, 这个对象叫做 delegate object. 比如, build.gradle 对应为程序中的 Project. 整个 Gradle 有三种类型的代理对象


下面, 我们一步一步来看看这个 Top-level Build Script:

#### buildscript
```gradle
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.0'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

/**
buildscript { }

Configures the build script classpath for this project.

The given closure is executed against this project’s ScriptHandler. The ScriptHandler is passed to the closure as the closure’s delegate.

Delegates to:
ScriptHandler from buildscript
*/

```

简单来说, buildscript 就是配置构建脚本所需要用到的 class 的 path. 其内部会把 configuration closure 传递给 ScriptHandler (你不用关心这是什么) 然后实现设置 repositories 和 dependencies.

一般而言, 这个 script block 都写在开头, 声明这个脚本本身所需要的依赖.



#### allprojects
```gradle
allprojects {
    repositories {
        jcenter()
    }
}

/*
allprojects { }

Configures this project and each of its sub-projects.

This method executes the given closure against this project and its sub-projects. The target Project is passed to the closure as the closure’s delegate.

Delegates to:
Each Project in allprojects
**/

```

这个 configuration closure 里的内容会被包括当前的 project 以及其所有 sub-project 执行. 你在这里配置的 repositories 会在上述地方都生效.

这里的写法意味着, 所有的 project 都会在 jcenter() 中寻找依赖. 除此之外, 还可以指定 `flatDir`, `maven`, `ivy` 等. 可以参考 `RepositoryHandler`.



#### task clean

```gradle
task clean(type: Delete) {
    delete rootProject.buildDir
}
```
这是定义了一个新的 task, 叫做 clean. 其类型是 Delete. 实际上, Android Plugin 内置了 clean 方法, 该方法位于 module 中. Module 中内置的 clean 方法只会清理 Module 中的文件并删除 Module 中的 build 目录, 但是工程根目录下的 build 文件是没人清理的, 所以这里定义的 clean 方法即删除项目目录下的 build 文件夹.


## Module-level Build Script
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/gradle3.png" height="350" />
模块级 script 用于描述该模块的编译过程. 一般而言, Android 能用到的一共有三种:

* Application: 对应 com.android.application. 编译的结果是一个 apk
* Android Library: 对应 com.android.library. 编译结果为 aar
* JavaLibrary: 对应的 java. 编译结果为 jar


你是不是好奇 android 一共有多少种插件? [可以看看 google android plugin 的仓库](https://android.googlesource.com/platform/tools/build/+/master/gradle/src/main/groovy/com/android/build/gradle)

java 这个插件一般用不到, 就不提了. com.android.library 和 com.android.application 内容配置差不太多, 这里以 com.android.application 为例讲解.

由于 Module-level Build Script 大部分都是相应插件自行实现的内容, 所以我们就不能再 gradle 文档中找了, 我们要到 google 系的 refs 中搜索 (见 refs).

你是不是好奇这个文件到底有多少个属性? [可以看看这个文件的源码](https://android.googlesource.com/platform/tools/build/+/master/gradle/src/main/groovy/com/android/build/gradle/AppPlugin.groovy)


#### android

```gradle
android {
    compileSdkVersion 24
    ....
}
```

其实 application 插件支持的属性远远比这个要多, 咱们来看一个全集:

```gradle
apply plugin: 'com.android.application'

android {
    /**
     * 设置编译 sdk 和编译工具的版本
     */
    compileSdkVersion 24
    buildToolsVersion "24.0.3"

    /**
     * 关于签名, 请参考 google 官方文档: Sign Your App
     */
    signingConfigs {
        /**
         * As 会自动帮我们使用 debug certificate 进行签名. 这个 debug certificate 每次安装 As 都会变,
         * 因此不适合作为发布之用.
         */
        debug {
        }

        /**
         * 由于 Module-level Build Script(本文件) 也要放在 VCS 中管理, 所以不将密码等信息写在这里.
         * 一般的做法是: 在本机设置环境变量, 然后通过下面代码中演示的这种方式读取.
         * 当然, 最佳实践也指导我们将 `gradle.properties` 排除在 VCS 之外,
         * 此时, 也在该文件中将密码设置为变量, 然后在此读取使用.
         */
        release {
            storeFile file("$System.env.STORE_FILE")
            storePassword "$System.env.STORE_PASSWORD"
            keyAlias "$System.env.KEY_ALIAS"
            keyPassword "$System.env.KEY_PASSWORD"
        }
    }

    /**
     * 为所有的 build variants 设置默认的值. 关于 build variant, 我们后面会用一张图片说明
     */
    defaultConfig {
        applicationId "com.walfud.myapplication"
        minSdkVersion 23
        targetSdkVersion 24
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }

    /**
     * type 默认会有 debug 和 release. 不管你写不写都如此.
     * 通常, 我们在 debug 中保留默认值, release 中开启混淆, 并使用私有的签名
     */
    buildTypes {
        debug {
            // 使用默认值
        }

        release {
            // 混淆
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'

            // 签名
            signingConfig signingConfigs.release
        }
    }

    /**
     * flavor 强调的是不同的版本, 比如付费版和免费版.
     * 在国内, 这个字段更多被用于区分不同的渠道, 即 360 渠道, 小米渠道等等.
     */
    productFlavors {
        m360 {}
        xiaomi {}
    }

    /**
     * 这个选项基本不用.
     * 官方说: 使用 splits 可以比使用 flavor 更加有效创建多 apk.
     * 目前而言, 仅支持 Density 和 ABIs 这两个分类.
     */
    splits {
        // 按屏幕尺寸
        density {
            enable true

            // 默认包含全部分辨率, 这里是剔除一些我们不要的
            exclude "ldpi", "mdpi", "xxxhdpi", "400dpi", "560dpi", "tvdpi"
        }

        // 按架构
        abi {
            enable true

            // 使用 `reset()` 后, 我们就相当于不包含任何架构,
            // 这种情况下我们就可以通过 `include` 指定想要使用的架构
            reset()

            include 'x86', 'armeabi-v7a'
            universalApk true       // 是否同时生成一个包含全部 Architecture 的包
        }
    }
}

/**
 * 这个项目的依赖
 */
dependencies {
    /**
     * `fileTree` 导入 libs 目录下的所有 jar 文件
     */
    compile fileTree(dir: 'libs', include: ['*.jar'])

    /**
     * 想导入本地 aar, 首先需要指明本地 aar 的位置, 如下 `repositories` 中所示, 我们把 aar 放在了
     * Module-level 的 libs 目录下. 然后引用这个文件即可.
     */
    compile(name: 'components', ext: 'aar')
}

/**
 * 配置了去哪里查找这个模块依赖文件
 */
repositories {
    flatDir {
        dirs 'libs'
    }
}
```

#### Build Variant
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/gradle4.png" height="350" />
简单的说, 就是为了不同的渠道或者版本自动生成相应的 apk. 其算法是 `buildType` * `productFlavor` * `density` * `abi` 做一个笛卡尔积.

## 2. As里的Gradle


### 1.自动下载

每一次重新导入工程时, 因为默认会使用 `Use default gradle wrapper (recommended)` 设置, 所以 As 会下载 `gradle-wrapper.properties` 中 `distributionUrl` 所指定的 gradle 版本. 这个过程在国内普遍巨慢无比. 我的建议是找到上述文件, 动手修改 distributionUrl 为已下载过的 gradle 版本(比如你之前已经下载过 gradle-2.14.1 了), 这样就能直接打开了.

实际上, 这个配置项位于 As 自动生成的配置文件(`.idea/gradle.xml`), 我们来看看:

对于新建 (‘File -> New -> New Project’)的项目,  As 是这样给我们生成的:

它在设置中的表现是这样的:
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/gradle5.png" height="350" />
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/gradle6.png" height="350" />
可见, As 帮我限定了 gradle 版本. 当然, 如果这个指定的 gradle 版本不存在的话, 一样会去下载.

再来看看被 ‘File -> Open’ (注意, 不是 Reopen) 打开的项目:
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/gradle7.png" height="350" />
<img src="https://raw.githubusercontent.com/zColdWater/Resources/master/Images/gradle8.png" height="350" />

也就是说, 每次你 Open 一个项目的时候, As 会忽略你之前设置的 gradle 版本, 而使用 wrapper 文件中所指定的版本. 所以你 clone 下来的代码第一次打开都会卡很久的原因, 就是因为他们在下载 wrapper 中的 gradle 啊…


### 2.gradle-wrapper.properties

AS 创建的工程, 在根目录下有 `gradlew.bat` 和 `gradlew.sh` 文件. 这两个文件会读取 `gradle/wrapper/gradle-wrapper.properties` 并使用其中指定的 gradle 版本进行编译. 一般而言这套机制用于 CI 服务器中来保障每次编译都在同样的环境下.

```gradle
#Wed Nov 11 21:08:45 CST 2015
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-2.10-all.zip
```


`GRADLE_USER_HOME` 默认值是你的 `USER_HOME/.gradle` (win 下是 `c:\Users\\.gradle`, linux 下是 `~/.gradle` ).

对于 `gradlew` 而言, 如果上述路径没有找到可执行的 gradle 文件, 则会使用 `distributionUrl` 中所指定的 url 下载后在执行.


### 3.gradle.properties

主要作用配置一些与当前机器 gradle 编译相关的属性. 这些属性是每个编译机器根据自己情况决定的(比如, 分配多大内存), 因此不适合放在 git 中.


#### 3.1 gradle.properties
类似于系统的环境变量. 作为 jvm 启动参数.

`org.gradle.daemon`  
是否开启 daemon. 一般来说, 本机编译建议打开, CI 上建议关闭.

`org.gradle.java.home`  
指定 gradle 进程的 java home. 没什么卵用.

`org.gradle.jvmargs`  
daemon 进程的 jvm 参数. 当你编译报错 OOM 的时候, 可以调整这个参数 (见后面的例子)

`org.gradle.configureondemand`  
自行 google. 没卵用

`org.gradle.parallel`  
project 之间并行编译

#### 3.2.设置代理

```gradle
systemProp.https.proxyHost=www.proxyhost.org
systemProp.https.proxyPort=8080
systemProp.https.proxyUser=userid
systemProp.https.proxyPassword=password
systemProp.https.nonProxyHosts=*.nonproxyrepos.com|localhost
```


# 常见的 ‘这是什么玩意儿’
transitive = true

```gradle
compile('com.crashlytics.sdk.android:answers:1.3.10@aar') {
    transitive = true;
}
```
简单地说, 由于该语句使用 `@aar` notation, 所以 gradle 只会下载这一个 aar 文件, 而不会顺带着下载这个 aar 所需要的依赖文件. 所以需要 `transitive` 让依赖能够自动被下载. 一般而言, 去掉 `@aar` 以及 `{ transitive = true }` 不会有任何问题.

