## MySQL-SQL优化

### 一、使用索引的原则。

##### **1）最左前缀匹配原则**

***mysql会一直向右匹配直到遇到范围查询（<,>.between,like）就停止匹配。所以要尽量把`“=”` 条件放在前面，把这些条件放在最后。***

```mysql
//不会用到b的索引
where a=1 and c>0 and b=2
//会用到b的索引
where a=1 and b=2 and c>0
```

##### 2）尽量选择区分度高的列作为索引,区分度的公式是`count(distinct col)/count(*)`，表示字段不重复的比例，比例越大我们扫描的记录数越少。

##### 3）**当取出的数据超过全表数据的`20%`时，不会使用索引。**

##### 4）使用like时要注意百分号的位置

##### 5）*尽量把`or`转换为 `union all`*

```mysql
//不使用索引：
select * from user where name='a' or age='20'

//使用索引：
select * from user where name='a' union all select * from user where age='20'
```

##### 6）*字段加函数不会使用索引*。所以尽量把函数放在数值上，

```mysql
//不使用索引：
explain ... where truncate(price) = 1
type  | possible_keys|rows
index |	NULL		| 568
//使用索引：
explain ... where price > 1 and price < 2
type  | possible_keys|rows
range | order_price	 |57

//拓展：truncate是截取函数
select truncate(7.536432,2)
mysql -> 7.53
```

##### **7）*如果使用数字作为字符，则数字需要加引号，否则mysql会自动在列上加数据类型转换函数。***

```mysql
//不使用索引
where mobile=18534874321
//使用索引
where mobile='18534874321'
```

##### 8）字段加运算符不会使用索引。所以尽量把运算放在数值上

```mysql
//不使用索引:
SELECT ACCOUNT_NAME, AMOUNT FROM TRANSACTION WHERE AMOUNT + 3000 >5000;
//使用索引:
SELECT ACCOUNT_NAME, AMOUNT FROM TRANSACTION WHERE AMOUNT > 2000 ;
```

##### 9）使用组合索引时，必须要包括第一个列。

```mysql
//例如
alter table test add index(a,b,c)：
//不使用索引：
where b=1, c=2
where b=1
where c=2
//使用索引：
where a=1, b=1, c=2
where a=1, b=1
where a=1, c=2
```

##### 10）***尽量避免使用 `is null` 或 `is not null`*** 

```mysql
不使用索引：
SELECT … FROM DEPARTMENT WHERE DEPT_CODE IS NOT NULL;
使用索引：
SELECT … FROM DEPARTMENT WHERE DEPT_CODE >0;
```

##### 11）不等于（!=）不会使用索引

```mysql
//不使用索引:
SELECT ACCOUNT_NAME FROM TRANSACTION WHERE AMOUNT !=0;
//使用索引:
SELECT ACCOUNT_NAME FROM TRANSACTION WHERE AMOUNT >0;
```

##### 12）ORDER BY 子句只在以下的条件下使用索引：

***ORDER BY中所有的列必须包含在相同的索引中并保持在索引中的排列顺序.***

***ORDER BY中不能既有ASC也有DESC***

```mysql
//例如:
alter table t1 add index(a,b);
alter table t1 add index(c);
//不使用索引：
select * from t1 order by a,c; //不在一个索引中
select * from t1 order by b; //没有出现组合索引的第一列
select * from t1 order by a asc, b desc; //混合ASC和DESC
```

***`select \* from t1 where a=1 order by c;` `where`和`order by`用的不是同一个索引，`where`使用索引，`order by`不使用。***

```mysql
//使用索引：
select * from t1 order by a,b;
select * from t1 order where a=1 order by b;
select * from t1 order where a=1 order by a,b;
select * from t1 order by a desc, b desc;
select * from t1 where c=1 order by c;
```

##### 13）索引不是越多越好。mysql需要资源来维护索引，任何数据的变更都会连带修改索引的值。所以，需要平衡考虑索引带来的查询加速和增删改减速。

#### 二、其他注意事项：

##### 1）尽量避免 `select *`

##### 2）尽量使用表连接（`join`）代替子查询 `select * from t1 where id in (select id from t2)`

##### 3）性能方面，表连接 > (`not`)`exists` > (`not`) `in`

3.1）用`exists`代替`in`

```mysql
低效:
SELECT *
FROM EMP
WHERE EMPNO > 0
AND DEPTNO IN (SELECT DEPTNO
FROM DEPT
WHERE LOC = ‘MELB’)
高效:
SELECT *
FROM EMP
WHERE EMPNO > 0
AND EXISTS (SELECT ‘X’
FROM DEPT
WHERE DEPT.DEPTNO = EMP.DEPTNO
AND LOC = ‘MELB’)
```
3.2）用not exists代替not in

```mysql
低效：
SELECT …
FROM EMP
WHERE DEPT_NO NOT IN (SELECT DEPT_NO
FROM DEPT
WHERE DEPT_CAT=’A’);
高效：
SELECT ….
FROM EMP E
WHERE NOT EXISTS (SELECT ‘X’
FROM DEPT D
WHERE D.DEPT_NO = E.DEPT_NO
AND DEPT_CAT = ‘A’);
```
3.3）用表连接代替exists

```mysql
exits：
SELECT ENAME
FROM EMP E
WHERE EXISTS (SELECT ‘X’
FROM DEPT
WHERE DEPT_NO = E.DEPT_NO
AND DEPT_CAT = ‘A’);
表连接：
SELECT ENAME
FROM DEPT D,EMP E
WHERE E.DEPT_NO = D.DEPT_NO
AND DEPT_CAT = ‘A’ ;
```

##### 4）清除不必要的排序

```mysql
低效：
select count(*) from (select * from user where id > 40 order by id);
高效：
select count(*) from (select * from user where id > 40);
```

##### 5）避免使用`HAVING`子句，`HAVING`只会在检索出所有记录之后才对结果集进行过滤。这个处理需要排序，总计等操作，如果能通过`where`子句限制记录的数目那就能减少这方面的开销。

```mysql
低效：
select * from user group by id having id > 40;
高效：
select * from user where id > 40 group by id;
```

##### 6）除非确实需要去掉重复的行，否则尽量使用`union all` 而不是`union` 因为`union` 会自带`distinct` 操作，代价很大。

### 学会使用执行计划`explain`查看sql性能

##### 1）在`select`之前加上`explain`即可。

##### 2）`explain`执行计划各个字段的意义：

```properties
1	id:语句的执行顺序
2  	select_type:主要有以下几个类型
2.1	simple:表示简单的select，没有union和子查询
2.2	primary:最外层的select。在有子查询的语句中，最外面的select查询就是primary
2.3	union:union语句的第二个或者说是后面那一个
2.4	union result:union的结果
2.5	subquery:子查询中的第一个 select
3	table:涉及的表
4	type:连接类型。主要有以下几个：（重点查看）
4.1	const:说明只有一个匹配行，使用了主键或唯一性索引。通常是最优化的情况。
4.2	eq_ref，ref，ref_or_null:表示走了简单索引
4.3	index_merge:表示使用了多个索引的组合
4.4	range:表示通过索引取出了一个范围内的值。例如where a in (1,2)
4.5	index:表示对索引进行了全扫描
4.6	ALL:表示全表扫描
#注意：以上类型从4.1到4.6性能越来越差。
5	possible_keys:可供使用的索引
6	keys:实际使用的索引
7	key_gen:索引长度
8	ref:显示使用哪个咧或常数与索引一起从表中选择行
9	rows:读取的行数。（重点查看）
10	Extra:备注
```

