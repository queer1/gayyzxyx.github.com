---
layout: post
title: 关于reflection
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

但是此时有一点需要注意的，constructor的顺序是根据反射的包名里面构造函数的顺序确定，不然程序会抛出以下异常:

    Exception in thread "main" java.lang.IllegalArgumentException: wrong number of arguments
        at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
        at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:39)
        at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:27)
        at java.lang.reflect.Constructor.newInstance(Constructor.java:513)

如果构造函数默认即不带参数类型，即可获取新实例完成初始化:

    per1 = (person)demo.newInstance()

可以通过反射调用类中的方法，如果方法没有参数，如下：

    public void sayName(){
        <div class=""></div>
    }
    .......
    try{
        Method method = className.getMethod("sayName");
        method.invoke(className.new Instance());
    }catch(Exception e){}

对于带参数的方法，在反射的时候要加上参数列表：

    try{
        Method method = className.getMethod("sayName",String.class,int.class);
        method.invoke(className.new Instance(),"gayyzxyx",1234);
    }catch(Exception e){}


