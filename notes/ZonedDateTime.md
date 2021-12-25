# ZonedDateTime

`LocalDateTime`总是表示本地日期和时间，要表示一个带时区的日期和时间，我们就需要`ZonedDateTime`。

可以简单地把`ZonedDateTime`理解成`LocalDateTime`加`ZoneId`。`ZoneId`是`java.time`引入的新的时区类，注意和旧的`java.util.TimeZone`区别。

要创建一个`ZonedDateTime`对象，有以下几种方法，

一种是通过`now()`方法返回当前时间：

```java
// 默认时区
ZonedDateTime z1 = ZonedDateTime.now();
System.out.println("默认时区======"+z1);
// 自定义时区
ZonedDateTime z2 = ZonedDateTime.now(ZoneId.of("Europe/Paris"));
System.out.println("自定义时区======"+z2)
    
//默认时区======2021-12-19T22:10:41.821+08:00[Asia/Shanghai]
//自定义时区======2021-12-19T15:10:41.821+01:00[Europe/Paris]
```

观察打印的两个`ZonedDateTime`，发现它们时区不同，但表示的时间都是同一时刻（毫秒数不同是执行语句时的时间差）

另一种方式是通过给一个`LocalDateTime`附加一个`ZoneId`，就可以变成`ZonedDateTime`：

```java
LocalDateTime l1 = LocalDateTime.of(2021, 12, 18, 12, 22, 22);
ZonedDateTime z3 = l1.atZone(ZoneId.systemDefault());
System.out.println(z3);
ZonedDateTime z4 = l1.atZone(ZoneId.of("Europe/Paris"));

//2021-12-18T12:22:22+08:00[Asia/Shanghai]
//2021-12-18T12:22:22+01:00[Europe/Paris]
```

以这种方式创建的`ZonedDateTime`，它的日期和时间与`LocalDateTime`相同，但附加的时区不同，因此是两个不同的时刻

时区转化

要转换时区，首先我们需要有一个`ZonedDateTime`对象，然后，通过`withZoneSameInstant()`将关联时区转换到另一个时区，转换后日期和时间都会相应调整。

### 注意：

时区转换的时候，由于夏令时的存在，不同的日期转换的结果很可能是不同的。**涉及到时区时，千万不要自己计算时差，否则难以正确处理夏令时。**

`ZonedDateTime`转换为``LocalDateTime``,直接舍弃地区信息

```java
LocalDateTime z7 = z5.toLocalDateTime();
```

```java
// 某航线从北京飞到纽约需要13小时20分钟，请根据北京起飞日期和时间计算到达纽约的当地日期和时间。
ZonedDateTime BJTime = ZonedDateTime.of(LocalDateTime.of(2021, 9, 19, 12, 30, 40), ZoneId.systemDefault());
System.out.println("北京时间"+BJTime);
ZonedDateTime NYTime = BJTime.withZoneSameInstant(ZoneId.of("America/New_York")).plusHours(13).plusMinutes(20);
System.out.println("纽约时间"+NYTime);
//北京时间2021-09-19T12:30:40+08:00[Asia/Shanghai]
//纽约时间2021-09-19T13:50:40-04:00[America/New_York]
```

### 总结：

`ZonedDateTime`是带时区的日期和时间，可用于时区转换；

`ZonedDateTime`和`LocalDateTime`可以相互转换。

