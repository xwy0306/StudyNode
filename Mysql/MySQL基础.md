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