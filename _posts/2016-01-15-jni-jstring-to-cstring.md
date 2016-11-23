---
layout: post
title: "利用Java的String类来完成字符编码转换"
categories: ANDROID
tags: [JNI]
---

	/** 
	 * 利用Java的String类来完成字符编码转换 
	 */  
	char* Jstring2CStr(JNIEnv* env, jstring jstr) {  
	char* rtn = NULL;  
	jclass clsstring = (*env)->FindClass(env,"java/lang/String");  
	jstring strencode = (*env)->NewStringUTF(env,"GB2312"); //转换成Cstring的GB2312，兼容ISO8859-1  
	jmethodID mid = (*env)->GetMethodID(env,clsstring, "getBytes",  
	        "(Ljava/lang/String;)[B");  
	jbyteArray barr = (jbyteArray) (*env)->CallObjectMethod(env,jstr, mid, strencode); //String.getByte("GB2312");  
	jsize alen = (*env)->GetArrayLength(env,barr);  
	jbyte* ba = (*env)->GetByteArrayElements(env,barr, JNI_FALSE);  
	if (alen > 0) {  
	    rtn = (char*) malloc(alen + 1);  
	    memcpy(rtn, ba, alen);  
	    rtn[alen] = 0; //"\0"  
	}  
	(*env)->ReleaseByteArrayElements(env,barr, ba, 0);  
	return rtn;  
	}  