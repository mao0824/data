# List去重的五种方法

## 一、LinkHashSet

**[LinkedHashSet](https://docs.oracle.com/javase/8/docs/api/java/util/LinkedHashSet.html)**是在一个ArrayList删除重复数据的最佳方法。LinkedHashSet在内部完成两件事：

- 删除重复数据
- 保持添加到其中的数据的顺序

```java
LinkedHashSet<Long> set = new LinkedHashSet<>(list);
ArrayList<Long> arrayList = new ArrayList<>(set);
```

## 二、stream

使用steam的`distinct()`方法返回一个由不同数据组成的流，通过对象的**equals（）**方法进行比较。

```java
List<Long> arrayList = list.stream().distinct().collect(Collectors.toList());
```

## 三、HashSet

利用HashSet不能添加重复数据的特性 由于HashSet不能保证添加顺序，所以只能作为判断条件保证顺序

```java
HashSet<Long> hashset = new HashSet<>(list.size());
ArrayList<Long> arraylist = new ArrayList<>(list.size());
for (Long aLong : list) {
    if (hashset.add(aLong)){
        arraylist.add(aLong);
    }
}
```

## 四、List的contains方法

利用List的contains方法循环遍历,重新排序,只添加一次数据,避免重复

```java
ArrayList<Long> arraylist = new ArrayList<>(list.size());
for (Long aLong : list) {
    if (!arraylist.contains(aLong)){
        arraylist.add(aLong);
    }
```

## 五、双重for循环去重 

