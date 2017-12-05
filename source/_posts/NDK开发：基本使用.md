---
title: NDK开发：基本使用
date: 2017-12-04 14:16:48
categories:
- NDK
tags:
- NDK
- JNI

---
## 前言
* NDK  
  Native Development Kit（NDK）是一系列工具的集合。它提供了一系列的工具，帮助开发者快速开发C/C++的动态库，并能自动将so和java一起打包成apk。
* JNI  
  Java Native Interface（JNI）标准是java平台的一部分，JNI是Java语言提供的Java和C/C++相互沟通的机制，Java可以通过JNI调用C/C++代码，C/C++的代码也可以调用java代码。
* JNI与NDK的关系  
  NDK可以为我们生成了C/C++的动态链接库，JNI是java和C/C++沟通的接口，两者与android没有半毛钱关系，只因为安卓是java程序语言开发，然后通过JNI又能与C/C++沟通，所以我们可以使用NDK+JNI来实现“Java+C”的开发方式。
* 为什么要NDK开发  
  NDK开发具有以下优点:  
  1. 项目需要调用底层的一些C/C++的一些东西（java无法直接访问到操作系统底层（如系统硬件等）），或者已经在C/C++环境下实现了功能代码（大部分现存的开源库都是用C/C++代码编写的。），直接使用即可。NDK开发常用于驱动开发、无线热点共享、数学运算、实时渲染的游戏、音视频处理、文件压缩、人脸识别、图片处理等。  
  2. 为了效率更加高效些。将要求高性能的应用逻辑使用C/C++开发，从而提高应用程序的执行效率。但是C/C++代码虽然是高效的，在java与C/C++相互调用时却增大了开销。  
  3. 基于安全性的考虑。防止代码被反编译，为了安全起见，使用C/C++语言来编写重要的部分以增大系统的安全性，最后生成so库（用过第三方库的应该都不陌生）便于给人提供方便。（任何有效的代码混淆对于会smail语法反编译你apk是分分钟的事，即使你加壳也不能幸免高手的攻击）  
  4. 便于移植。用C/C++写得库可以方便在其他的嵌入式平台上再次使用。

## 安装与配置
首先我们在Android Studio下新建一个安卓项目。然后打开Project Structure界面，如下：
![](http://ozv0hqacp.bkt.clouddn.com/17-12-5/91411828.jpg)  
在SDK Location目录下，有SDK和NDK的路径，如果我们暂时还未下载配置过NDK，故我们需要点击Download Android NDK来进行下载（Android Studio还是很强大的，相比Eclipse能省不少事）。这里Android Studio会下载最新版本的NDK进行安装，默认会下载保存在SDK的路径下。我们在上图中还能看到有一段介绍文字，说SDK以及NDK的路径配置会保存在local.properties文件内，安装完成后我们刷新Project，进local.properties文件查看也能看到SDK与NDK的路径。  
```
ndk.dir=F\:\\AndroidSDK\\ndk-bundle
sdk.dir=F\:\\AndroidSDK
```
NDK下载配置完成之后，需要在gradle.properties文件中加上一行：
```
android.useDeprecatedNdk=true
```
接下来，我们借助强大的Android Studio的插件功能，在External Tools下配置两个非常有用的插件。进入Settings–>Tools–>ExternalTools，点击+号增加。
![](http://ozv0hqacp.bkt.clouddn.com/17-12-5/54641453.jpg)

![](http://ozv0hqacp.bkt.clouddn.com/17-12-5/68029794.jpg)
javah -jni命令，是根据java文件生成.h头文件的，会自动根据java文件中的类名（包含包名）与方法名生成对应的C/C++里面的方法名。下面是参数配置及其含义：
1. Program: `$JDKPath$\bin\javah.exe` 这里配置的是JDK目录下的javah.exe的路径。
2. Parametes: `-classpath . -jni -d $ModuleFileDir$/src/main/jni $FileClass$` 这里$FileClass$指的是要执行操作的类名（即我们操作的文件），$ModuleFileDir$/src/main/jni表示生成的文件保存在这个module目录的src/main/jni目录下。
3. Working: `$ModuleFileDir$\src\main\java` module目录下的src\main\java目录（不是很理解）。
使用方式：选中java文件—>右键—>External Tools—>javah-jni，将生成jni文件夹以及文件夹下的 包名.类名的.h头文件 （名字过长，我们可以自己重命名）。
![](http://ozv0hqacp.bkt.clouddn.com/17-12-5/26940002.jpg)
ndk -build命令，是根据C/C++文件生成so文件的。下面是参数配置及其含义：
1. Program: `F:\apk\sdk\ndk-bundle\ndk-build.cmd` 这里配置的是ndk下的ndk-build.cmd的路径（根据实际情况填写）。
2. Working: `$ModuleFileDir$\src\main\`
使用方式：选中C/C++文件—>右键—>ExternalTools—>ndk-build，将在main文件夹下生成libs文件夹以及多个so文件，我们可以移动至jniLibs目录下去。

## 简单实例
接下来我们创建一个访问本地C/C++方法的java类。
```java
public class JniTest {
    /**
     * 将用C++代码实现，在android代码中调用的方法：获取当前app的包名
     * @param o
     * @return
     */
    public static native String getPackname(Object o);

    /**
     * 加载so库或jni库，在使用到该库之前加载就行，不一定非要写在这个类内
     * 系统自己会判断扩展名是dll还是so,这里加载libJNI_ANDROID_TEST.so
     */
    static {
        System.loadLibrary("JNI_ANDROID_TEST");
    }
}
```
注意JNI_ANDROID_TEST这个Library名字，之后还会需要用到，要保持一致。该类提供了一个static的native方法，该方法将用来获取app的包名。然后对该文件执行javah -jni操作，生成对应的.h头文件。 
![](http://ozv0hqacp.bkt.clouddn.com/17-12-5/74565858.jpg)
如图，已经根据我们的java类生成了对应的.h文件，文件名为包名_类名.h，我们可以手动改名为jnitest.h，里面只有一个方法，返回值为String（jstring），方法名为Java_类的包名_类名_方法名（包名中的分级不是用.而是_），前面两个参数是C++里面必须有的（JNIEnv代表指向JVM的指针，jclass是调用该方法的java对象），第三个就是我们java类的方法里面的参数Object。注意，这是java函数与C++函数对应的静态注册方法，即通过特定的规则来写，此处方法名可以随意起名字，然后还可以用动态注册的方式关联两个方法（显然，静态注册要简单一些）。
然后我们新建一个C++文件，取名为jnitest.cpp，写上需要include的文件，从.h文件中复制方法过来（方法名、参数类型、返回值等必须一致！血与泪的教训）。 
![](http://ozv0hqacp.bkt.clouddn.com/17-12-5/7153380.jpg)
至此，.h文件和c++文件均已完成，接下来还需要在这个jni目录下增加两个文件，Android.mk和Application.mk。
Android.mk，注意LOCAL_MODULE的值与之前的名字相对应，LOCAL_SRC_FILES的值写c++文件的名字，这两个值成对设置，可设置多组。（:=是赋值的意思，$是引用某变量的值。）
```
# 设置当前的编译目录（Android.mk所在的目录）
LOCAL_PATH := $(call my-dir)

# 清除LOCAL_XX变量（LOCAL_PATH除外）
include $(CLEAR_VARS)

# 指定当前编译模块的名称
LOCAL_MODULE := JNI_ANDROID_TEST

# 编译模块需要的源文件
LOCAL_SRC_FILES := jnitest.cpp

# 指定编译出的库类型，BUILD_SHARED_LIBRARY：动态库；BUILD_STATIC_LIBRARY：静态库， BUILD_EXECUTEABLE指：可执行文件
include $(BUILD_SHARED_LIBRARY)
```
![](http://ozv0hqacp.bkt.clouddn.com/17-12-5/56196755.jpg)
在一个Android.mk文件中配置多个Module的方式如下（include$(CLEAR_VARS)、include $(BUILD_SHARED_LIBRARY)两个语句也需要加上）：
```
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)
LOCAL_MODULE := JNI_STATIC_ANDROID_TEST
LOCAL_SRC_FILES := jnistaticutils.cpp
include $(BUILD_SHARED_LIBRARY)

include $(CLEAR_VARS)
LOCAL_MODULE := JNI_DYNAMIC_ANDROID_TEST
LOCAL_SRC_FILES := jnidynamicutils.cpp
include $(BUILD_SHARED_LIBRARY)
```
Application.mk，APP_ABI有四种类型（默认armeabi），armeabi、armeabi-v7a、x86、mips，设置时以空格隔开，all表示所有。该文件中有个可选配置的APP_MODULES，类似于上面Android.mk文件中的LOCAL_MODULE，以空格隔开，且会覆盖掉Android.mk文件中的LOCAL_MODULE设置（比如Android.mk文件中的写了两个jni库的配置，LOCAL_MODULE := JNI1、LOCAL_MODULE := JNI2，而Application.mk中设置的APP_MODULES := JNI1，则只能生成JNI1的so文件，要生成JNI2的so文件的时候会报错，除非写成APP_MODULES := JNI1 JNI2，这里我们直接省略默认使用Android.mk中的）。
`APP_ABI := all`
接下来我们需要对C++文件执行ndk-build操作，生成相应的so文件。 
![](http://ozv0hqacp.bkt.clouddn.com/17-12-5/81517961.jpg)  
如图，在main/libs目录下生成了多个so文件，名字为lib+我们指定的库名（同时还生成了obj文件夹，不知是什么东西）。
这时候我们可以在main目录下新建jniLibs文件夹，把生成的libs文件夹内的东西均复制过去，删除新生成的jni、libs、obj三个文件夹。然后在Activity中测试调用，在TextView上显示我们通过C++代码实现的方法getPackname获取app的包名了。
```java
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        TextView tv = (TextView) findViewById(R.id.tv_app_package_name);
        tv.setText("packageName: " + JniTest.getPackname(MainActivity.this));
    }
}
```
![](http://ozv0hqacp.bkt.clouddn.com/17-12-5/18965197.jpg)
测试能正确得到包名，说明调用成功了。我们可以把JniTest类以及so文件给别人去使用，这样别人是看不到我们的代码实现的，能很好的保护我们的源码。

如果你也遇到这样的问题
![](http://ozv0hqacp.bkt.clouddn.com/17-12-5/25517309.jpg)
来我们仔细看下Log，大概意思就是说：  
1. android.useDeprecatedNdk不再支持了  
2. 让使用CMake or ndk-build  
3. 然后还有链接  

考虑使用CMake或ndk构建集成。要了解更多信息，请访问:  
https://d.android.com/r/studio-ui/add-native-code.html#ndkCompile    
首先，您可以使用Android的ndk构建脚本示例插件为您生成:  
/Users/apple/Desktop/AndroidJNITest/app/build/intermediates/ndk/debug/Android.mk  
或者，你可以使用实验插件:  
https://developer.android.com/r/tools/experimental-plugin.html  
继续使用已弃用的NDK编译60天，设置  
在gradle.properties  
android.deprecatedNdkCompileLease = 1512283120054(这个测试不起作用) 
 
经过各种查资料，发现原来在gradle3.0以上以前这种方法不在支持  
学习过程就不详细描述了，直接上结果：  
先通过SDKManager下载：CMake和LLDB  
![](http://ozv0hqacp.bkt.clouddn.com/17-12-5/47997322.jpg)
在build.gradle的defaultConfig节点下加入：
```
	// 使用Cmake工具
    externalNativeBuild {
      cmake {
        cppFlags ""
        //生成多个版本的so文件
        abiFilters 'arm64-v8a','armeabi-v7a','x86','x86_64'
      }
    }
```
在build.gradle的android节点下加入：
```
  // 配置CMakeLists.txt路径
  externalNativeBuild {
    cmake {
      path "CMakeLists.txt"  // 设置所要编写的c源码位置，以及编译后so文件的名字
    }
  }
```
![](http://ozv0hqacp.bkt.clouddn.com/17-12-5/43514697.jpg)
添加CMakeLists.txt文件到build.gradle文件同级目录下，具体内容如下：
```
# For more information about using CMake with Android Studio, read the
# documentation: https://d.android.com/studio/projects/add-native-code.html

# Sets the minimum version of CMake required to build the native library.
#CMakeLists.txt
cmake_minimum_required(VERSION 3.4.1)

# Creates and names a library, sets it as either STATIC
# or SHARED, and provides the relative paths to its source code.
# You can define multiple libraries, and CMake builds them for you.
# Gradle automatically packages shared libraries with your APK.

add_library( # Sets the name of the library.
      # 设置so文件名称.
       JNI_ANDROID_TEST

       # Sets the library as a shared library.
       SHARED
       # 设置这个so文件为共享.

       # Provides a relative path to your source file(s).
       # 设置这个so文件为共享.
       src/main/jni/jnitest.cpp)

# Searches for a specified prebuilt library and stores the path as a
# variable. Because CMake includes system libraries in the search path by
# default, you only need to specify the name of the public NDK library
# you want to add. CMake verifies that the library exists before
# completing its build.

find_library( # Sets the name of the path variable.
       log-lib

       # Specifies the name of the NDK library that
       # you want CMake to locate.
       log )

# Specifies libraries CMake should link to your target library. You
# can link multiple libraries, such as libraries you define in this
# build script, prebuilt third-party libraries, or system libraries.

target_link_libraries( # Specifies the target library.
            # 制定目标库.
            JNI_ANDROID_TEST

            # Links the target library to the log library
            # included in the NDK.
            ${log-lib} )
```
最后修改CMakeLists.txt中以下圈出部分就能解决了：
![](http://ozv0hqacp.bkt.clouddn.com/17-12-5/47020339.jpg)

参考文章：  
[详解AndroidStudio JNI +Gradle3.0以上JNI爬坑之旅](http://www.jb51.net/article/129667.htm "详解AndroidStudio JNI +Gradle3.0以上JNI爬坑之旅")  
[NDK开发 从入门到放弃(一：基本流程入门了解)](http://blog.csdn.net/xiaoyu_93/article/details/52870395 "NDK开发 从入门到放弃(一：基本流程入门了解)")