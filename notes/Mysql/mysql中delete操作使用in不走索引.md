# mysql中delete操作使用in不走索引

```mysql
delete from `user` u where u.user_id in (select o.user_id from org o where o.com_code = '%' )
```

很简单的语句，但是发现没走user表的索引，导致删除操作特别慢。

同样的语句改为select语句就很快，因为会走索引。

```mysql
select * from `user` u where u.user_id in (select o.user_id from org o where o.com_code = '%' )
```

想要解决上述问题，可以将in替换成inner join 

```mysql
delete from `user` u inner join org o on u.user_id = o.user_id where o.com_code = '%' 
```

比较靠谱的解释是：

之所以会卡住是因为子查询的操作肯定会创建临时表，当然创建内存临时表并不可怕但是你的数据量很大的时候，内存临时表的单表大小限制后，临时表会转换为写入磁盘形式的物理内存表。

这两个参数决定了你临时表的大小：

tem_table_size

max_heap_table_size

