# 构造 K 个回文字符串

“回文串”是一个正读和反读都一样的字符串

判断一个字符串是否为回文串：

```java
public static boolean check(String str){
    if(null == str || "".equals(str))return false;
    String[] strings = str.split("");
    for (int i = 0,j = str.length() - 1; i <= j; i++,j--) {
        if(!strings[i].equals(strings[j]))return false;
    }
    return true;
}
```

### 问题：

给你一个字符串 s 和一个整数 k 。请你用 s 字符串中 所有字符 构造 k 个非空 回文串 。

如果你可以用 s 中所有字符构造 k 个回文字符串，那么请你返回 True ，否则返回 False 。

### 思路:

如果length(s) < k,直接返回false 。

