# Java的占位符-格式修饰符

Java中的printf()方法是类似于C语言的printf()风格的一种格式化输出功能。printf()并不使用重载的 “+” 操作符（C没有重载）来连接引号内的[字符串](https://so.csdn.net/so/search?q=字符串&spm=1001.2101.3001.7020)或者字符串变量，而是使用特殊的占位符来表示数据将来的位置。而且它将插入格式化字符串的参数，按照顺序以逗号隔开，在字符串后面顺次排列。
这些[占位符](https://so.csdn.net/so/search?q=占位符&spm=1001.2101.3001.7020)在Java中被称为 **格式修饰符** 。

上面示例中，**按照顺序，x将插入到%d的位置，y将插入到%s的位置，同时，%d表示x是一个整数，%f表示是一个浮点数（float或者double都可能）**。

总结：

使用格式修饰符作为占位符，可以格式化输出字符串中指定位置、指定类型的内容，使得控制输出的代码更加简单，提升Java开发者对于输出格式和排列的控制能力。

|          |                                             |
| -------- | ------------------------------------------- |
| 转 换 符 | 说 明                                       |
| %s       | 字符串类型                                  |
| %c       | 字符类型                                    |
| %b       | 布尔类型                                    |
| %d       | 整数类型（十进制）                          |
| %x       | 整数类型（十六进制）                        |
| %f       | 浮点类型                                    |
| %o       | 整数类型（八进制）                          |
| %a       | 十六进制浮点类型                            |
| %e       | 指数类型                                    |
| %g       | 通用浮点类型（f和e类型中较短的）            |
| %h       | 散列码                                      |
| %%       | 百分比类型                                  |
| %tx      | 日期与时间类型（x代表不同的日期与时间转换符 |
| %n       | 换行符                                      |