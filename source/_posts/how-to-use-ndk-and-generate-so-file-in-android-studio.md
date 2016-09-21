title: Android NDK 介绍与使用示例
date: 2016-09-21 14:02:27
tags:
- android
---
## 简单介绍

### NDK 是干啥的？
全称 `Native Development Kit`, 允许你在 Android 应用中使用 `C` 或 `C++` 代码

### NDK 的好处
- NDK 可以生成 `.so` 文件, 方便代码共享 
- 增加反编译的难度
- 提高性能

##  使用示例
### 版本说明
- Android Studio: 2.2
- Android Plugin for Gradle: com.android.tools.build:gradle:2.2.0
- Gradle: 2.14.1


### 准备工作
在 Android Studio 里下载好 NDK
{% asset_img "ndk-installation-figure.png" "ndk installation figure" %}

Android Studio 2.2 稳定版已发布，这个版本增强了 C++ 的开发能力，能够使用 ndk-build 或 CMake 去编译和调试项目里的 C++ 代码

ndk-build 或 CMake 比用 Gradle 去编译 C++ 更好

因为 NDK 套件里自带 ndk-build, 不需要再额外安装 CMake, 为了简单起见以下示例都用 ndk-build 去编译 C++

#### 注意
- 为了能够在 Android Studio 里使用 CMake 或 ndk-build，你必须使用 Android Studio 2.2 或更高版本 和 Android Plugin for Gradle version 2.2.0 或更高版本
- 由于 Android Studio 编译 C 和 C++ 代码默认使用  CMake, 因此我建议使用 [Experimental Android Plugin for Gradle](http://tools.android.com/tech-docs/new-build-system/gradle-experimental) 的用户也切换到 Android Plugin for Gradle version 2.2.0 或更高版本，这样既可以使用 Android Plugin for Gradle 的稳定版本，也可以使用 CMake 或 ndk-build 来提高编译速度

### Android.mk 和 Application.mk
简单来说
- Android.mk 用来描述需要生成哪些模块的 `.so` 文件
- Application.mk 用来描述如何生成 `.so` 文件，生成静态库还是动态库

这里给出示例文件
#### [Android.mk](https://developer.android.com/ndk/guides/android_mk.html)
```plain
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)

LOCAL_MODULE := gaussianBlur
LOCAL_SRC_FILES := blur.cpp
LOCAL_LDLIBS := -llog

include $(BUILD_SHARED_LIBRARY)
```
- 宏函数 my-dir 是由编译系统提供的，会返回当前目录的路径（当前目录指的是包含 Android.mk 的目录）

- CLEAR_VARS 这个变量也是由编译系统提供的，会清除很多 LOCAL_XXX 变量

以上两行命令基本上是固定的，不需要去动

- LOCAL_MODULE 指定模块名称，会自动生成相应的 libgaussianBlur.so 文件

- LOCAL_SRC_FILES 指定这个模块要编译的 C++ 文件

- LOCAL_LDLIBS 指定这个模块里会用到哪些原生 API, 详见 [Android NDK Native APIs](https://developer.android.com/ndk/guides/stable_apis.html)

- BUILD_SHARED_LIBRARY 根据你之前定义的 LOCAL_XXX 变量，决定要编译啥，如何去编译，这行命令一般也不需要动，固定的

#### [Application.mk](https://developer.android.com/ndk/guides/application_mk.html)
```plain
APP_STL := gnustl_static
```

APP_STL 指定使用哪些 C++ 运行时, 详见 [C++ Library Support](https://developer.android.com/ndk/guides/cpp-support.html)

Android.mk 和 Application.mk 都放在 jni 目录下
项目文件结构如下
```plain
|____app
| |____src
| | |____main
| | | |____jni
| | | | |____Android.mk
| | | | |____Application.mk
| | | | |____blur.cpp
```

### 如何使用 C++ 代码?
前面已经给出了 Android.mk 和 Application.mk 的示例，下面在 build.gradle 里配置 externalNativeBuild 就可以自动编译 C++ 代码了

示例内容如下
```gradle
defaultConfig {
	applicationId "com.example.app"
	minSdkVersion 16
	targetSdkVersion 24
	versionCode 102
	versionName "0.2"

	externalNativeBuild {
		ndkBuild {
			arguments "NDK_APPLICATION_MK:=src/main/jni/Application.mk"
			cFlags "-DTEST_C_FLAG1", "-DTEST_C_FLAG2"
			cppFlags "-DTEST_CPP_FLAG2", "-DTEST_CPP_FLAG2"
			abiFilters "armeabi-v7a", "armeabi"
		}
	}
}
externalNativeBuild {
	ndkBuild {
		path "src/main/jni/Android.mk"
	}
}
```
- path 用来指定 Android.mk 的路径
- arguments 用来指定 Application.mk 的路径
- abiFilters 用来指定生成哪些平台的 .so 文件
- cFlags 和 cppFlags 是用来设置环境变量的, 一般不需要动，和示例一样就好，

好了，现在运行项目，就可以将 `blur.cpp` 自动编译为 `libgaussianBlur.so` 文件了

### 手动生成 `.so` 文件
如果能直接引用生成好的 `.so` 文件，可以避免重复编译 `.so` 文件，从而加快应用 build 速度

下面是手动生成 `.so` 文件的步骤
```bash
# 进入 main 目录
cd app/src/main

# 生成 .so 文件
/Users/lee/Library/Android/sdk/ndk-bundle/ndk-build NDK_PROJECT_PATH=. NDK_APPLICATION_MK=./jni/Application.mk NDK_LIBS_OUT=./jniLibs
```
执行这个命令后，会在 `app/src/main/jniLibs` 目录生成各个平台的 `.so` 文件
如果需要把 `.so` 文件共享给其他人，把这些平台下的 `.so` 文件发给其他人就好了

/Users/lee/Library/Android/sdk/ndk-bundle/ndk-build 是我下载好的 NDK 目录路径

- NDK_PROJECT_PATH 指定项目路径, 会自动读取这个目录下的 jni/Android.mk 文件
- NDK_APPLICATION_MK 指定 Application.mk 的位置
- NDK_LIBS_OUT 指定将生成的 `.so` 文件放到哪个目录，默认 Android Studio 会读取 jniLibs 目录下的 `.so` 文件, 所以我们把 `.so` 文件生成到这

测试结果: （测试均在 clean 项目后进行）
引用 `.so` 文件前平均耗时 `1m 27s`
引用 `.so` 文件后平均耗时 `47s`
我们可以看到 build 速度快了将近一倍

### 调试 NDK
让 `NDK_LOG` 变量为1，就可以打印日志信息
```bash
ndk-build -e NDK_LOG=1
```

## 需要注意的地方
### Android Studio 如何知道项目里用了 C 或 C++ 代码并自动去编译它 ？
Android Studio 会检查 `app/src/main/jni` 目录下是否有 C 或 C++ 代码, 如果有, 就根据 `build.gradle` 里的配置调用 CMake 或 ndk-build 去编译它

### externalNativeBuild 这个我怎么知道如何配 ?
可以参考 [Building C++ in Android Studio with CMake or ndk-build](https://sites.google.com/a/android.com/tools/tech-docs/external-c-builds)

这里有个问题，官方示例里的 targets 参数我不知道怎么用，如果设置为和 Android.mk 里    LOCAL_MODULE 一样的会报错误，不加就能运行

这里有个 bug 报告, [Inconsitency between LOCAL_MODULE and gradle target name in ndk-build integration](https://code.google.com/p/android/issues/detail?id=215826)

### 生成的 .so 文件需要优化么？
参考这个 SO  问题 [Android NDK release build](http://stackoverflow.com/a/14579929/2227031)

简单来说就是，默认生成的 .so 文件就是已经优化过的，不需要额外做操作了

## 参考资料
- [Bugtags V1.2.7 引入 NDK SO 库](http://blog.bugtags.com/2016/06/08/android-ndk-so/)
- [What you should know about .so files](http://ph0b.com/android-abis-and-so-files/)
- [使用AndroidStudio进行NDK开发（一）](https://dailyios.com/article/Using_Android_Studio_for_NDK_development.html)
- [ABI Management](https://developer.android.com/ndk/guides/abis.html#sa)
- [Android NDK编译本地文件以及引用第三方so文件](http://www.jianshu.com/p/ce003c4fe1cf)
- [Android Studio 2.2](http://android-developers.blogspot.jp/2016/09/android-studio-2-2.html)
- [Add C and C++ Code to Your Project](https://developer.android.com/studio/projects/add-native-code.html)
- [NDK can't find the application directory](http://stackoverflow.com/questions/14156596/ndk-cant-find-the-application-directory)
