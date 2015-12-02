title: "java基础加强---Day3"
date: 2012-03-07
tags: "java"
categories: "编程"
---
#### 19.
1. 得到某个类的所有构造方法
`Constructor[] constructors
= Class.forName(“java.lang.String”).getConstructors();
`
2. 得到某个类的某一个构造方法
`Constructor constructor
= Class.forName(“java.lang.String”).getConstructor(StringBuffer.Class);
`获得方法时要用到类型
3. 创建实例对象
一、通常方式创建
`tring s = new String(new StringBuffer(“abc”));`
二、反射方式创建
`String s = (String)constructor.newInstance(new StringBuffer(“abc”));`
调用获得的方法时，要用到相同类型的实例对象
4. Class.newInstance()方法
`String s = (String)Class.forName(“java.lang.String”).newInstance();`
内部先得到默认的构造方法，然后用该构造方法创建实例对象
#### 20、Field类 alt + shift + s快捷键得到source选项下面的方法
