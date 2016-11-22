---
layout: post
title: "jni编译问题：jin中添加LOG方法以及undefined reference to `__android_log_print'错误"
description: ""
category: ANDROID
tags: [JNI]
---

*版权声明：本文为博主原创文章，未经博主允许不得转载。*

注意Android.mk 里有一行include $(CLEAR_VARS)
必须把LOCAL_LDLIBS :=-llog放在它后面才有用，否则相当于没写
> 添加方法：

Android.mk里:

    LOCAL_PATH := $(call my-dir) //它用于在开发tree中查找源文件
    include $(CLEAR_VARS) //负责清理很多LOCAL_xxx
    LOCAL_MODULE := hello  //表示Android.mk中的每一个模块
    LOCAL_SRC_FILES := hello.c //包含将要打包如模块的C/C++ 源码
    LOCAL_LDLIBS += -llog  //附加log日志库，必须放在include $(CLEAR_VARS)后面
    include $(BUILD_SHARED_LIBRARY)  //决定编译为BUILD_STATIC_LIBRARY（静态库），BUILD_SHARED_LIBRARY （动态库 ）

hello.c里:

    // jni中打印log的宏 需要在Android.mk文件中添加LOCAL_LDLIBS += -llog
    #include <android/log.h>
    #define LOG_TAG "System.out"
    #define LOGD(...)__android_log_print(ANDROID_LOG_DEBUG,LOG_TAG,__VA_ARGS__)
    #define LOGI(...)__android_log_print(ANDROID_LOG_INFO,LOG_TAG,__VA_ARGS__)
