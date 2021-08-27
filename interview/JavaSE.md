# Java SE

[TOC]



## 你是怎么理解面向对象的？

面对对象有利于语言对现实事物进行抽象。面对对象有以下特征：

1. 继承：继承是从已有类得到继承信息创建新类的过程
2. 封装：通常认为封装是把数据和操作数据的方法绑定起来，对数据的访问只能通过已定义的接口。
3. 多态性：多态是指代码执行过程中呈现的多种形式。编译时多态体现形式是重载，运行时体现形式有重写和向上造型
4. 抽象：抽象是将一类对象的共同特征总结出来构造类的过程，包括数据抽象和行为抽两方面

## int和integer的区别

- Integer是int的包装类，int是java的一种基本类型
- Integer变量必须实例化后才能使用，int不需要
- Integer实际是对象的引用，当new一个Integer时，实际上是生成一个指针指向对象，而int则是直接存储数据值
- Integer的默认值为null，int的默认值为0
- java在编译Integer i = 100；时，会翻译成Integer i = Integer.valueOf(100)。Java API中队=对Integer类型的valueOf的定义如下：对于-128到127之间的数，会进行缓存，Integer i = 127时，会将127这个Integer对象进行缓存，下次再写Integer j = 127时，就会直接从缓存中取，就不会new了

```java
            Integer a = 127;  
	        Integer b = 127;  
	        Integer c = 128;  
	        Integer d = 128;  
	        System.out.println(a==b); //true  
	        System.out.println(c==d); //false  
```

## == 和 equals 的区别

**==**

基本数据类型在比较时，比较的是变量的值

引用类型在比较时，比较的是地址值

**equals**

equals是Object的方法

如果没有重写equals方法比较的是两个对象的地址值

如果重写以后equals方法比较的是两个对象的属性值

## 谈谈你对反射的理解

**反射（Reflection）机制：**

所谓的反射机制就是java语言在运行时拥有的一项自观的能力，通过这种能力可以彻底了解自身的情况为下一步的动作做准备。

Java反射机制的实现主要借助四个类：class、Constructor、Field、Method

其中class代表的是运行时对象，Constructor是类的构造器对象，Field是类的属性对象，Method是类的方法对象，通过这四个对象可以粗略的看到一个类的各个组成部分。

**反射的作用**

在Java运行时环境中，对于任意一个类，可以知道这个类有哪些属性和方法。对于任意一个对象，可以调用它的任意一个的方法。这种动态获取类的信息以及动态调用对象的方法的功能来自Java语言的反射机制。

**反射机制提供的功能**

- 在运行时判断任意一个对象所属的类
- 在运行时构造任意一个类的对象
- 在运行时判断任意一个类所具有的成员变量和方法
- 在运行时调用任意一个对象的方法

## ArrayList和LinkedList的区别

1. ArrayList是实现了基于动态数组的数据结构，LinkedList基于链表的数据结构
2. 对于随机访问get和set，ArrayList绝对优于LinkedList，因为LinkedList要移动指针
3. 对于新增和删除操作，LinkedList比较有优势，因为ArrayList要移动数据。但若只对单条数据插入或者删除，ArrayList的速度反而优于LinkedList。但若是批量随机的插入删除数据，LinkedList的速度大大优于ArrayList，因为ArrayList每插入一条数据，要移动插入点及之后的数据