---
layout: post
title: "DanmakuFlameMaster使用小结"
description: ""
category: ANDROID
tags: [ANDROID]
---

*版权声明：本文为博主原创文章，未经博主允许不得转载。*

最近有在做一个视频软件，需要用到弹幕，使用过程中没有文档可以借鉴，比较痛苦，在这里写上一些总结

弹幕相关连接：
[http://www.oschina.NET/p/danmakuflamemaster](http://www.oschina.NET/p/danmakuflamemaster "点击这里")

弹幕格式有两种  xml和json的 代码里有相应的例子，由于我平常用惯了json格式，就只用json

结果json数据例子一打开我一脸阿库娅

    [{"c":"0,16777215,1,25,196050,1364468342","m":"。。。。。。。。。。。。。。。。。。。。。。"}]

经过多次尝试以及上网搜索。。总结出来了。。

`{"c": "播放时间,颜色,模式,字号,uid,发送时间", "m": "弹幕内容"} ` 

这里介绍下c里面各自参数：

1、播放时间单位是秒 如1.234

2、颜色是十进制：颜色对照例子如下

白色:16777215 红色:16711680 绿色:65280 蓝色:255 牡丹红:16711935 青色:65535

但是安卓里十进制颜色它直接用是懵逼的，所以我到danmaku源码里找到了十进制颜色转化为可以直接setTextColor的代码：

    public static int getColorByInt(int colorInt){  
         return colorInt | -16777216;  
    } 

3、模式在BaseDanmaku里有声明，总结一下就是
1:滚动弹幕
4:底端弹幕
5:顶端弹幕

但是又遇到了视频卡顿VideoView无法监听就没办法实时改变弹幕状态的问题

到网上找了如下的解决方法

```
Handler myHandler = new Handler(new Handler.Callback() {  
  
  
        @Override  
        public boolean handleMessage(Message msg) {  
            // TODO Auto-generated method stub  
            switch (msg.what) {  
  
  
                case PROGRESS_CHANGED:  
        if (mDanmakuView != null && mDanmakuView.isPrepared()) {  
                        // 监听是否卡顿  
                        int nowduration = mVideoView.getCurrentPosition();  
                        if (old_duration == nowduration) {  
                            //卡顿  
                            if (mVideoView.isPlaying()) {  
                                mDanmakuView.pause();  
                            }  
                        } else {  
                            //正常  
                            if (mVideoView.isPlaying()&&mDanmakuView.isPaused()) {  
                                mDanmakuView.resume();  
                            }  
                        }  
                        old_duration = nowduration;  
                    }  
                    myHandler.sendEmptyMessageDelayed(PROGRESS_CHANGED, 500);  
                    break;  
            }  
            return false;  
        }  
    });  
    myHandler.sendEmptyMessage(PROGRESS_CHANGED);  
    ```
