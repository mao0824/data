# replace into与insert ignore的区别

mysql中常用的三种插入数据的语句:

insert into 数据库会检查主键（PrimaryKey），如果出现重复会报错；

insert ignore into  ，如果中已经存在相同的记录，则忽略当前新数据；根据主键或者是唯一索引

replace into   跟 insert 功能类似，不同点在于：replace into 首先尝试插入数据到表中， **1. 如果发现表中已经有此行数据（根据主键或者唯一索引判断）则先删除此行数据，然后插入新的数据。 2. 否则，直接插入新数据。**

要注意的是：插入数据的表必须有主键或者是唯一索引！否则的话，replace into 会直接插入数据，这将导致表中出现重复的数据。