# mysql相关日期函数

## to_days()

给定一个日期date, 返回一个天数 (从年份0开始的天数 )。

```sql
select TO_DAYS(NOW()) 
-- 738650

select TO_DAYS('2020-02-11') 
-- 737831

select TO_DAYS('20-02-11') 
-- 737831
```

- to_dates()只可以用于现行的阳历（1582）之后的值，原因是当日历改变时，遗失的日期不会被考虑在内。
- mysql"日期和时间类型"中的规则将日期中的二位数年份值转化为四位。例如'2020-02-11'和'20-02-11'被视为同样的日期。
- 对于1582 年之前的日期(或许在其它地区为下一年 ), 该函数的结果是不可靠的。

## from_days()

该函数从数字日期值返回日期。

该函数仅用于公历中的日期。

```sql
select FROM_DAYS(737831) 
-- 2020-02-11
```

与to_days()相反



## date_add()

该函数将[间隔](http://www.yiibai.com/mysql/interval.html)时间添加到[DATE](http://www.yiibai.com/mysql/date.html)或[DATETIME](http://www.yiibai.com/mysql/datetime.html)值。

语法：

```sql
DATE_ADD(start_date, INTERVAL expr unit)
```

参数：

`start_date`是`DATE`或`DATETIME`的起始值。

`INTERVAL expr unit`是要添加到起始日期值的间隔值。

返回值：

`DATETIME` - 如果第一个参数是`DATETIME`值，或者如果间隔值具有时间元素，如小时，分钟或秒等。

否则返回字符串。

```sql
-- 加1s时间
select DATE_ADD(CURDATE(),INTERVAL 1 SECOND)
-- 2022-05-10 00:00:01

-- 加1天
select DATE_ADD(CURDATE(),INTERVAL 1 DAY) 
-- 2022-05-11

-- 加 -1天5小时
select DATE_ADD('2022-03-21 06:00:00',INTERVAL '-1 5' DAY_HOUR) 
-- 2022-03-20 01:00:00
```

## date_sub()

该函数从[DATE](http://www.yiibai.com/mysql/date.html)或[DATETIME](http://www.yiibai.com/mysql/datetime.html)值中减去时间值(或[间隔](http://www.yiibai.com/mysql/interval.html))

参数：

- `start_date`是`DATE`或`DATETIME`的起始值。
- `expr`是一个字符串，用于确定从起始日期减去的间隔值。`unit`是`expr`可解析的间隔单位，例如`DAY`，`HOUR`等。

返回值：

类似于[DATE_ADD()](http://www.yiibai.com/mysql/date_add.html)函数，`DATE_SUB()`函数的返回值的数据类型可以是：

- 如果第一个参数是`DATETIME`，则返回值应为`DATETIME`，或者该间隔具有时间元素，如小时，分钟，秒等。
- 否则返回一个字符串。

```sql
-- 昨天的日期
SELECT DATE_SUB(CURDATE() ,INTERVAL 1 DAY)
-- 2022-05-09

-- 减1小时
select DATE_SUB('2022-03-21',INTERVAL 1 HOUR) 
-- 2022-03-20 23:00:00 因为间隔3小时，所以返回值为DATETIME类型

-- 加一天
select DATE_SUB('2022-03-21',INTERVAL -1 DAY) 
-- 2022-03-22 expr在间隔值可以为正或负数值。 如果expr为负数，则DATE_SUB()函数的行为与DATE_ADD()函数类似

-- 无效或格式错误的日期
select DATE_SUB('2022-02-30',INTERVAL -1 DAY) 
-- null
```

