---
layout: post
title: "jni编译问题：conflicting types for XXX (方法名）"
categories: ANDROID
tags: [JNI]
---

一个很简单的原因导致

是因为方法的申明顺序导致的：1）MAIN方法尝试调用还未申明的方法；2）编译严格按照先后（从上至下）进行，所以在编译MAIN方法时，下面像个方法是找不到的。

一个方法只能调用在它之前申明的方法