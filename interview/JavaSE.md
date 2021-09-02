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

## &和&&的区别

&运算符：逻辑与

&&运算符：短路与

1. &和&&在程序中最终的运算结果是完全一致的，只不过&&会存在短路现象，当&&运算符左边的表达式结果为false的时候，右边的表达式不执行，此时就发生了短路现象。&运算符不管左边的表达式是true还是false，右边的表达式一定执行。
2. &运算符还可以使用在二进制位运算上，例如按位与。

## String  str =  new  String("xyz"); 创建了几个对象？

一个或者两个

1. 如果String常量池中，已经创建了"xyz"，此时只创建了一个对象new String("xyz")
2. 如果String常量池中，没有创建"xyz"，此时会创建两个对象，一个对象的值是"xyz"，另一个对象是str

## java中break,continue,return 的区别？

- break默认是跳出最里层的循环，也就是break所在的最近的那层循环
- continue是终止本次循环，继续下次循环
- return是结束当前方法或者将返回值返回给调用者

## "" 和null的区别

- " "是有地址但是里面的内容是空的
- null是没有地址的

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

## HashMap的底层源码以及数据结构

HashMap的底层结构在jdk1.7中由数组+链表实现的，在jdk1.8中由数组+链表+红黑树实现的

![](https://raw.githubusercontent.com/mao0824/pictureBed/master/img/20210828233707.png)

![](https://raw.githubusercontent.com/mao0824/pictureBed/master/img/20210828233717.png)

![](https://raw.githubusercontent.com/mao0824/pictureBed/master/img/20210828234023.png)

![](https://raw.githubusercontent.com/mao0824/pictureBed/master/img/20210828234031.png)

## HashMap和HashTable的区别

1. 线程安全性不同

   HashMap是线程不安全的，HashTable是线程安全的，内部的方法基本都经过Synchronize修饰的。在多线程并发的情况下，可以直接使用HashTable，但是使用HashTable是必须自己增加同步处理。

2. 是否提供contains方法

   HashMap中只有containsValue和containsKey方法；HashTable中有contains、containsValue和containsKey方法，contains和containsValue方法功能相同

3. key和value是否允许null

   HashTable中key和value都不允许出现null值，HashMap则可以

4. 数组初始化和扩容机制

   HashTable在不指定容量的情况下默认容量为11，HashMap默认容量为16，HashMap要求底层数组的容量一定是2的整数次幂。

   HashTable扩容时，将容量变为原来的2倍加1，HashMap将容量变为原来的2倍

##  HashTable和ConCurrentHashMap的区别

HashTable里使用的是synchronized关键字，这其实是对对象加锁，锁住的都是对象整体，当Hashtable的大小增加到一定的时候，性能会急剧下降，因为迭代时需要被锁定很长的时间。

ConCurrentHashMap是线程安全的HashMap的实现。ConcurrentHashMap算是对HashTable的优化，其构造函数如下，默认传入的是16，0.75，16。

```java
public ConcurrentHashMap(int paramInt1, float paramFloat, int paramInt2)  {    
    //…  
    int i = 0;    
    int j = 1;    
    while (j < paramInt2) {    
      ++i;    
      j <<= 1;    
    }    
    this.segmentShift = (32 - i);    
    this.segmentMask = (j - 1);    
    this.segments = Segment.newArray(j);    
    //…  
    int k = paramInt1 / j;    
    if (k * j < paramInt1)    
      ++k;    
    int l = 1;    
    while (l < k)    
      l <<= 1;    
    
    for (int i1 = 0; i1 < this.segments.length; ++i1)    
      this.segments[i1] = new Segment(l, paramFloat);    
  }    
  
public V put(K paramK, V paramV)  {    
    if (paramV == null)    
      throw new NullPointerException();    
    int i = hash(paramK.hashCode()); //这里的hash函数和HashMap中的不一样  
    return this.segments[(i >>> this.segmentShift & this.segmentMask)].put(paramK, i, paramV, false);    
}  
```

ConcurrentHashMap引入了**分割(Segment)**，上面代码中的最后一行其实就可以理解为把一个大的Map拆分成N个小的HashTable，在put方法中，会根据hash(paramK.hashCode())来决定具体存放进哪个Segment，如果查看Segment的put操作，我们会发现内部使用的同步机制是基于lock操作的，这样就可以对Map的一部分（Segment）进行上锁，这样影响的只是将要放入同一个Segment的元素的put操作，保证同步的时候，锁住的不是整个Map（HashTable就是这么做的），相对于HashTable提高了多线程环境下的性能，因此HashTable已经被淘汰了。

##  HashMap和ConCurrentHashMap的对比

1. ConcurrentHashMap对整个桶数组进行了分割分段(Segment)，然后在每一个分段上都用lock锁进行保护，相对于HashTable的syn关键字锁的粒度更精细了一些，并发性能更好，而HashMap没有锁机制，不是线程安全的。
2. HashMap的键值对允许有null，但是ConCurrentHashMap都不允许。

## HashSet和TreeSet的区别

HashSet是采用Hash表来实现的，其中的元素没有按照顺序排序，add()、remove()以及contains()等方法都是复杂度为O(1)的方法

TreeSet是采用树结构实现(红黑树算法)的。元素按照一定的顺序进行排列，但是add()、remove()以及contains()等方法都是复杂度O(log(n))的方法。它还提供一些方法来处理排序的set，比如first(),last(),headSet(),tailSet()等等。

## String 和 String Builder 的区别

共同点：:都是final类,不允许被继承

String : 一个不可改变的字符序列，String为字符串常量，一旦定义，不可修改

```java
String str = "hello";
str = "world"; //创建一个新的str对象，就得str=“hello”对象被回收

//由final修饰
private final char value[]; 
```

String Builder : 可以改变的字符序列，字符串变量，类似于字符串的缓冲区，可以直接对该对象进行修改，而不								进行创建和回收，效率更高。

​						       可以通过append()、indert()进行字符串的操作。

## String Buffer 和 String Builder 的区别

共同点：

- 都是final类,不允许被继承可以
- 功能和方法完全是等价的
- 都是字符串变量，字符串的缓冲区，即一个容器，里面装很多字符串并可以操作字符串

不同点：

- StringBuffer jdk1.0出现的，方法大都采用了 synchronized 关键字进行修饰，因此是线程安全的，但效率低
- StringBuilder  jdk1.5出现的，线程不安全，效率高

## 什么是Java序列化？如何实现Java序列化？

序列化就是一种用来处理对象流的机制，所谓对象流也就是将对象的内容就行流化。可以对流化的对象进行读写操作，也可以将流化后的对象传输与网络之间。序列化是为了解决在对对象流进行读写操作时所引发的问题。

把内存里面的这些对象给变成一连串的字节描述的过程。如: 文件保存

序列化的实现：将需要被序列化的类实现Serializable 接口，该接口没有需要实现的方法，实现该接口只是为了标注该对象是可被序列化的，然后使用一个输出流（比如FileOutputStream）来构造一个ObjectOutputStream（对象流）对象，接着使用ObjectOutputStream对象的writeObject（Object obj）方法就可以将参数为obj的对象写出（即保存其状态），要恢复的话则用输入流。

**注意：1、静态变量不会被序列化的，它可不在堆内存中，序列化只会序列化堆内存的数据**

​            **2、transient 修饰的属性，是不会被序列化的**

## Object中有哪些方法？

1. protected Object clone（）---创建并返回此对象的一个副本

2. boolean equals（Object obj）---指示某个其他对象是否与此对象“相等”

3. protect void finalize()---当垃圾回收器确定不存在该对象的更多引用时，由对象的垃圾回收器调用此方法

4. Class<？extends Object>getClass()---返回一个对象的运行时类

5. int hasCode()---返回该对象的哈希码值

6. void notify()---唤醒在此对象监视器上的等待的单个线程

7. void notifyAll()---唤醒在此对象监视器上的等待的所有线程

8. String toString()---返回该对象的字符串表示

9. void wait()---导致当前线程等待，直到其他线程调用此对象的void notify()或者void notifyAll()方法。

   void wait(long timeout)---导致当前线程等待，直到其他线程调用此对象的void notify()或者void notifyAll()方法，或者超过指定的时间

   void wait(long timeout, int nanos)---导致当前的线程等待，直到其他线程调用此对象的 notify()