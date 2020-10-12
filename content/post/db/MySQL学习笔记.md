# MySQL学习笔记

<a name="8f613db0"></a>

摘抄：https://www.yuque.com/flipped-aurora/gqbcfk/ih6bw9

## DML语言


- **DML语言 : 数据操作语言**
- 数据库意义 : 数据存储、数据管理
- 用于操作数据库对象中所包含的数据
- 包括 :
   - INSERT (添加数据语句)
   - UPDATE (更新数据语句)
   - DELETE(删除外键)
<a name="Insert"></a>
### Insert
<a name="f2b0b493"></a>
#### 语法

- 添加单条数据
   - 语法: `INSERT INTO 表名[(字段1,字段2,字段3,...)] VALUES('值1','值2','值3')`
```sql
INSERT INTO `users`(`username`, `password`, `avatar`) VALUES ('a', 'b', 'c')
```

- 添加多条数据
   - 语法:`INSERT INTO 表名[(字段1,字段2,字段3,...)] VALUES('值1','值2','值3'), ('值1','值2','值3')...`
```sql
-- 例如
INSERT INTO `users`(`username`, `password`, `avatar`)
VALUES ('a', 'b', 'c'),('1', '2', '3')
```
<a name="1bbbb204"></a>
#### 注意事项

- 字段和字段之间使用英文逗号隔开
- 字段是可以省略的,但是后面的值必须要一一对应,不能少
<a name="Update"></a>
### Update
<a name="f2b0b493-1"></a>
#### 语法
```mysql
UPDATE 表名 SET column=value [,column=value2,...] WHERE [条件];
```

- column:要更改的数据列
- value 为修改后的数据 , 可以为变量 , 具体指 , 表达式或者嵌套的SELECT结果
- condition 为筛选条件 , 如不指定则修改该表的所有列数据
<a name="72a7df49"></a>
#### Where 条件子句

- 有条件地从表中筛选数据
| 运算符 | 含义 | 范围 | 结果 |
| :---: | :---: | :---: | :---: |
| = | 等于 | 5=6 | false |
| <> 或 != | 不等于 | 5!=6 | true |
| > | 大于 | 5>6 | false |
| < | 小于 | 5<6 | true |
| >= | 大于等于 | 5>=6 | false |
| <= | 小于等于 | 5<=6 | true |
| BETWEEN | 在某个范围之间 | BETWEEN 5 AND 10 |  |
| AND | 并且(&&) | 5 > 1 AND 1 > 2 | false |
| OR | 或(&#124;&#124;) | 5 > 1 OR 1 > 2 | true |

<a name="1bbbb204-1"></a>
#### 注意事项

- column是数据库的列,尽量带上``
- 条件,筛选的条件,如果没有指定,则会修改所有的列
- value,可以是一个具体的值,也可以是一个变量
- 多个设置的属性之间,使用英文逗号隔开
<a name="c4e75c77"></a>
### Delete &&Truncate
<a name="3fa25bf8"></a>
#### Delete语法

- `DELETE FROM 表名 WHERE [条件];`
```sql
  DELETE FROM `users` WHERE id=1;
```
<a name="811d397f"></a>
#### TRUNCATE语法

- `TRUNCATE [TABLE] table_name;`
```sql
  TRUNCATE TABLE `users`
```
> **Delete 与 Truncate 的区别**


- 相同:
   - 都能删除数据,不删除表结构,但是TRUNCATE速度更快
- 不同
   - 使用TRUNCATE TABLE 重新设置AUTO_INCREMENT计数器
   - 使用TRUNCATE TABLE 不会对事务有影响
> **Delete删除的问题** 重启数据库现象

- InnoDB 自增列会从1开始(存在内存中的,断电即失)
- MyISAM 继续从上一个自增量开始(存在文件中的,不会丢失)
<a name="4b56617d"></a>
## DQL语言

- (Data Query Language: 数据查询语言)
- 所有的查询操作都要用它 `Select`
- 简单的查询,复杂的查询都能做~
- 
- 使用频率最高的语句
> SELECT语法

- **[] 括号代表可选的 , {}括号代表必选得**
```sql
SELECT [ALL | DISTINCT]
	{* | table.* | [table.field1[as alias1][,table.field2[as alias2]][,...]]} 
FROM table_name [as table_alias]
    [left | right | inner join table_name2] -- 联合查询 [WHERE ...] -- 指定结果需满足的条件
    [GROUP BY ...] -- 指定结果按照哪几个字段来分组 [HAVING] -- 过滤分组的记录必须满足的次要条件
    [ORDER BY ...] -- 指定查询记录按一个或多个条件排序
    [LIMIT {[offset,]row_count | row_countOFFSET offset}]; 
    -- 指定查询的记录从哪条至哪条
```
<a name="a082878c"></a>
### 指定查询字段
```sql
-- 查询student表的全部数据
SELECT * FROM student

-- 查询指定字段
SELECT `StudentNo`, `StudentName` FROM student
```
> AS 字句作为别名

- 可给数据列取一个新别名
- 可给表取一个新别名
- 可吧经计算或者总结果用另一个新名称来代替



```sql
-- 为列去别名, AS可以省略
SELECT `StudentNo` AS 学号, `StudentName` AS 姓名 FROM studenS

-- 为表取别名
SELECT `StudentNo` As 学号, `StudentName` AS 姓名 FROM student AS s

-- 为查询结果取一个新名字, CONCAT()函数拼接字符串
SELECT CONCAT('姓名：', `StudentName` ) AS 新名字 FROM student
```


> DISTINCT 去重关键字



```sql
-- 查询result表所有的数据
SELECT * FROM result;

-- 查询result表的StudentNo字段
SELECT r.StudentNo From result as r;

-- DISTINCT 去除重复项 , (默认是ALL)
SELECT DISTINCT r.StudentNo FROM result as r;
```


> 使用表达式的列


<br />**数据库中的表达式:一般由文本值,列值,NULL,函数和操作符等组成**<br />应用场景:<br />

- SELECT语句返回的结果列中使用
- SELECT语句中的ORDER BY, HAVING等字句中使用
- DML语句中的Where条件语句中使用表达式



```sql
-- 查询系统版本(函数)
SELECT VERSION();
-- 计算7*7-1(表达式)
SELECT 7*7-1 AS 计算结果;
-- 查询自增的步长(变量)
SELECT @@auto_increment_increment

-- 学院考试成绩+1分查看
SELECT `StudentNo`, `StudentResult` + 1 AS 提分后 FROM `result` AS r
```


<a name="5719dc76"></a>
### Where条件语句

<br />作用: 用于检索数据表中符合条件的记录<br />
<br />搜索条件可由一个或多个逻辑表达式组成,结果一般为真或假<br />

> 逻辑操作符

| 操作符 | 语法 | 描述 |
| --- | --- | --- |
| AND 或 && | a AND b 或 a && b | 逻辑与，同时为真结果才为真 |
| OR 或 &#124;&#124; | a OR b 或 a &#124;&#124; b | 逻辑或，只要一个为真，则结果为真 |
| NOT 或 ! | NOT a 或 !a | 逻辑非，若操作数为假，则结果为真! |



```sql
-- 示例SQL语句
-- 查询result表的学号与学生成绩
SELECT `StudentNo`, `StudentResult` FROM result;
-- 查询成绩在95-100之间的
SELECT `StudentNo`, `StudentResult` FROM result WHERE StudentResult >=95 AND StudentResult <= 100;
-- AND也可以写成&&
SELECT `StudentNo`, `StudentResult` FROM result WHERE StudentResult >=95 && StudentResult <= 100;
-- 模糊查询(区间)
SELECT `StudentNo`, `StudentResult` FROM result WHERE StudentResult BETWEEN 95 AND 100;
-- 查询除了1000号同学的成绩
SELECT `StudentNo`, `StudentResult` FROM result WHERE NOT StudentNo = 1000;
-- NOT也可以写成!
SELECT `StudentNo`, `StudentResult` FROM result WHERE StudentNo != 1000;
```


> 模糊查询:比较操作符



- 数值数据类型的记录之就按才能进行算术运算;
- 相同数据类型的数据之间才能进行比较;
| **操作符名称** | **语法** | **描述** |
| --- | --- | --- |
| IS NULL | a IS NULL | 若操作符为NULL，则结果为真 |
| IS NOT NULL | a IS NOT NULL | 若操作符不为NULL，则结果为真 |
| BETWEEN | a BETWEEN b AND c | 若 a 范围在 b 与 c 之间，则结果为真 |
| **LIKE** | a LIKE b | SQL 模式匹配，若a匹配b，则结果为真 |
| **IN** | a IN (a1，a2，a3，......) | 若 a 等于 a1,a2..... 中的某一个，则结果为真 |



```sql
-- ======= like =======
-- like结合 %(代表0到任意个字符) _(一个字符)
-- 查询姓刘的同学
select `StudentNo`, `StudentName` from student where `StudentName` like '刘%';
-- 查询姓刘的同学，名字后面只有一个字的
select `StudentNo`, `StudentName` from student where `StudentName` like '刘_';
-- 查询姓刘的同学，名字后面只有两个字的
select `StudentNo`, `StudentName` from student where `StudentName` like '刘__';
-- 查询名字中间有嘉字的同学
select `StudentNo`, `StudentName` from student where `StudentName` like '%嘉%';

-- ======= in =======
-- 查询 1001，1002，1003号学员
select `StudentNo`, `StudentName` from student where StudentNo in (1001,1002,1003);
-- 查询 地址在北京，南京，河南洛阳的学生
select `StudentNo`, `StudentName`, `Address` from student where Address in ('北京', '南京', '河南洛阳');

-- ======= null/not null =======
-- 查询出生日期没有填写的同学
select `StudentName` from student where BornDate is null;
-- 查询出生日期填写了的同学
select `StudentName` from student where BornDate is not null;
-- 查询没有写家庭地址的同学(空字符串不等于null)
select `StudentName` from student where Address='' or Address is null;
```


<a name="d08fe29e"></a>
### 连接查询


> Join 对比



- `join on`:连接查询
- `where`:等值查询
| 操作符名称 | 描述 |
| --- | --- |
| inner join | 如果表中有至少一个匹配,则返回行 |
| right join | 左边中返回所有的值,即使右表中没有匹配数据 |
| left join | 右边中返回所有的值,即使左表中没有匹配数据 |



- 基础使用



```sql
-- ======= inner join =======
select s.StudentNo, StudentName, SubjectNo, StudentResult
from student as s
inner join result as r on s.StudentNo = r.StudentNo;

-- ======= right join =======
select s.StudentNo, StudentName, SubjectNo, StudentResult
from student as s
right join result as r on s.StudentNo=r.StudentNo

-- ======= left join =======
select s.StudentNo, StudentName, SubjectNo, StudentResult
from student as s
left join result as r on s.StudentNo=r.StudentNo
```


- 进阶使用



```sql
-- 查询缺考的同学(左连接应用场景)
select s.StudentNo, StudentName, SubjectNo, StudentResult
from student as s
left join result as r on s.StudentNo=r.StudentNo
where StudentResult is null;

-- 思考题:查询参加了考试的同学信息(学号,学生姓名,科目名,分数)
select s.StudentNo, s.StudentName, r.StudentResult, sub.SubjectName
from student as s
right join result as r on s.StudentNo = r.StudentNo
inner join subject as sub on r.SubjectNo = sub.SubjectNo
```


> 自连接(了解)



- 数据表与自身进行连接



```sql
-- 编写SQL语句,将栏目的父子关系呈现出来 (父栏目名称,子栏目名称)
-- 核心思想:把一张表看成两张一模一样的表,然后将这两张表连接查询(自连接)
select a.categoryName as '父栏目', b.categoryName as '子栏目'
from category as a, category as b
where a.categoryid = b.pid;

-- 思考题:查询参加了考试的同学信息(学号,学生姓名,科目名,分数)
select s.StudentNo, s.StudentName,s2.SubjectName,r.StudentResult
from student as s
inner join result r on s.StudentNo = r.StudentNo
inner join subject s2 on r.SubjectNo = s2.SubjectNo;

-- 查询学员及所属的年级(学号,学生姓名,年级名)
select s.StudentNo, s.StudentName,g.GradeName
from student as s
inner join grade g on s.GradeId = g.GradeID;

-- 查询科目及所属的年级(科目名称,年级名称)
select s.SubjectName, g.GradeName
from subject as s
inner join grade g on g.GradeID = s.GradeID;

-- 查询 数据库结构-1 的所有考试结果(学号 学生姓名 科目名称 成绩)
select s.StudentNo, s.StudentName,s2.SubjectName,r.StudentResult
from student as s
inner join subject s2 on s.GradeId = s2.GradeID
inner join result r on s.StudentNo = r.StudentNo
where s2.SubjectName = '数据库结构-1';
```


<a name="3862626c"></a>
### 分页


- 语法:`[LIMIT {[offset,] row_count | row_count OFFSET offset}];`
```sql
  -- 第N页 : limit (PageNo-1)*PageSize,
  -- PageSzie [PageNo:页码,PageSize:单页面显示条数]
```


```sql
-- 每页显示5条数据
select s.StudentNo, s.StudentName,s2.SubjectName,r.StudentResult
from student as s
inner join subject s2 on s.GradeId = s2.GradeID
inner join result r on s.StudentNo = r.StudentNo
where s2.SubjectName = '数据库结构-1'
order by r.StudentResult asc
limit 0,5;

-- 查询 JAVA第一学年 课程成绩前10名并且分数大于80的学生信息(学号,姓名,课程名,分数)
select s.StudentNo,s.StudentName,s2.SubjectName,r.StudentResult
from student as s
inner join result r on s.StudentNo = r.StudentNo
inner join subject s2 on r.SubjectNo = s2.SubjectNo
where s2.SubjectName = 'JAVA第一学年'
order by r.StudentResult desc
limit 0,10;
```


<a name="c360e994"></a>
### 排序


- 语法:`order by [字段名] [desc/asc]`
- desc:降序,由大到小
- asc:升序,由小到大



```sql
-- 查询 数据库结构-1 的所有考试结果(学号 学生姓名 科目名称 成绩) 降序
select s.StudentNo, s.StudentName,s2.SubjectName,r.StudentResult
from student as s
inner join subject s2 on s.GradeId = s2.GradeID
inner join result r on s.StudentNo = r.StudentNo
where s2.SubjectName = '数据库结构-1'
order by r.StudentResult desc ;

-- 查询 数据库结构-1 的所有考试结果(学号 学生姓名 科目名称 成绩) 升序
select s.StudentNo, s.StudentName,s2.SubjectName,r.StudentResult
from student as s
inner join subject s2 on s.GradeId = s2.GradeID
inner join result r on s.StudentNo = r.StudentNo
where s2.SubjectName = '数据库结构-1'
order by r.StudentResult asc ;
```


<a name="98250245"></a>
### 子查询


- 在查询语句中的WHERE条件子句中,又嵌套了另一个查询语句
- 嵌套查询可由多个子查询组成,求解的方式是由里及外
- 子查询返回的结果一般都是集合,故而建议使用IN关键字



```sql
-- 查询 数据库结构-1 的所有考试结果(学号，科目编号，成绩)，降序排列
-- 方式一
select r.StudentNo, s.SubjectName, r.StudentResult
from result as r
         inner join subject s on r.SubjectNo = s.SubjectNo
where s.SubjectName = '数据库结构-1'
order by r.StudentResult desc;

-- 方式二:使用子查询(执行顺序:由里及外)
select r.StudentNo, r.SubjectNo, r.StudentResult
from result as r
where SubjectNo = (
    select SubjectNo
    from subject
    where SubjectName = '数据库结构-1'
)
order by r.StudentResult desc;
-- 方法一:使用连接查询
-- 查询课程为 高等数学-2 且分数不小于80分的学生的学号和姓名

select s.StudentNo, s.StudentName, s2.SubjectName, r.StudentResult
from student as s
         inner join result r on s.StudentNo = r.StudentNo
         inner join subject s2 on r.SubjectNo = s2.SubjectNo
where s2.SubjectName = '高等数学-2'
  and r.StudentResult >= 80;

-- 方法二:使用连接查询+子查询
-- 分数不小于80分的学生的学号和姓名
select s.StudentNo, s.StudentName, s2.SubjectName, r.StudentResult
from student as s
         inner join result r on s.StudentNo = r.StudentNo
         inner join subject s2 on s.GradeId = s2.GradeID
where StudentResult >= 80;

-- 在上面SQL基础上,添加需求:课程为 高等数学-2
select s.StudentNo, s.StudentName, r.StudentResult
from student as s
         inner join result r on s.StudentNo = r.StudentNo
where StudentResult >= 80
  and r.SubjectNo = (
    select SubjectNo
    from subject
    where SubjectName = '高等数学-2'
);


-- 方法三:使用子查询
-- 分步写简单sql语句,然后将其嵌套起来
select s.StudentNo,s.StudentName
from student as s
where StudentNo in (
    select r.StudentNo
    from result as r
    where r.StudentResult >= 80
      AND r.SubjectNo = (
        select sub.SubjectNo
        from subject as sub
        where sub.SubjectName = '高等数学-2'
    )
);

/* 
练习题目:
	查 C语言-1 的前5名学生的成绩信息(学号,姓名,分数)
	使用子查询,查询郭靖同学所在的年级名称 
*/

-- C语言-1 的前5名学生的成绩信息(学号,姓名,分数)
select s.StudentNo, s.StudentName, r.StudentResult
from student as s
         inner join result r on s.StudentNo = r.StudentNo
order by r.StudentResult desc
limit 0,5
;

-- 使用子查询,查询周丹同学所在的年级名称
select g.GradeName
from grade as g
where GradeID = (
    select s.GradeId
    from student as s
    where StudentName = '周丹'
);
```


<a name="801894a7"></a>
### 分组和过滤


```sql
-- 查询不同课程的平均分,最高分,最低分 
-- 前提:根据不同的课程进行分组
select s.SubjectName, avg(r.StudentResult) as 平均分, max(r.StudentResult) as 最高分, min(r.StudentResult) as 最低分
from result as r
inner join subject s on r.SubjectNo = s.SubjectNo
group by r.SubjectNo
having  平均分 > 80
```


<a name="a8c21811"></a>
## MySQL函数


<a name="ca48b68b"></a>
### 常用函数


<a name="e0d12136"></a>
#### 数据函数


```sql
-- ======= 数学运算 =======
-- 绝对值
select abs(-8);
-- 向上取整
select ceiling(9.4);
-- 向下取整
select floor(9.4);
-- 返回一个0~1之间的整数
select rand();
-- 判断一个数的符号，负数返回1，正数返回1，0返回0
select sign(10);
```


<a name="ecb2f3ab"></a>
#### 字符串函数


```sql
-- ======= 字符串函数 =======
-- 字符串长度
select char_length('你猜我猜不猜你猜不猜');
-- 拼接字符串
select concat('a', 'b', 'c');
-- 查询，从某个位置开始替换某个长度
select insert('我爱Golang',1,1,'SliverHorn');
-- 小写字母
select lower('SliverHorn');
-- 大写字母
select UPPER('SliverHorn');
-- 返回第一次出现的索引
select instr('SliverHorn', 'r');
-- 替换出现的指定字符串
select replace('SliverHorn说SliverHorn', '说', '-->');
-- 返回制定本的子字符串(源字符串，截取的位置，截取的长度)
select substr('SliverHorn', 4, 6);
-- 字符串反转
select reverse('SliverHorn');

-- 查询姓周的同学,改成邹
select replace(StudentName, '周', '邹') as 名字
from student where StudentName LIKE '周%';
```


<a name="ac99dc14"></a>
#### 日期和时间函数


```sql
-- ======= 日期和时间函数 =======
-- 获取当前日期
select current_date();
-- 获取当前日期
select curdate();
-- 获取当前的时间
select now();
-- 获取本地时间
select localtime();
-- 获取系统时间
select sysdate();

-- 获取当前年份
select year(now());
-- 获取当前月份
select month(now());
-- 获取当前日
select day(now());
-- 获取当前时
select hour(now());
-- 获取当前的分
select minute(now());
-- 获取当前秒
select second(now());
```


<a name="d9ece9d8"></a>
#### 系统信息函数


```sql
-- ======= 系统信息函数 =======
-- 版本
select version();
-- 用户
select user();
```


<a name="b5b1571d"></a>
#### 聚合函数
| 函数名 | 描述 |
| --- | --- |
| **COUNT()** | 返回满足Select条件的记录总和数，如 select count(_) 【不建议使用 _，效率低】 |
| SUM() | 返回数字字段或表达式列作统计，返回一列的总和。 |
| AVG() | 通常为数值字段或表达列作统计，返回一列的平均值 |
| MAX() | 可以为数值字段，字符字段或表达式列作统计，返回最大的值。 |
| MIN() | 可以为数值字段，字符字段或表达式列作统计，返回最小的值。 |



```sql
-- ======= 聚合函数 =======
select count(StudentName) from student;
select count(*) from student;
select count(1) from student;
-- 从含义上讲，count(1) 与 count(*) 都表示对全部数据行的查询。
-- count(字段) 会统计该字段在表中出现的次数，忽略字段为null 的情况。即不统计字段为null 的记录。
-- count(*) 包括了所有的列，相当于行数，在统计结果的时候，包含字段为null 的记录;
-- count(1) 用1代表代码行，在统计结果的时候，包含字段为null 的记录 。

/* 很多人认为count(1)执行的效率会比count(*)高，原因是count(*)会存在全表扫描，而count(1) 可以针对一个字段进行查询。其实不然，count(1)和count(*)都会对全表进行扫描，统计所有记录的 条数，包括那些为null的记录，因此，它们的效率可以说是相差无几。而count(字段)则与前两者不 同，它会统计该字段不为null的记录条数。
下面它们之间的一些对比:
1)在表没有主键时，count(1)比count(*)快 2)有主键时，主键作为计算条件，count(主键)效率最高; 3)若表格只有一个字段，则count(*)效率较高。
*/
select sum(r.StudentResult) as 总和 from result as r;
select avg(r.StudentResult) as 平均分 from result as r;
select max(r.StudentResult) as 最高分 from result as r;
select min(r.StudentResult) as 最低分 from result as r;
```


<a name="9f82401d"></a>
## 事务


<a name="a4d3b02a"></a>
### 概述


> 什么是事务



- 事务就是将一组SQL语句放在同一批次内去执行
- 如果一个SQL出错,则改批次内的所有SQL都将被取消执行
- MySQL事务处理只支持InnoDB和BDB数据表类型



> 事务的ACID原则


<br />**原子性(Atomic)**<br />

- 整个事务中的所有操作,要么全部完成,要么全部失败,不可能停滞在中间的某个环节.事务在执行过后才能中发生错误,会被回滚(Rollback) 到事务开始前的状态,就像这个事务从来没有执行过一样.


<br />**一致性(Consist)**<br />

- 一个事务可以封装状态改变(除非它是一个只读的).事务必须始终保持系统处于一致的状态,不管在任何给定的时间并发事务有多少.如果事务是并发多个,系统也必须如同串行事务一样的操作,其主要特征是保护性和不变性(Presering an lnvarriant).
   - 以转账案例为例,假如有五个账户,每个账户余额是100元,那么五个账户总额是500元,如果在这个5个账户之间同事发生多个转账,无论并发多少个,比如在A与B账户之间转账5元,在C与D账户之间转账10元,在B与E之间转账15元.五个账户的总额也应该还是500,这就是保护性和不变性.


<br />**隔离性(isolated)**<br />

- 隔离状态执行事务,是它们好像是系统在给定时间内执行的唯一操作.如果有两个事务,运行在相同的时间内,执行相同的功能,事务的隔离性将确保每一事务在系统中认为只有该事务在使用系统.这种属性有时称为串行化,为了防止事务操作的混淆,必须串行化或者序列化请求,使得在同一时间仅有一个请求用于同一数据.


<br />**持久性(Durable)**<br />

- 在事务完成后,该事务对数据库所作的更改,持久的保存在数据库之中,并不会被回滚



> 事务实现:基本语法



```sql
-- mysql 是默认开启事务，自动提交
-- 关闭自动提交
set autocommit = 0;
-- 开启自动提交
set autocommit = 1;

-- 手动处理事务
set autocommit = 0;

-- 事务开启：标记一个事务的开始，从这个之后的sql都在同一个事务内
start transaction;


-- 提交：持久化到磁盘(成功)
commit;

-- 回滚：回到原来的样子(失败！)
rollback;

-- 事务结束开启自动提交
set autocommit = 1;

-- 设置一个事务的保存点
savepoint 保存点名称;

-- 回滚到保存点
rollback to savepoint 保存点名称;

-- 删除保存点
release savepoint 保存点名称;
```


> 事务实现:测试题目



```sql
/*
课堂测试题目
A在线买一款价格为500元商品,网上银行转账. A的银行卡余额为2000,然后给商家B支付500. 商家B一开始的银行卡余额为10000
创建数据库shop和创建表account并插入2条数据 */
CREATE DATABASE `shop`CHARACTER SET utf8 COLLATE utf8_general_ci; USE `shop`;
CREATE TABLE `account` (
	`id` INT(11) NOT NULL AUTO_INCREMENT, `name` VARCHAR(32) NOT NULL,
  `cash` DECIMAL(9,2) NOT NULL, PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8 INSERT INTO account (`name`,`cash`)
VALUES('A',2000.00),('B',10000.00)
-- 转账实现
SET autocommit = 0; -- 关闭自动提交
START TRANSACTION; -- 开始一个事务,标记事务的起始点 UPDATE account SET cash=cash-500 WHERE `name`='A'; UPDATE account SET cash=cash+500 WHERE `name`='B'; COMMIT; -- 提交事务
# rollback;
SET autocommit = 1; -- 恢复自动提交
```


<a name="b271e427"></a>
## 索引


> MySQL官方对索引的定义为：
> - **索引(Index)是帮助MySQL高效获取数据的数据结构。**
> - 提取句子主干，就可以得到索引的本质：索引是数据结构。



<a name="92f096e7"></a>
### 索引分类


> 索引的作用



- 提高查询速度
- 确保数据的唯一性
- 可以加速表和表之间的连接,实现表和表之间的参照完整性
- 使用分组和排序字句进行数据检索时,可以显著减少分组和排序的时间
- 全文检索字段进行搜索优化



> 索引的分类



- 主键索引(Primary Key)
- 唯一索引(Unique)
- 常规索引(Index)
- 全文索引(FullText)



<a name="7456f65f"></a>
### 主键索引

<br />主键:某一属性组能唯一标识一条记录<br />
<br />特点:<br />

- 最常见的索引类型
- 确保数据记录的唯一性
- 确定特定数据记录子在数据库中的位置



<a name="6e5fde10"></a>
### 唯一索引

<br />作用:避免同一个表中数据列中的值重复<br />
<br />与主键索引的区别是<br />

- 主键索引只能有一个
- 唯一索引可能有多个



<a name="5900f8b8"></a>
### 常规索引

<br />作用:快速定位特定数据<br />
<br />注意:<br />

- index和key关键字都可以设置常规索引
- 应加在查询找条件的字段
- 不宜添加太多常规索引,影响数据的插入,删除和修改操作



<a name="be13841f"></a>
## 全文索引

<br />作用:快速定位特定的数据<br />
<br />注意:<br />

- MySQL5.6版本开始支持InnoDB引擎的全文索引
- 只能用于CHAR , VARCHAR , TEXT数据列类型
- 适合大型数据集



<a name="4ece5adc"></a>
### 索引准则


- 索引不是越多越好
- 不要对经常变动的数据加索引
- 小数据量的表建议不要加索引
- 索引一般应加载查找条件的字段



<a name="ec1e58b7"></a>
### 索引的数据结构


```sql
-- 我们可以在创建上述索引的时候，为其指定索引类型，分两类 hash类型的索引:查询单条快，范围查询慢 btree类型的索引:b+树，层数越多，数据量指数级增长(我们就用它，因为innodb默认支持它)
-- 不同的存储引擎支持的索引类型也不一样
InnoDB 支持事务，支持行级别锁定，支持 B-tree、Full-text 等索引，不支持 Hash 索引; 
MyISAM 不支持事务，支持表级别锁定，支持 B-tree、Full-text 等索引，不支持 Hash 索引; 
Memory 不支持事务，支持表级别锁定，支持 B-tree、Hash 等索引，不支持 Full-text 索引; 
NDB 支持事务，支持行级别锁定，支持 Hash 索引，不支持 B-tree、Full-text 等索引; 
Archive 不支持事务，支持表级别锁定，不支持 B-tree、Hash、Full-text 等索引;
```

<br />关于索引的本质:[http://blog.codinglabs.org/articles/theory-of-mysql-index.html](http://blog.codinglabs.org/articles/theory-of-mysql-index.html)<br />

<a name="23bbdd59"></a>
## 权限管理


<a name="7d94de1c"></a>
### 用户管理


```sql
/* 用户和权限管理 */ ------------------ 用户信息表:mysql.user
-- 刷新权限
FLUSH PRIVILEGES
-- 增加用户 CREATE USER kuangshen IDENTIFIED BY '123456' CREATE USER 用户名 IDENTIFIED BY [PASSWORD] 密码(字符串)
- 必须拥有mysql数据库的全局CREATE USER权限，或拥有INSERT权限。 - 只能创建用户，不能赋予权限。
- 用户名，注意引号:如 'user_name'@'192.168.1.1'
- 密码也需引号，纯数字密码也要加引号
- 要在纯文本中指定密码，需忽略PASSWORD关键词。要把密码指定为由PASSWORD()函数返回的 混编值，需包含关键字PASSWORD
-- 重命名用户 RENAME USER kuangshen TO kuangshen2 RENAME USER old_user TO new_user
-- 设置密码
SET PASSWORD = PASSWORD('密码') -- 为当前用户设置密码
SET PASSWORD FOR 用户名 = PASSWORD('密码') -- 为指定用户设置密码

-- 删除用户 DROP USER kuangshen2 DROP USER 用户名
-- 分配权限/添加用户
GRANT 权限列表 ON 表名 TO 用户名 [IDENTIFIED BY [PASSWORD] 'password']
- all privileges 表示所有权限 - *.* 表示所有库的所有表
- 库名.表名 表示某库下面的某表
-- 查看权限 SHOW GRANTS FOR root@localhost; SHOW GRANTS FOR 用户名
-- 查看当前用户权限
SHOW GRANTS; 或 SHOW GRANTS FOR CURRENT_USER; 或 SHOW GRANTS FOR CURRENT_USER();
-- 撤消权限
REVOKE 权限列表 ON 表名 FROM 用户名
REVOKE ALL PRIVILEGES, GRANT OPTION FROM 用户名 -- 撤销所有权限
```


> 权限解释



```sql
-- 权限列表
ALL [PRIVILEGES] -- 设置除GRANT OPTION之外的所有简单权限
ALTER -- 允许使用ALTER TABLE
ALTER ROUTINE -- 更改或取消已存储的子程序
CREATE -- 允许使用CREATE TABLE
CREATE ROUTINE -- 创建已存储的子程序
CREATE TEMPORARY TABLES -- 允许使用CREATE TEMPORARY TABLE
CREATE USER -- 允许使用CREATE USER, DROP USER, RENAME USER和REVOKE ALL PRIVILEGES。
CREATE VIEW -- 允许使用CREATE VIEW
-- 允许使用DELETE -- 允许使用DROP TABLE
DELETE
DROP
EXECUTE
FILE
INDEX
INSERT
LOCK TABLES
PROCESS
REFERENCES
RELOAD
REPLICATION CLIENT -- 允许用户询问从属服务器或主服务器的地址
REPLICATION SLAVE -- 用于复制型从属服务器(从主服务器中读取二进制日志事件) SELECT -- 允许使用SELECT
SHOW DATABASES -- 显示所有数据库
-- 允许用户运行已存储的子程序
-- 允许使用SELECT...INTO OUTFILE和LOAD DATA INFILE
-- 允许使用CREATE INDEX和DROP INDEX -- 允许使用INSERT
-- 允许对您拥有SELECT权限的表使用LOCK TABLES -- 允许使用SHOW FULL PROCESSLIST
-- 未被实施 -- 允许使用FLUSH
-- 允许使用SHOW CREATE VIEW -- 允许使用mysqladmin shutdown
SHOW VIEW
SHUTDOWN
SUPER
mysqladmin debug命令;允许您连接(一次)，即使已达到max_connections。 UPDATE -- 允许使用UPDATE
USAGE -- “无权限”的同义词 GRANT OPTION -- 允许授予权限

/* 表维护 */
-- 分析和存储表的关键字分布
ANALYZE [LOCAL | NO_WRITE_TO_BINLOG] TABLE 表名 ...
-- 检查一个或多个表是否有错误
CHECK TABLE tbl_name [, tbl_name] ... [option] ...
option = {QUICK | FAST | MEDIUM | EXTENDED | CHANGED}
-- 整理数据文件的碎片
OPTIMIZE [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ...
```


<a name="5c907264"></a>
### MySQL备份

<br />数据库备份必要性<br />

- 保证重要数据不丢失
- 数据转移


<br />MySQL数据库备份方法<br />

- mysqldump备份工具
- 数据库管理工具,如SQLyog
- 直接拷贝数据库文件和相关配置文件


<br />**mysqldump客户端**<br />
<br />作用 :<br />

- 转储数据库
- 搜集数据库进行备份
- 将数据转移到另一个SQL服务器,不一定是MySQL服务器



```sql
-- 导出
1. 导出一张表 -- mysqldump -uroot -p123456 school student >D:/a.sql
	mysqldump -u用户名 -p密码 库名 表名 > 文件名(D:/a.sql)
2. 导出多张表 -- mysqldump -uroot -p123456 school student result >D:/a.sql
	mysqldump -u用户名 -p密码 库名 表1 表2 表3 > 文件名(D:/a.sql) 3. 导出所有表 -- mysqldump -uroot -p123456 school >D:/a.sql
	mysqldump -u用户名 -p密码 库名 > 文件名(D:/a.sql)
4. 导出一个库 -- mysqldump -uroot -p123456 -B school >D:/a.sql
	mysqldump -u用户名 -p密码 -B 库名 > 文件名(D:/a.sql) 可以-w携带备份条件

可以-w携带备份条件
-- 导入
1. 在登录mysql的情况下: -- source D:/a.sql
source 备份文件 2. 在不登录的情况下
mysql -u用户名 -p密码 库名 < 备份文件
```


<a name="50e9fed6"></a>
## 规范化数据库设计


<a name="11c838b4"></a>
### 为什么需要数据库设计

<br />**当数据库比较复杂时我们需要设计数据库**<br />
<br />**糟糕的数据库设计** **:**<br />

- 数据冗余,存储空间浪费
- 数据更新和插入的异常
- 程序性能差


<br />**良好的数据库设计** **:**<br />

- 节省数据的存储空间
- 能够保证数据的完整性
- 方便进行数据库应用系统的开发


<br />**软件项目开发周期中数据库设计** **:**<br />

- 需求分析阶段: 分析客户的业务和数据处理需求
- 概要设计阶段:设计数据库的E-R模型图 , 确认需求信息的正确和完整.


<br />**设计数据库步骤**<br />

- 收集信息
   - 与该系统有关人员进行交流 , 座谈 , 充分了解用户需求 , 理解数据库需要完成的任务.
- 标识实体[Entity]
   - 标识数据库要管理的关键对象或实体,实体一般是名词
- 标识每个实体需要存储的详细信息[Attribute]
- 标识实体之间的关系[Relationship]



<a name="a3cad0da"></a>
### 三大范式

<br />不合规范的表设计会导致的问题:<br />

- 信息重复
- 更新异常
- 插入异常
   - 无法正确表示信息
- 删除异常
- 丢失有效信息



> **三大范式**


<br />**第一范式 (1st NF)**<br />

- 第一范式的目标是确保每列的原子性,如果每列都是不可再分的最小数据单元,则满足第一范式


<br />**第二范式(2nd NF)**<br />

- 第二范式(2NF)是在第一范式(1NF)的基础上建立起来的，即满足第二范式(2NF)必须先满足第一 范式(1NF)。
- 第二范式要求每个表只描述一件事情


<br />**第三范式(3rd NF)**<br />

- 如果一个关系满足第二范式,并且除了主键以外的其他列都不传递依赖于主键列,则满足第三范式.
- 第三范式需要确保数据表中的每一列数据都和主键直接相关，而不能间接相关。


<br />**规范化和性能的关系**<br />

- 为满足某种商业目标 , 数据库性能比规范化数据库更重要
- 在数据规范化的同时 , 要综合考虑数据库的性能
- 通过在给定的表中添加额外的字段,以大量减少需要从中搜索信息所需的时间
- 通过在给定的表中插入计算列,以方便查询
