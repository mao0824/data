# 时间戳与Data之间的转换

## 1.Date对象转换为时间戳

```java
Date date = new Date();
long time = date.getTime();
```

输出：

```shell
1639491471351
```

## 2.时间戳转换为Date日期对象

```java
long l = System.currentTimeMillis();
Date date = new Date(l);
```

输出：

```shell
Tue Dec 14 22:21:24 CST 2021
```

## 3.时间戳转换为Timestamp

```java
long l = System.currentTimeMillis();
Timestamp timestamp = new Timestamp(l);
```

输出：

```shell
2021-12-14 22:24:11.299
```

## 4.时间戳转换为指定格式的日期

```java
long l = System.currentTimeMillis();
SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS");
String date = simpleDateFormat.format(l);
```

输出：

```shell
2021-12-14 22:29:02.049 # String类型的
```

## 5.时间字符串<年月日时分秒毫秒 >转为 时间戳

```java
//大写HH：24小时制，小写hh：12小时制 毫秒：SSS
//指定转化前的格式
SimpleDateFormat sdf = new SimpleDateFormat("yyyyMMddHHmmssSSS");
//转化后为Date日期格式
Date date2 = sdf.parse("20211214223221456");
//Date转为时间戳long
long shootTime = date.getTime();
```

输出：

```shell
1639492439601
```

## 6.将指定格式的日期转换为时间戳

```java
String str = "2021-12-14 22:33:59.601";
DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss.SSS");
LocalDateTime localDateTime = LocalDateTime.parse(str, dateTimeFormatter);
Timestamp timestamp = Timestamp.valueOf(localDateTime);
long time = timestamp.getTime();
```

输出：

```shell
1639492439601
```

