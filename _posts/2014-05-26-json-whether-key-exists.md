---
layout: post
title: "在解析json时遇到get(key);key不存在时解析错误的问题"
categories: ANDROID
tags: [JSON]
---

JsonObject对象自带has(String key)方法用来确定是否存在该key值对应的value

    JSONObject jsonObject1 = new JSONObject();  
    JSONObject jsonObject2 = new JSONObject();  
    try {  
        jsonObject2.put("aa", "aa");  
    } catch (JSONException e) {  
        // TODO Auto-generated catch block  
        e.printStackTrace();  
    }  
    Log.i("tag1", jsonObject1.has("aa") + "");  
    Log.i("tag2", jsonObject2.has("aa") + "");  

输出log为：
05-26 10:39:39.240: I/tag1(7406): false
05-26 10:39:39.240: I/tag2(7406): true

这是以前初学Android时遇到的一个问题  后来都用的Gson 近段时间才看到别处有解决办法 