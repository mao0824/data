# Map根据value获取key的四种方法

## 一、遍历Map

就是通过遍历Map里的Entry，一个个比较，把符合条件的找出来，会有三种情况

1. 找到一个值
2. 找到多个值
3. 找不到值

```java
private static <V, K> Set<K> getKeyByValue(Map<K,V> map, V value){
    HashSet<K> keySet = new HashSet<>();
    for (Map.Entry<K,V> entry : map.entrySet()){
        if (Objects.equals(entry.getValue(),value)){
            keySet.add(entry.getKey());
        }
    }
    return keySet;
}
// Objects.equals(a, b)方法，而不是用a.equals(b)方法。这样可以避免空指针异常。
```

## 二、stream

```java
private static <K, V> Set<K> getKeysByStream(Map<K, V> map, V value) {
    return map.entrySet()
            .stream()
            .filter(kvEntry -> Objects.equals(kvEntry.getValue(), value))
            .map(Map.Entry::getKey)
            .collect(Collectors.toSet());
}
```

## 三、 Guava的BiMap

Google的`Guava`提供了`BiMap`这样一个双向Map，调用`inverse()`方法会返回一个反向的关联的`BiMap`，然后便可以通过`get()`方法获取key值了。

```java
BiMap<Integer, String> biMap = HashBiMap.create();
biMap.put(1, "q");
biMap.put(2, "w");
biMap.put(3, "e");
biMap.put(4, "r");
Integer integer = biMap.inverse().get("q");
```

BiMap作为一个双向的Map，它不能存储多对一的关系；而HashMap是可以的。其实很好理解，因为是双向的，所以即要满足Key值的唯一性，也要满足Value值的唯一性。如果往里存放同样的Value，会抛异常： [java](http://www.wityx.com/).lang.IllegalArgumentException: value already present。

### 四、Apache Commons Collections的BidiMap

类似地，`Apache Commons Collections`也提供了双向Map的类`BidiMap`，它也是维持一对一的关系，不能多对一。它提供了`getKey(value)`方法返回Key值

***
```java
BidiMap<Integer, String> bidiMap = new DualHashBidiMap<>();
bidiMap.put(1, "q");
bidiMap.put(2, "w");
bidiMap.put(3, "e");
bidiMap.put(4, "r");
Integer r = bidiMap.getKey("r");
```

与Guava的`BiMap`不同的是，当存放同样的Value时，它不会抛异常，而是覆盖原有的数据。

## 总结

- 第一种方法和第二种方法本质上都是要遍历的，如果一个Map经常需要反向取Key值，则不建议使用，可以考虑Guava和Apache Commons提供的双向Map；
- 双向Map其实是一种空间换取时间的思想，虽然能较快的找到满足条件的Key值，但它也使用了更多的空间来储存双向Map；
- 双向Map并不支持多对一的关系。