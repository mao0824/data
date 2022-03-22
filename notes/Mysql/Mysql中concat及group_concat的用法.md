# Mysql中concat及group_concat的用法

## concat()函数

作用：将多个字符串连接成一个字符串

语法：concat(str1,str2,str3,..) 返回结果为连接参数产生的字符串，如果有任何一个参数为null,则返回值为null

## concat_ws()函数

concat with separator

作用：和concat()函数一样，将多个字符串连接成一个字符串，但是可以一次性指定分隔符。

语法：concat_ws(separator,str1,str2....)

## group_concat()函数

在有group by的查询语句中，select指定的字段要么就包含在group by语句的后面，作为分组的依据，要么就包含在聚合函数中

作用：将group by产生的同一个分组中的值连接起来，返回一个字符串结果。

语法：group_concat( [distinct] 要连接的字段 [order by 排序字段 asc/desc  ] [separator '分隔符'] )

通过使用distinct可以排除重复值；如果希望对结果中的值进行排序，可以使用order by子句；separator是一个字符串值，缺省为一个逗号