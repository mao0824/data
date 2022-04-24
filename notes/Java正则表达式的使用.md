# Java正则表达式的使用

## 正则表达式

正则表达式(Regular Expression)是一种文本模式，包括普通字符（例如，a 到 z 之间的字母）和特殊字符（称为"元字符"）。

正则表达式使用单个字符串来描述、匹配一系列匹配某个句法规则的字符串。一般用于字符串匹配, 字符串查找和字符串替换。

## java.util.regex 包

java.util.regex 包主要包括以下三个类：

### **Pattern 类：**

pattern 对象是一个正则表达式的编译表示。Pattern 类没有公共构造方法。要创建一个 Pattern 对象，你必须首先调用其公共静态编译方法，它返回一个 Pattern 对象。该方法接受一个正则表达式作为它的第一个参数。

### **Matcher 类：**

Matcher对象是对输入字符串进行解释和匹配操作的引擎。与Pattern类一样，Matcher也没有公共构造方法。你需要调用 Pattern 对象的 matcher 方法来获得一个 Matcher 对象。

#### `matches()方法`

`matches()`方法会将整个字符串与模板进行匹配

#### `find()方法`

`find()`则是从当前位置开始进行匹配, 如果传入字符串后首先进行`find()`, 那么当前位置就是字符串的开头, 对当前位置的具体分析可以看下面的代码示例

```java
// 匹配3~5个数字
Pattern pattern = Pattern.compile("\\d{3,5}");
String s = "123-34345-234-00";
Matcher m = pattern.matcher(s);

//先演示matches(), 与整个字符串匹配.
System.out.println(m.matches());
//结果为false

//然后演示find(), 先使用reset()方法把当前位置设置为字符串的开头
m.reset();
System.out.println(m.find());//true 匹配123成功
System.out.println(m.find());//true 匹配34345成功
System.out.println(m.find());//true 匹配234成功
System.out.println(m.find());//false 匹配00失败

//下面我们演示不在matches()使用reset(), 看看当前位置的变化
m.reset();//先重置
System.out.println(m.matches());//false 匹配整个字符串失败, 当前位置来到-
System.out.println(m.find());// true 匹配34345成功
System.out.println(m.find());// true 匹配234成功
System.out.println(m.find());// false 匹配00始边
System.out.println(m.find());// false 没有东西匹配, 失败
```

#### `lookingAt()方法`

`lookingAt()`方法会从字符串的开头进行匹配。

```java
// 匹配3~5个数字
Pattern pattern = Pattern.compile("\\d{3,5}");
String s = "123-34345-234-00";
Matcher m = pattern.matcher(s);
//演示lookingAt(), 从头开始找
System.out.println(m.lookingAt());//true 找到123, 成功
```

#### `start()方法`

如果一次匹配成功的话`start()`用于返回匹配开始的位置。

#### `end()方法`

`end()`用于返回匹配结束字符的后面一个位置

#### `group()方法`

分组

```java
// "\\d{3,5}[a-z]{2}"表示3~5个数字跟上两个字母, 然后打印出每个匹配到的字符串.
Pattern p = Pattern.compile("\\d{3,5}[a-z]{2}");
String s = "123aa-5423zx-642oi-00";
Matcher m = p.matcher(s);
while(m.find()) {
    String group = m.group();
    System.out.println(group);
}
// 输出结果
// 123aa
// 5423zx
// 642oi
while(m.find()) {
    String group = m.group(1);
    System.out.println(group);
}
// 输出结果
// 123
// 5423
// 642
```

如果想要打印每个匹配串中的数字, 如何操作呢？

首先你可能想到把匹配到的字符串再进行匹配, 但是这样太麻烦了, 分组机制可以帮助我们在正则表达式中进行分组。

规定使用()进行分组, 这里我们把字母和数字各分为一组`"(\\d{3,5})([a-z]{2})"。`

然后在调用`m.group(int group)`方法时传入组号即可。

注意：组号从0开始, 0组代表整个正则表达式, 从0之后, 就是在正则表达式中从左到右每一个左括号对应一个组. 在这个表达式中第1组是数字, 第2组是字母。

### **PatternSyntaxException：**

PatternSyntaxException 是一个非强制异常类，它表示一个正则表达式模式中的语法错误。

```java
// Pattern.matches()源码
public static boolean matches(String regex, CharSequence input) {
    // 创建一个 Pattern 对象
    Pattern p = Pattern.compile(regex);
    // 获得一个 Matcher 对象
    Matcher m = p.matcher(input);
    return m.matches();
    }
```

### 总结：

`Pattern`可以理解为一个模式, 字符串需要与某种模式进行匹配。

我们看到创建`Pattern`对象时调用的是`Pattern`类中的`compile`方法, 也就是说对我们传入的正则表达式编译后得到一个模式对象. 而这个经过编译后的模式对象, 会使得正则表达式使用效率会大大提高, 并且作为一个常量（Pattern被final修饰）, 它可以安全地供多个线程并发使用。

`Matcher`可以理解为模式匹配某个字符串后产生的结果。字符串和某个模式匹配后可能会产生很多个结果。

最后当我们调用`m.matches()`时就会返回完整字符串与模式匹配的结果。

### 注意：

上面的三行代码可以写为input.matches(regex),因为`String`类中有个`matches(String regex)`方法, 返回值为布尔类型, 用于告诉这个字符串是否匹配给定的正则表达式。

但是如果一个正则表达式需要被重复匹配, 这种写法效率较低。我理解为因为**预编译**，通过`Pattern.compile(regex)`预编译返回**Pattern对象**，在后面代码中可以直接引用。通过`matches(String regex)`即用编译，虽然也会有缓存**Pattern对象**，但是每次使用都需要去缓存中取出，比预编译多一步取操作。

