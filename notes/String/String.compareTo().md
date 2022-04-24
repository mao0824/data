# String.compareTo()

- 字符串前面部分的每个字符完全一样，返回：后面两个字符串长度差；
- 字符串前面部分的每个字符存在不一样，返回：出现不一样的字符 ASCII 码的差值。
- 字符串的每个字符完全一样，返回 0；

在String内部还有个静态内部类CaseInsensitiveComparator也实现了该接口

```java
private static class CaseInsensitiveComparator
            implements Comparator<String>, java.io.Serializable{
                //.................
            }
1234
```

该重写的接口方法是String对象的大小写不敏感比较方法

```java
        public int compare(String s1, String s2) {
            int n1 = s1.length();
            int n2 = s2.length();
            int min = Math.min(n1, n2);
            for (int i = 0; i < min; i++) {
                char c1 = s1.charAt(i);
                char c2 = s2.charAt(i);
                //转大写
                if (c1 != c2) {
                    c1 = Character.toUpperCase(c1);
                    c2 = Character.toUpperCase(c2);
                    //还不一样则转小写
                    if (c1 != c2) {
                        c1 = Character.toLowerCase(c1);
                        c2 = Character.toLowerCase(c2);
                        //还不一样则：返回不一样字符的ASCII 码的差值。
                        if (c1 != c2) {
                            // No overflow because of numeric promotion
                            return c1 - c2;
                        }
                    }
                }
            }
            return n1 - n2;
        }
```

 