# stream.sorted()

我们能够以自然序或着用Comparator 接口定义的排序规则来排序一个流。Comparator 能用用lambada表达式来初始化， 我们还能够逆序一个已经排序的流。

**1、sorted() 默认使用自然序排序， 其中的元素必须实现Comparable 接口
2、sorted(Comparator<? super T> comparator) ：我们可以使用lambada 来创建一个Comparator 实例。可以按照升序或着降序来排序元素。**

自然排序：

```java
list.stream().sorted() 
```

自然序逆序元素，使用Comparator 提供的reverseOrder() 方法:

```java
list.stream().sorted(Comparator.reverseOrder()) 
```

使用Comparator 来排序一个list:

```java
list.stream().sorted(Comparator.comparing(Student::getAge)) 
```

把上面的元素逆序:

```java
list.stream().sorted(Comparator.comparing(Student::getAge).reversed()) 
```

