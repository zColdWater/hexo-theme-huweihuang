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
如上图, 标准的 As 项目中, 包含三大部分:

* Top-level Gradle:用于配置所有的 Module 属性
* Moudle-level Gradle: 配置独立 Moudle 的属性
* Gradle Wrapper: 用于统一编译环境, 一般供 CI 使用

### Top-level Build Script

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

简单的说, 就是为了不同的渠道或者版本自动生成相应的 apk. 其算法是 `buildType` * `productFlavor` * `density` * `abi` 做一个笛卡尔积.


