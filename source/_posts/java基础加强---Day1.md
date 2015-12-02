title: "java基础加强---Day1"
date: 2012-02-23
tags: "java"
categories: "编程"
---
1. 面试时，即使不知道 jdk1.6的特性，也要把jdk1.5的特性说一下。
2. javaw是java图形化界面时启动的进程。英文单词的简称
3. workspace里有很多project，设置快捷键Window–Preferences–General—keys–
Content Assist，syso +　alt / ，出来System.out.println();
com.itheima.day1 变量名命名准确
4. Perspective (透视图) view(视图)
5. 编译环境和运行环境要一致， workspace与project环境的配置，体现了extend和override
的应用
6. 配置模块代码 Preferences–Java–Editor–Templates，即可根据自己的需要创建新模板
7. 配置jdk，选择 Build Path
8. 静态导入 import static java.lang.Math.* 在程序中就可以不用写Math而直接使用Math的
方法。
9. overload和override的区分； VariableParameter 接收可变参数，只能出现在参数列表的最
后，例如int … args，调用时采用args[]数组的形式访问
10. ctrl + shift + / 注释代码 增强的for循环 for(类型 变量名：集合变量名)，集合变量可以
是数组或者实现了Iterable接口的集合类
11. Integer 自动装箱，把int类型装入，不需要转换，Integer和int相加时，自动拆箱成int类型
相加；当int类型的值为-128~127时，共享一个元类，超出该范围时，只不共享。FlyWeight享
元设计模式，避免大量拥有相同内容的小类的开销(如耗费内存),使大家共享一个类(元类)，有内
部状态intrinsic和外部状态extrinsic之分
```
String s1 = “abcd”;
String s2 = “abcd”;
System.out.println(s1 == s2);
String s3 = new String(“a”);
String s4 = new String(“a”);
System.out.println(s3.equals(s4));
String s5 = “ja”;
String s6 = “va”;
String s7 = “java”;
String s8 = s1 + s2;
System.out.println(s7 == s8);//false
```
12. 枚举就是让某个类型的变量取值只能为若干固定值中的一个，否则，在编译期间编译器就会
报错，从而能够尽早的发现问题。
