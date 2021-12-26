# SQL语言的组成部分

- SQL（Structured Query Language）指结构化查询语言
- SQL可以访问和处理数据库，包括插入，查询，更新和删除

SQL包括了所有对数据库的操作，主要是由4个部分组成：

## DDL（Data Definition Language）

DDL即``数据定义语言``,主要用来定义逻辑结构，包括数据库中的表、索引、视图、存储过程、触发器等。

常用的语句关键字：

**CREATE**

1.定义表

Create table<表名> (<列名字><数据类型>[列级完整性约束条件] , ………….);

2.定义视图

视图：从一个或者几个基表或者视图导出的表（结果集），是一个逻辑上的虚表。数据库中只存放视图的定义，不存放视图的数据。所以基表的数据变化，视图的数据也会跟着变化。

Create view <视图名> [(列名),(列名)…] as <子查询> [with check option]

3.定义索引

Create[Unique] [Cluster ] index <索引名> on <表名>[次序] (<列名>……..)

Unique选项表示此索引的每一个索引值不能重复，对应唯一的数据记录。

Cluster 选项表示要建立的索引是聚簇索引。

<表名>是所需要创建索引的基表的名称。索引可以建立在对应标的一列或者多列上，个列名之间用逗号分隔。（次序）指定了索引值的排列次序，ASC升序，DESC降序。缺省值ASC。

聚簇索引：指索引项的顺序与表中记录的物理顺序相一致的索引组织。（聚簇索引是将索引和表记录一起存放，一个基表只能建立一个聚簇索引。）（建立聚簇索引后，更新列数据时会导致表记录的物理顺序的变更，代价较高，经常更新的列不宜建立索引）

**ALTER**

Alter table<表名> [Add <新列名><数据类型>[完整性约束]] [drop <完整性约束名>] [modify <列名><数据类型>]

ADD子句用于增加新列和完整性约束条件，DROP子句用于三处指定的完整性约束条件，MODIFY子句用于修改原有的列定义，修改列名和数据类型

**DROP**

Drop table <表名>

删除表后，表中的数据，表上的索引都将删除。在oracle中，删除基表后，建立在表上的视图依然在数据字典中，但是用户引用会报错。

Drop view <视图名>；

视图是定义在了数据字典中的。

删除索引：

索引由系统进行维护，如果数据频繁的删改，系统维护索引的时间就会增加，此时可以删除不必要的索引。索引删除后，系统会从数据字典中清除该索引的描述。

**TRUNCATE**

删除表中的所有记录，包括为记录分配的所有空格

**COMMENT**

为数据字典添加注释

**RENAME**

重命名表

## DML（Data Manipulation Language）

DML即``数据操纵语言``，用于改变数据库中的数据，包括插入，删除，修改。

常用的语句关键字：

1.INSERT- 将数据插入表中

2.DELETE-更新表中的现有数据

3.UPDATE-删除数据库表中的所有记录

4.SELECT-从数据库中检索数据

## DQL（Data Query Language）

DQL即``数据查询语言``,主要用来对数据库中的各种数据对象进行查询

完整语法：

SELECT[ALL | DISTINCT] TOP n[PERCENT] WITH TIES select_list

[INTO[new table name]]

[FROM{table_name | view_name} [(optimizer_hints)]

[,{table_name2| view_name2} [(optimizer_hints)]

[ …,table_name6|view_name6 ] [(optimizer hints)]]]

[WHEREclause]

[GROUPBY clause]

[HAVINGclause]

[ORDERBY clause]

[COMPUTEclause]

[FORBROWSE]

SQL 查询语句的解析顺序： 

1、from子句组装来自不同数据源的数据；

2、where子句基于指定的条件对记录行进行筛选；

3、group by子句将数据划分为多个分组；

4、使用聚集函数进行计算；

5、使用having子句筛选分组；

6、计算所有的表达式；

7、select 的字段；

8、使用order by对结果集进行排序。

SQL语言不同于其他编程语言的最明显特征是处理代码的顺序。在大多数据库语言中，代码按编码顺序被处理。

## DCL（Data Control Language）

DCL即``数据控制语言``,

数据控制指数据库的安全性和完整性控制

SQL的数据控制语言，对表和视图的授权，完整性规则的描述以及事务开始和结束等控制语句。

SQL通过对数据库用户的授权和取消授权命令来实现相关数据的存取控制，保证数据库的安全性，。还提供了数据完整性约束的定义和检查机制，保证数据库的完整性。

主要的关键字：

GRANT-允许用户访问数据库的权限

DENY-在安全系统中创建一项，以拒绝给当前数据库内的安全帐户授予权限并防止安全帐户通过其组或角色成员资格继承权限

REVOKE-撤消使用GRANT命令给出的用户访问权限

## TCL（Transaction Control Language）

TCL即``事务控制语言``，包括事务的提交与回滚，它的语句能确保被DML语句影响的表的所有行及时得以更新

主要的关键字：

SET TRANSACTIION-指定事务的特征

ROLLBACK-在发生任何错误的情况下回滚事务

COMMIT-提交事务

SAVEPOINT - 回滚在组内创建点的事务