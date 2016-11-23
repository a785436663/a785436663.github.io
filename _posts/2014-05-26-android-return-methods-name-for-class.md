---
layout: post
title: "根据反射获取类中参数类型及参数名和参数值"
categories: ANDROID
tags: [ANDROID]
---

根据反射获取类中参数类型及参数名和参数值

	public String getMyDeclaredMethods(T t) {  
	    Class<?> c = t.getClass();  
	    Method m[] = c.getDeclaredMethods();  
	    String text = "";  
	    String n = c.getCanonicalName();  
	    for (int i = 0; i < m.length; i++) {  
	        // Method.getModifiers()表示修饰符 比如public 返回的是int类型 对应表如下：  
	        // PUBLIC: 1  
	        // PRIVATE: 2  
	        // PROTECTED: 4  
	        // STATIC: 8  
	        // FINAL: 16  
	        // SYNCHRONIZED: 32  
	        // VOLATILE: 64  
	        // TRANSIENT: 128  
	        // NATIVE: 256  
	        // INTERFACE: 512  
	        // ABSTRACT: 1024  
	        // STRICT: 2048  
	        // Field.getModifiers()作用同上  
	        text = text + m[i].getModifiers() + "   " + m[i].getReturnType()  
	                + "   " + m[i].getName() + "\n";  
	    }  
	    // 也可以用getFields 不过这个方法只能返回修饰为public的参数  
	    Field[] fields = t.getClass().getDeclaredFields();// 获取public private  
	                                                        // protect声明的属性的数组  
	    for (Field field : fields) {  
	        try {  
	            field.setAccessible(true);// 暴力反射；  
	            // field.getType()获取参数类型 ；field.getName()获取参数名 ；field.get(t)获取参数  
	            text = text + field.getType() + "  " + field.getName() + "  "  
	                    + field.get(t).toString() + "\n";  
	  
	            if (field.getType() == String.class)// 比较字节码用==  
	            {  
	                String oldValue;  
	                oldValue = (String) field.get(t);  
	                String newValue = oldValue.replace('b', 'a');  
	                field.set(t, newValue);// 往该参数里输入参数  
	  
	            } else if (field.getName().equals("a")) {  
	                field.set(t, 2);  
	            }  
	        } catch (IllegalAccessException e1) {  
	            // TODO Auto-generated catch block  
	            e1.printStackTrace();  
	        } catch (IllegalArgumentException e1) {  
	            // TODO Auto-generated catch block  
	            e1.printStackTrace();  
	        }  
	    }  
	    return text + n + "\n" + t.toString();  
	}  