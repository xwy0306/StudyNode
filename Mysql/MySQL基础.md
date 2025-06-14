# 基本的命令行操作
```sql
mysql -u -p --连接数据库

update mysql.user set authentication_string=password('123456') where user='root' and Host = 'localhost'; --修改用户密码

flush privileges; --刷新权限

——————————————————————————————
--所有语句都使用“;”结尾。
show databases; --查看所有数据库
use database; --切换数据库命令
-----------------------------
show tables; --查询所有的表
describe tablename; --查看表的信息

______________________________
create databases dataname; --创建一个数据库

exit; --退出链接

```
#  数据库
操作数据库>操作数据库中的表>操作数据库中表的数据
# 操作数据库
#### 创建数据库

```sql
create database [if not exists] name
```
#### 删除数据库
```sql
drop database [if exists] name
```
#### 使用数据库
```sql
--如果字段名或者表名是特殊字符，就要用符号"`"
use `name`
```
#### 查看数据库
```sql
show databases --查看所有的数据库
```
## 数据库的列类型
> 数值

- tinyint    十分小的数据     1个字节
- smallint  比较小的数据   2个字节
- mediumint   中等大小数据   3个字节
- int           标准的整数        4个字节
- bigint          较大的数据         8个字节
-  float            浮点数            4个字节
- double         浮点数             8个字节
- decimal         字符串形式的浮点数   金融计算的时候使用
>字符串  

- char 字符串的固定大小 0-255
- **varchar 可变字符串 0-65535**
- tinytext 微型文本 2^8-1
- **text 文本串 2^16-1** 保存大文本
>时间和日期

- date YYYY-MM-DD 日期
- time HH :mm: ss 时间格式
- datetime YYYY-MM-DD HH: mm: ss 完整时间格式
- timestamp 时间戳 1970.1.1到现在的毫秒数
- year 年份表示

>null

- 没有值，未知
- ==注意，不要使用NULL进行运算

## 数据库的字段属性
==Unsigned
- 无符号的整数
- 声明了该列不能声明为负数
==zerofill==
- 0填充的
- 不足的位数使用0填充
==自增
- 通常理解为自增，自动在上一条的基础上+1
- 通常用来设置唯一的主键，必须是整数类型
- 可以自定义设计主键自增的起始值和步长
==非空
- 假设设置为not null，如果不赋值，就会报错
- null，如果不填写，就是null
==默认==
- 设置默认的值

## 创建数据库表
```sql
create table if not exists `tablename` (
	`id` int(4) not null auto_increment comment 'id',
	`name` varchar(30) not null default '匿名' comment '姓名',
	`pwd` varchar(20) not null default '123456' comment '密码',
	`sex` varchar(2) not null default '女' comment '性别',
	`birthday` datetime default null comment '出生日期',
	`address` varchar(100) default null comment '家庭住址',
	`email` varchar(50) default null comment '邮件',
	primary key(`id`)
) engine=innodb default charset=utf8
```
## 数据库引擎
==Innodb
- 默认使用
==myisam
- 早些年使用

|       | MYISAM | INNODB        |
| ----- | ------ | ------------- |
| 事务支持  | 不支持    | 支持            |
| 数据行锁定 | 不支持    | 支持            |
| 外键约束  | 不支持    | 支持            |
| 全文索引  | 支持     | 不支持           |
| 表空间大小 | 较小     | 较大，约为myisam两倍 |
常规使用操作：
- MYISAM 节约空间，速度较快
- INNODB 安全性较高，支持事务处理，多表多用户操作

>在物理空间存在的位置

所有的数据库文件都会在data文件下
本质还是文件的存储！

MySQL引擎在物理文件的区别
- InnoDB在数据库表中只有一个*.frm文件，以及上级目录下的ibdata文件。
- MYISAM对应的文件
	- .frm 文件结尾的
	-  *.MYD 数据文件(data)
	- *.MYI 索引文件

> 设置数据库的编码方式

```sql
charset=utf8
```
不设置的话，会是mysql默认的编码，并不支持中文。
## 修改删除表

>修改

```sql
alter table tablename rename as newname --修改表名

--增加表的字段
alter table tablename add [字段名] [列类型]

--修改表的字段（重命名，修改表的约束）
alter table tablename modify [字段名] [需要修改的约束]
alter table tablename change [旧字段名] [新字段名] [字段属性]

--删除表的字段
alter table tablename drop [字段名]

```

>删除表

```sql
drop table if exists tablename
```

==所有的创建和删除字段操作尽量加上判断，以免报错

# MySQL数据管理

## 外键
- 定义外键key
- 给这个外键添加约束（执行引用）

> 方式1在创建表的时候增加外键

删除有外键关系的表的时候，必须要先删除引用别人的表(从表)，再删除本表

> 创建表的时候没有外键关系

```sql
alter table tablename
add constraint `FK_字段名` foreign key(`字段名`) references `表名`(`列名1`);
```

## DML语言
- insert
- update
- delete

## 添加
```sql
insert into 表名 ([字段1，字段2，字段。。。]) values (vale1,value2,...),(vale1,value2,...);
```

## 修改
```sql
update tablename set column1_name = new_value1,column2_name=value2 where [condition];
```

## 删除
```sql
delete from table_name where [condition];

--完全清空一个表，索引和结构不变
truncate table_name;
```

>delete和truncate区别

- 相同点：都能删除数据，都不会删除表结构。
- 不同点：
	- TRUNCATE：重新设置自增列，计数器会归零
	- truncate：不会影响事务
# DQL查询数据
- 所有查询操作都要用select
- 简单查询复杂查询也要用
- ==数据库最核心的==
- 使用频率最高
## 查询字段

```sql
--查询全部信息
selet * from table_name where [condition];

--查询特定列信息
select column1,column2,.. from table_name where [condition];

--增加别名
select column_name as new_name from table_name where [condition];

--函数 Concat(a,b) 拼接字符串
select concat(column1,column2) as new_columns from table where [condition];

--去除重复信息
select distinct distinct_columns from table_name where [condition];
```

## where 条件语句
作用：检索符合条件的值

> 逻辑运算符

| 运算发 | 语法      | 描述             |
| --- | ------- | -------------- |
| and | a and b | 逻辑与，两个都为真，结果为真 |
| or  | a or b  | 逻辑或，一个为真，结果为真  |
| Not | not a   | 逻辑非，真为假，假为真    |

>模糊查询：比较运算符


| 运算发         | 语法                | 描述                   |
| ----------- | ----------------- | -------------------- |
| is null     | a is null         | 如果操作符为null，则结果为真     |
| is not null | a is not null     | 如果操作符为not null，则结果为真 |
| between     | a between b and c | 如果a在b和c之间，为真         |
| like        | a like b          | SQL匹配，如果a能匹配到b结果为真   |
| in          | a in (a1,a2..)    | a在原表中则为真             |
|             |                   |                      |
## 连表查询

>join对比

![[Pasted image 20250612191119.png]]

| 操作         | 描述                     |
| ---------- | ---------------------- |
| inner join | 如果表中至少有一个匹配，则返回        |
| left join  | 即使右表中没有匹配，也会从左表中返回所有的值 |
| right join | 会从右表中返回所有的值，即使没有匹配     |

>自连接

==自己的表和自己的表连接，核心：一张表拆分为两张一样的表即可==
```sql
SELECT a.【学生姓名】

FROM 【成绩表】 AS a, 【成绩表】 AS b

WHERE a.【主键ID】 = b.【主键ID】

AND a.【成绩】 >= b.【成绩】

AND a.【课程名称】 = '语文'

AND b.【课程名称】 = '数学';
```

查询了成绩表中语文成绩大于或等于数学成绩的学生姓名。通过为成绩表指定两个不同的别名_a_和_b_，我们可以在WHERE子句中使用这些别名来指定连接条件和比较逻辑。
==注意事项：== 
- 自连接可以是内连接也可以是外连接
- 在自连接中，必须使用别名来区分表的不同实例，否则会导致语法错误。
- 自连接的查询逻辑可能会比较复杂，需要仔细构造_WHERE_子句以确保正确的比较和关联。

## 分页和排序
### limit

```sql
limit a,b   --a为起始值，b为页面大小
```

> limit (n-1)\*pageSize, pageSize

n:为总页数， pageSize：为页面大小
### order by
>降序排

order by [column] desc

>升序

order by [column] asc

## 子查询

本质：在where语句中嵌套一个子查询

# MySql常用函数
>数学函数

- `ABS(x)`   返回x的绝对值
- `BIN(x)`   返回x的二进制（OCT返回八进制，HEX返回十六进制）
- `CEILING(x)`   返回大于x的最小整数值
- `EXP(x)`   返回值e（自然对数的底）的x次方
- `FLOOR(x)`   返回小于x的最大整数值
- `GREATEST(x1,x2,...,xn)`返回集合中最大的值
- `LEAST(x1,x2,...,xn)`      返回集合中最小的值
- `LN(x)`                    返回x的自然对数
- `LOG(x,y)`返回x的以y为底的对数
- `MOD(x,y)`                 返回x/y的模（余数）
- `PI()`返回pi的值（圆周率）
- `RAND()`返回０到１内的随机值,可以通过提供一个参数(种子)使RAND()随机数生成器生成一个指定的值。
- `ROUND(x,y)`返回参数x的四舍五入的有y位小数的值
- `SIGN(x)` 返回代表数字x的符号的值
- `SQRT(x)` 返回一个数的平方根
- `TRUNCATE(x,y)`            返回数字x截短为y位小数的结果

>聚合函数(常用于GROUP BY从句的SELECT查询中)

- `AVG(col)`返回指定列的平均值
- `COUNT(col)`返回指定列中非NULL值的个数
- `MIN(col)`返回指定列的最小值
- `MAX(col)`返回指定列的最大值
- `SUM(col)`返回指定列的所有值之和
- `GROUP_CONCAT(col)` 返回由属于一组的列值连接组合而成的结果

>字符串函数

- `ASCII(char)`返回字符的ASCII码值
- `BIT_LENGTH(str)`返回字符串的比特长度
- `CONCAT(s1,s2...,sn)`将s1,s2...,sn连接成字符串
- `CONCAT_WS(sep,s1,s2...,sn)`将s1,s2...,sn连接成字符串，并用sep字符间隔
- `INSERT(str,x,y,instr)` 将字符串str从第x位置开始，y个字符长的子串替换为字符串instr，返回结果
- `FIND_IN_SET(str,list)`分析逗号分隔的list列表，如果发现str，返回str在list中的位置
- `LCASE(str)或LOWER(str)` 返回将字符串str中所有字符改变为小写后的结果
- `LEFT(str,x)`返回字符串str中最左边的x个字符
- `LENGTH(s)`返回字符串str中的字符数
- `LTRIM(str)` 从字符串str中切掉开头的空格
- `POSITION(substr,str)` 返回子串substr在字符串str中第一次出现的位置
- `QUOTE(str)` 用反斜杠转义str中的单引号
- `REPEAT(str,srchstr,rplcstr)`返回字符串str重复x次的结果
- `REVERSE(str)` 返回颠倒字符串str的结果
- `RIGHT(str,x)` 返回字符串str中最右边的x个字符
- `RTRIM(str)` 返回字符串str尾部的空格
- `STRCMP(s1,s2)`比较字符串s1和s2
- `TRIM(str)`去除字符串首部和尾部的所有空格
- `UCASE(str)`或`UPPER(str)` 返回将字符串str中所有字符转变为大写后的结果

>日期和时间函数

- `CURDATE()`或`CURRENT_DATE()` 返回当前的日期
- `CURTIME()`或`CURRENT_TIME()` 返回当前的时间
- `DATE_ADD(date,INTERVAL int keyword)`返回日期date加上间隔时间int的结果(int必须按照关键字进行格式化),如：`SELECTDATE_ADD(CURRENT_DATE,INTERVAL 6 MONTH);`
- `DATE_FORMAT(date,fmt)`  依照指定的fmt格式格式化日期date值
- `DATE_SUB(date,INTERVAL int keyword)`返回日期date加上间隔时间int的结果(int必须按照关键字进行格式化),如：`SELECTDATE_SUB(CURRENT_DATE,INTERVAL 6 MONTH);`
- `DAYOFWEEK(date)`   返回date所代表的一星期中的第几天(1~7)
- `DAYOFMONTH(date)`  返回date是一个月的第几天(1~31)
- `DAYOFYEAR(date)`   返回date是一年的第几天(1~366)
- `DAYNAME(date)`   返回date的星期名，如：`SELECT DAYNAME(CURRENT_DATE);`
- `FROM_UNIXTIME(ts,fmt)`  根据指定的fmt格式，格式化UNIX时间戳ts
- `HOUR(time)`   返回time的小时值(0~23)
- `MINUTE(time)`   返回time的分钟值(0~59)
- `MONTH(date)`   返回date的月份值(1~12)
- `MONTHNAME(date)`   返回date的月份名，如：`SELECT MONTHNAME(CURRENT_DATE);`
- `NOW()`    返回当前的日期和时间
- `QUARTER(date)`   返回date在一年中的季度(1~4)，如`SELECT QUARTER(CURRENT_DATE);`
- `WEEK(date)`   返回日期date为一年中第几周(0~53)
- `YEAR(date)`   返回日期date的年份(1000~9999)

## 事务
## 什么是事务
==要么都成功，要么都失败== 

将一组sql放在一个批次中去执行~

>事务原则：ACID原则 原子性，一致性，隔离性，持久性（脏读，幻读。。。）

**原子性（Atomically）**
要么都成功，要么都失败。
**一致性（Consistency）**
事务前后的数据完成性要保持一致。
**持久性（Duarability）**
事务一旦提交则不可以，被持久化到数据库中！
**隔离性（Isolation）**
事务的隔离性是多个用户访问数据库时，数据库为每一个用户开启事务，不能被其他事务操作数据所干扰，事务之间要相互隔离。

>隔离所导致的一些问题：

**脏读**
指一个事务读取了另一个事务未提交的数据
**不可重复读**
在一个事务内读取表中的某一行数据，多次读取的结果不同（这个不一定是错误，只是某个场合不对）
**虚读\幻读**
是指在一个事务内读取到了别的事务插入的数据，导致前后读取不一致。