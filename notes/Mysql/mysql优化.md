# mysql优化

### 开启查询缓存，优化查询



### explain你的select查询，这可以帮你分析你的查询语句或是表结构的性能瓶颈。 EXPLAIN 的查询结果还会告诉你你的索引主键被如何利用的，你的数据表是如何被搜索 和排序的



### 当只要一行数据时使用limit 1，MySQL数据库引擎会在找到一条数据后停止搜索，而不 是继续往后查少下一条符合记录的数据为搜索字段建索引



### 使用 ENUM 而不是 VARCHAR。如果你有一个字段，比如“性别”，“国家”，“民族”， “状态”或“部门”，你知道这些字段的取值是有限而且固定的，那么，你应该使用 ENUM 而不是VARCHAR

alter table deleted add adders enum("Y","N");

ENUM 类型是非常快和紧凑的。在实际上，其保存的是 TINYINT，但其外表上显示为字符串。这样一来，用这个字段来做一些选项列表变得相当的完美。

可以用PROCEDURE ANALYSE()命令去查看mysql的建议

PROCEDURE ANALYSE()的用法

比如需要分析temp表，则用命令：select * from temp PROCEDURE ANALYSE()

### Prepared StatementsPrepared Statements很像存储过程，是一种运行在后台的SQL 语句集合，我们可以从使用 prepared statements 获得很多好处，无论是性能问题还是 安全问题。Prepared Statements 可以检查一些你绑定好的变量，这样可以保护你的程序不会受到 “SQL注入式”攻击



### 垂直分表



### 选择正确的存储引擎