---
layout: post
title: about reflection
category: java
tags: [reflection, java]
---
用反射进行对象初始化的时候，可以参考构造函数，如果默认构造函数的参数不为空的话需要先取得全部构造函数，如下：
    try{
        demo = Class.forName("mymaven.untitled.person.person");
    }catch (Exception ex){
        ex.printStackTrace();
    }
    person per1= null;
    person per2 = null;
    person per3  = null;
    Constructor<?> constructor[] = demo.getConstructors();
    try {
        per1 = (person)constructor[3].newInstance();
        per2 = (person)constructor[1].newInstance(100);
        per3 = (person)constructor[2].newInstance("gayyzxyx",23423);
    }catch (InstantiationException ex){
    ex.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();  //To change body of catch statement use File | Settings | File Templates.
        } catch (InvocationTargetException e) {
    e.printStackTrace();  //To change body of catch statement use File | Settings | File Templates.
    }
