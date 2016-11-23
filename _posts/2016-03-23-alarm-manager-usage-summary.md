---
layout: post
title: "AlarmManager 定时 使用小结"
categories: ANDROID
tags: [ANDROID]
---

*版权声明：本文为博主原创文章，未经博主允许不得转载。*

使用方法如下：

	Intent intent = new Intent(context, UploadDeviceStatus.class);

	//(Context context, int requestCode, Intent intent, int flags)  requestCode这个参数随意，尽量不要相同  flags这个参数
	PendingIntent sender = PendingIntent.getService(context, (int)(triggerAtTime + duration), intent,   PendingIntent.FLAG_CANCEL_CURRENT); 
	
	AlarmManager am = (AlarmManager) context .getSystemService(Context.ALARM_SERVICE); 
	
	//type有四个选项
	AlarmManager.RTC，硬件闹钟，不唤醒手机（也可能是其它设备）休眠；当手机休眠时不发射闹钟。
	AlarmManager.RTC_WAKEUP，硬件闹钟，当闹钟发躰时唤醒手机休眠；
	AlarmManager.ELAPSED_REALTIME，真实时间流逝闹钟，不唤醒手机休眠；当手机休眠时不发射闹钟。
	AlarmManager.ELAPSED_REALTIME_WAKEUP，真实时间流逝闹钟，当闹钟发躰时唤醒手机休眠；
	//triggerAtMillis根据type参数变化，如果RTC系列 就传需要定时的毫秒数   如果ELAPSED_REALTIME系列就是SystemClock.elapsedRealtime()从开机到现在的毫秒数再加上比如5秒后就加5*1000；
	//intervalMillis 如果为0就不重复  大于零就是每隔多久重复一次
	am.setRepeating(int type, long triggerAtMillis, long intervalMillis, PendingIntent operation)

> 注:如果时间过了，比如现在是9.16日，你定时时间为 9.15日，那么就会立刻发送一条广播，不会等到下一个重复时间再播