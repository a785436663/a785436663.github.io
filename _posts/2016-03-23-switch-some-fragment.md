---
layout: post
title: "让多个Fragment 切换时不重新实例化"
categories: ANDROID
tags: [FRAGMENT]
---

在项目中需要进行Fragment的切换，一直都是用replace()方法来替换Fragment：

    public void switchContent(Fragment fragment) {
        if(mContent != fragment) {
            mContent = fragment;
            mFragmentMan.beginTransaction()
                .setCustomAnimations(android.R.anim.fade_in, R.anim.slide_out)
                .replace(R.id.content_frame, fragment) // 替换Fragment，实现切换
                .commit();
        }
    }

但是，这样会有一个问题：
每次切换的时候，Fragment都会重新实例化，重新加载一边数据，这样非常消耗性能和用户的数据流量。
就想，如何让多个Fragment彼此切换时不重新实例化？
翻看了Android官方Doc，和一些组件的源代码，发现，replace()这个方法只是在上一个Fragment不再需要时采用的简便方法。
正确的切换方式是add()，切换时hide()，add()另一个Fragment；再次切换时，只需hide()当前，show()另一个。
这样就能做到多个Fragment切换不重新实例化：

    public void switchContent(Fragment from, Fragment to) {
        if (mContent != to) {
            mContent = to;
            FragmentTransaction transaction = mFragmentMan.beginTransaction().setCustomAnimations(
                    android.R.anim.fade_in, R.anim.slide_out);
            if (!to.isAdded()) {    // 先判断是否被add过
                transaction.hide(from).add(R.id.content_frame, to).commit(); // 隐藏当前的fragment，add下一个到Activity中
            } else {
                transaction.hide(from).show(to).commit(); // 隐藏当前的fragment，显示下一个
            }
        }
    }

#### 问题一：保存UI与数据的内存消耗 ####

上面所述为避免重新实例化而带来的“重新加载一边数据”、“消耗数据流量”，其实是这个Fragment不够“纯粹”。
Fragment应该分为UI Fragment和Headless Fragment。
前者是指一般的定义了UI的Fragment，后者则是无UI的Fragment，即在onCreateView()中返回的是null。将与UI处理无关的异步任务都可以放到后者中，而且一般地都会在onCreate()中加上setRetainInstance(true)，故而可以在横竖屏切换时不被重新创建和重复执行异步任务。
这样做了之后，便可以不用管UI Fragment的重新创建与否了，因为数据和异步任务都在无UI的Fragment中，再通过Activity 的 FragmentManager 交互即可。
只需记得在Headless Fragment销毁时将持有的数据清空、停止异步任务。

UIFragment.java

    public class UIFragment extends Fragment{
        @Override
        public View onCreateView(LayoutInflater inflater, ViewGroup container,
                Bundle savedInstanceState) {
            View view = inflater.inflate(R.layout.fragment,container, false);
            return view;
        }
        ...
    }

HeadlessFragment.java

    public class HeadlessFragment extends Fragment{
        @Override
        public void onCreate(Bundle savedInstanceState) {
            setRetainInstance(true);
        }
        @Override
        public View onCreateView(LayoutInflater inflater, ViewGroup container,
                Bundle savedInstanceState) {
            return null;
        }
        ...
    }

具体实例代码如下:
ApiDemo: [https://android.googlesource.com/platform/development/+/master/samples/ApiDemos/src/com/example/android/apis/app/FragmentRetainInstance.java](https://android.googlesource.com/platform/development/+/master/samples/ApiDemos/src/com/example/android/apis/app/FragmentRetainInstance.java "FragmentRetainInstance.java")
MostafaGazar Sample: [https://github.com/MostafaGazar/soas/blob/master/app/src/main/java/com/meg7/soas/ui/fragment/retained/PhotosListTaskFragment.java](https://github.com/MostafaGazar/soas/blob/master/app/src/main/java/com/meg7/soas/ui/fragment/retained/PhotosListTaskFragment.java "PhotosListTaskFragment.java")

#### 问题二：Fragment重叠 ####

其实是由Activity被回收后重启所导致的Fragment重复创建和重叠的问题。
在Activity onCreate()中添加Fragment的时候一定不要忘了检查一下savedInstanceState：

	if (savedInstanceState == null) {
	    getFragmentManager().beginTransaction().add(android.R.id.content,
	                new UIFragment()).commit();
	}

多个Fragment重叠则可以这样处理：通过FragmentManager找到所有的UI Fragment，按需要show()某一个Fragment，hide()其他即可！

为了能准确找出所需的Fragment，所以在add()或者replace() Fragment的时候记得要带上tag参数，因为一个ViewGroup 容器可以依附add()多个Fragment，它们的id自然是相同的。

	if (savedInstanceState == null) {
	    // getFragmentManager().beginTransaction()...commit()
	}else{
	  //先通过id或者tag找到“复活”的所有UI-Fragment
	  UIFragment fragment1 = getFragmentManager().findFragmentById(R.id.fragment1);
	  UIFragment fragment2 = getFragmentManager().findFragmentByTag("tag");
	  UIFragment fragment3 = ...
	  ...
	  //show()一个即可
	  getFragmentManager().beginTransaction()
	          .show(fragment1)
	          .hide(fragment2)
	          .hide(fragment3)
	          .hide(...)
	          .commit();
	}   

注: 关于Fragment id的问题建议阅读 FragmentManager中moveToState()源码

[http://grepcode.com/file/repository.grepcode.com/java/ext/com.google.android/android/5.0.2_r1/android/app/FragmentManager.java#793](http://grepcode.com/file/repository.grepcode.com/java/ext/com.google.android/android/5.0.2_r1/android/app/FragmentManager.java#793)