# Java中对象的比较

对于JDK1.8而言，有3种实现对象比较的方法：

1、覆写Object类的equals（）方法；

2、继承Comparable接口，并实现compareTo（）方法；

3、定义一个单独的对象比较器，继承自Comparator接口，实现compare（）方法。

在Java中经常会涉及到对象数组的排序问题，那么就涉及到对象之间的比较问题。Java中的对象，正常情况下，只能进行比较: ==或!=（也就是两个对象的引用地址是否相同），不能使用>或<来比较对象的大小。但是在实际的开发场景中，我们需要对多个对象进行排序，言外之意，就需要比较对象的大小，如何实现?使用两个接口中的任何一个: `Comparable或 Comparator`。

## 自然排序： Comparable

Comparable接口强行对实现它的每个类的对象进行整体排序。这种排序被称为类的自然排序。

- 实现comparable的类必须实现 **compareTo(Object obj)** 方法，两个对象即通过 compareTo(Object obj) 方法的返回值来比较大小。如果当前对象this大于形参对象obj，则返回正整数，如果当前对象this小于形参对象obj，则返回负整数，如果当前对象this等于形参对象obj，则返回0。
- Comparable可以认为是一个**内比较器，实现了Comparable接口的类有一个特点，就是这些 类是可以和自己比较的**。若一个类实现了Comparable接口，就意味着该类支持排序。
- 实现Comparable接口的对象列表（比如list集合）和数组可以通过 **Collections.sort 或Arrays.sort**进行自动排序( **Collections或Arrays的排序的参数分别适用于集合和数组，牢记**)。实现此接口的对象可以用作有序映射中的键或有序集合中的元素，无需指定比较器。

- **Comparable** **的典型实现**：(**默认都是从小到大排列的**)

  String：按照字符串中字符的Unicode值进行比较

  Character：按照字符的Unicode值来进行比较

  数值类型对应的包装类以及BigInteger、BigDecimal：按照它们对应的数值大小进行比较

  Boolean：true 对应的包装类实例大于 false 对应的包装类实例

  Date、Time等：后面的日期时间比前面的日期时间大

## 定制排序 Comparator

Comparator是比较接口，我们如果需要控制某个类的次序，而该类本身不支持排序(即没有实现Comparable接口)，那么我们就可以建立一个“该类的比较器”来进行排序，这个“比较器”只需要实现Comparator接口即可。总结为以下两点:

**对象不支持自己和自己比较（没有实现Comparable接口），但是又想对两个对象进行比较（大都是这种情况）**；

对象实现了Comparable接口，但是开发者认为compareTo方法中的比较方式并不是自己想要的那种比较方式；

- 可以将 Comparator 传递给 sort 方法（如 Collections.sort 或 Arrays.sort），从而允许在排序顺序上实现精确控制。
- 还可以**使用 Comparator 来控制某些数据结构（如有序 set或有序映射）的顺序，或者为那些没有自然顺序的对象 collection 提供排序**。

### Comparable 的 Comparator

Comparable 和 Comparator 都是接口，用来对自定义的类进行比较。

区别：

- Comparable 是定义在自定义类的内部，需要自定义类实现 compareTo 方法。例如 String 类就实现了 Comparable 接口，所以 Arrays.sort(String)、Collections.sort(String) 等方法才能获得正确的结果。
- Comparator 是定义在自定义类的外部，自定义类结构不需要有任何变化，我们自定义一个比较器，实现 compare 方法

总结：

实现 Comparable 接口的对象可以直接作为比较的对象，但是需要修改自定义类的结构。用Comparator 的好处是不需要修改自定义类的结构，并且可以根据实际需要实现比较器而不用在自定义类时就思考如何比较。

