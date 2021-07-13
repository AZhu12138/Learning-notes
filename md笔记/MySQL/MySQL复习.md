```sql
SELECT [ALL | DISTINCT]{ ... }
FROM tablename [AS ...] 
[LEFT | RIGHT | INNER JOIN tablename_2] -- 连接查询
[WHERE ...] -- 查询结果满足的条件
[GROUP BY ...] -- 按哪个/些字段分组
[HAVING ...] -- 过滤分组满足的次要条件
[ORDER BY ...] -- 按哪个/些列排序（asc升 desc降）
[LIMIT {起始位置,范围大小}] -- 从哪条记录到哪条记录,limit (pNow-1)*pSize,pSize


CONCAT(,) -- 字符串拼接

BETWEEN 2 AND 5 -- [2,5]
DISTINCT -- 去除重复列

SELECT VERSION(); -- 查询mysql版本
SELECT @@auto_increment_increment -- 查询自增的步长

SELECT ... FROM ... WHERE a!=2;
SELECT ... FROM ... WHERE NOT a==2; -- 一样的结果

-- 模糊查询
WHERE a IS NULL; 
WHERE a IS NOT NULL;
WHERE a BETWEEN b AND c;
WHERE a IN(b1,b2,b2);

WHERE a LIKE b; -- %(0到任意多个字符)，_(一个字符)
WHERE `name` LIKE '%刘%';

/* 连接查询 join (表) on 等值条件
inner join : 如果表中至少有一个匹配，就查出来
left join : 左表所有的都查出来，即使右表没有（以左表为基础）
right join : 右表所有的都查出来，即使左表没有（以右表为基础）

1、分析需要查的列
2、判断需要连接的表
3、选择连接查询的种类：7种
*/
select s.studntNO,studentName,courseNo,grade
from student as s
inner join course as c
on s.studntNO = c.studntNO
where ...

-- ==常用函数==
-- 数学运算
select abs(-8) -- 绝对值
select ceiling(9,4) -- 向上取整
SELECT floor(9,4) -- 向下取整
SELECT rand() -- (0,1)随机数
SELECT sign(4) -- 返回参数的符号 -1,0,1

-- 字符串函数
select char_length() -- 字符串长度
select concat(,,) -- 字符串拼接
select insert('abcdefg',1,2,'ert') -- 插入替换
select lower() -- 转小写
SELECT uppER() -- 转大写

-- 系统
select user() 
select version()


-- ==聚合函数==
count() sum() avg() max() min()

select count(sname) from s -- 会忽略null值
SELECT COUNT(*) FROM s -- 不会忽略null值
SELECT COUNT(1) FROM s -- 不会忽略null值

-- md5加密
md5(pwd)

-- ===事务===
set autocommit = 0 -- 关闭自动提交
set autocommit = 1 -- 打开自动提交(默认)

start transaction -- 事务开始
...
rollback -- 回滚
...
commit -- 提交

savepoint -- 设置保存点
rollback to savepoint -- 回滚到保存点

-- ===索引===
primary key() -- 主键索引：唯一表示，不可重复
unique key() -- 唯一索引：避免重复列，可重复
key() -- 常规索引：默认的
fulltext -- 全文索引：

show index from `student_5393`
-- 创建索引
alter table ... 
add FULLTEXT index ` `( )

create index id_table_colum on tablename(colum)

-- 执行细节
explain
select * from student_5393

-- ===用户管理===

create user username identified by pwd;
SET PASSWORD = PASSWORD('');
set password for uasename = password('');
rename user uasename1 to username2;

-- 授权
grant all privileges on *.*;

show grant for username;
show grant for root@localhost;

revoke all privileges on *.* from username;
drop user username;

-- ===三大范式===
/*
1NF
原子性：每一列不可再分
*/

/*
2NF
  非主属性完全函数依赖于候选码：
每张表只描述一件事
每一列都和主键相关，不能和主键的某一部分相关
*/

/*
3NF
  消除传递依赖：
每一列都和主键直接相关，不能是间接相关
*/
```

