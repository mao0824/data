# 统计某一字段不同值的个数的sql

数据类似于：

| id   | exam_id | user_id | paper_id | exam_number | question_id | user_answer | is_correct |
| ---- | ------- | ------- | -------- | ----------- | ----------- | ----------- | ---------- |
| 1    | 1       | linan48 | 1        | 1           | 2           | C           | N          |
| 2    | 1       | linan48 | 1        | 1           | 35          | C           | N          |
| 3    | 1       | linan48 | 1        | 1           | 5           | C           | Y          |
| 4    | 1       | linan48 | 1        | 1           | 37          |             | N          |
| 5    | 1       | linan48 | 1        | 1           | 40          | C           | N          |
| 6    | 1       | linan48 | 1        | 1           | 44          |             | N          |
| 7    | 1       | linan48 | 1        | 1           | 13          | C           | N          |
| 8    | 1       | linan48 | 1        | 1           | 45          |             | N          |
| 9    | 1       | linan48 | 1        | 1           | 15          | C           | N          |
| 10   | 1       | linan48 | 1        | 1           | 18          | C           | N          |
| 11   | 1       | linan48 | 1        | 1           | 20          | C           | N          |
| 12   | 1       | linan48 | 1        | 1           | 21          | C           | N          |
| 13   | 1       | linan48 | 1        | 1           | 23          | D           | N          |
| 14   | 1       | linan48 | 1        | 1           | 28          | C           | N          |
| 15   | 1       | linan48 | 1        | 1           | 29          | C           | N          |

根据id的不同，查找is_correct为N和Y的个数。

SQL语句

```sql
select
	exam_id,
	sum(case when is_correct = 'Y' then 1 else 0 end) as correct_num,
	sum(case when is_correct = 'N' then 1 else 0 end) as worng_num
from
	kb_e_user_answer
where
	user_id = 'linan48'
	and exam_number = 1
group by
	exam_id
```

查询结果

| exam_id | correct_num | worng_num |
| ------- | ----------- | --------- |
| 1       | 1           | 14        |
| 2       | 2           | 13        |
| 4       | 0           | 1         |

