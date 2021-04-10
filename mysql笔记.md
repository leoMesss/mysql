# 基本mysql命令

查询整个表单

```sql
select * from XX;
```


查询表单中某列

```
select XX from XX;
```


查询表单结构

```
desc(describe) XX;
```


查看数据库版本号

```
select version();
```


查看当前数据库

```
select database();
```


终止一条命令的输入

```
'\c'
```



# 简单查询

​			

```
select 字段名 from 表名;
```

查询字段:

```
1.select a,b,c,d,e from XX;
2.select * from XX;(效率低，可读性差，实际开发中不使用)
```

查多个字段:

```
select a,b,c,d,e from XX;用逗号隔开
```

给查询的列起别名:

```
select XX1 as YY from XX2;
```

将XX2表单中XX1字段起别名为YY，注意只是在显示时XX1更改为YY，表单实际上仍未XX1而不是YY，as可以去掉select XX1 YY from XX2;有空格的话加单引号	或（双引号尽量不要用）

```
 select XX1 'Y Y' from XX2
```

字段可以使用数学表达式

```
select ename,sal*12 from emp 
```

# 条件查询

select
			字段1，字段2，字段3
		from 
			表名
		where
			条件
		有哪些条件：

```
=
!= <>（也表示不等）
<
>
<=
>=
between ... and ... 两个值之间，等同于>= and <=
is null 为空 is not null不为空
and 并且 
示例：select empno,ename,job,sal from emp where job='MANAGER' and sal>5000;
```

or 并且	

```
select * from emp where sal>2500 and deptno=10 or deptno =20;
```

and 和or同时出现，and优先级较高，如果or先执行，需要交小括号



in 包含，等效于多个or

```
select empno,ename,job from emp where job in ('MANAGER','SALESMAN')
```

等效于：

```
select empno,ename,job from emp where job='MANAGER' or job='SALESMAN'
```

not in 不包含



like 模糊查询，支持%或下划线匹配
			%匹配任意多个字符
			下划线，任意一个字符

```
select ename from emp where ename like '%o%';找出含'o'的
select ename from emp where ename like 'K%';以K开头的
select ename from emp where ename like '_A%';第二个字母是A的
select ename from emp where ename like '__R%';第三个字母是R的
```

# 排序

```
select ename,sal from emp order by sal asc;ascending升序排列，不写也行，默认升序
select enamel，sal from emp order by sal desc;Descending降序排列
select enamel，sal from emp order by sal asc,ename asc;先按sal排序，如果有相同再按ename排序
select ename,sal from emp order by 2；以第二列排序，一般不这么写，这样写不健壮
```

# 数据处理函数

## 单行处理函数

单行处理函数(一行一行地处理，处理完后数据行数不变):

```
lower:转换小写
	 	select lower(ename) as ename from emp
upper:转换大写
	 	select upper(name) as ename from t_student
substr:取子串
	 	select substr(ename,1,1) as ename from emp
concat:字符串拼接
	 	select concat(ename,empon) as result from emp;
length:取长度
trim: 去空格
	 	str_to_date:change string into date
ate_format:格式化日期
format:设置千分位
```

case... when..then..else..end
	 		 当员工的工作岗位时MANAGER的时候，工资上调10%，当岗位是SALESMAN时，工资上调50%，其它正常

```
select ename,job,sal from emp;
select
	 	ename,job,sal as oldsal,(case job when 'MANAGER' then sal*1.1 when'SALESMAN' then sal*1.5 else sal end) as newsal 
from 
	 	emp;
```

数字处理

```
round:四舍五入
round(1236.567,1) 保留一位小数 1236.6
round(1236.567,2) 保留两位小数 1236.57
round(1236.567,-1) 保留到十位 1240
round(1236.567，-2) 保留到百位 1200
rand():生成0~1随机数
ifnull:可以将null转化成具体的值
	 select ename,(sal+ifnull(comm,0))*12 as yearsal from emp;
```

## 分组函数(多行处理函数)

分组函数(多行处理函数)(多个输入对应一个输出):

​			多行处理函数的特点:输入多行，最终输出一行.

5个:

```
count 计数
sum 求和
avg 平均值
max 最大值
min 最小值
```

注意:
	 	分组函数在使用时必须先分组，然后才能使用
	 	如果没有分组，则整张表默认为一组
找出最低工资:

```
select min(sal) from emp;
```

找出最高工资:

```
select max(sal)	from emp;
```

计算工资和: 

```
select sum(sal) from emp;
```

计算平均工资:

```
select avg(sal) from emp;
```

计算员工数量:

```
select count(ename) from emp;
```

分组函数在计算时要注意哪些
	 	1.分组函数自动忽略NULL，你不需要对NULL进行处理
	 	2.分组函数中count(*)和count(具体字段)
	 			count(具体字段)：表示统计该字段下不为NULL的元素的总数
	 			count(*):统计表单的行数。(只要有一行数据count++)每一行记录至少有一列不为NULL,否则这一行不为有效数据

```
select
	 ...
from
	 ...
where
	 ...
group by
	 ...
order by
	 ...
```

注意执行顺序为23451
分组函数是要先分组再执行的，也就是说在where中加分组函数没有意义

### group by

​	 			按某一字段(列)分组，比如按职位分组，把MANAGER分到一组，SALER分到一组，然后再使用分组函数

```
select ename,job,sum(sal) from emp group by job;
```

以上语句在mysql中可以执行，但没有意义，因为我们按job分组，再列出每组的ename没有意义，因为对于一组而言是没有ename这个属性的，显示的ename也是随机的一个ename，而且在oracle中执行会报错。

找出每个部门，不同工作岗位的最高薪资
		 	技巧:两个字段联合成一个字段看.(两个字段联合分组）

```
select
	deptno,job,max(sal)
from 
	emp
group by
	deptno,job;
```

### having

使用having对分完组后的数据进一步过滤
		 	

```
select
	deptno,max(sal)
from
	emp
group by
	deptno
having
	max(sal)>3000
```

表示按deptno分组，取出每组中最大sal大于3000的但这样效率比较低，我们一般写成:
		 	

```
select
	deptno,max(sal)
from
	emp
where
	sal>3000
group by
	deptno;
```

注意，having不能单独使用，必须和group by在一起使用，有where 用where ，实在没办法用having
**一个必须用having的例子**
找出每个部门的平均薪资高于2500

```
select
	deptno,avg(sal)
from
	emp
group by
	deptno
having
	avg(sal)>2500
```

我们可以看出，按分组函数的结果进行条件查询的，只能用having

### 总结

​		关键字顺序:
​		

```
select 
		...
from
		...
where
		...
group by
		...
having
		...
order by
		...
```

执行顺序？
1.from
2.where
3.group by
4.having
5.order by
找出每个岗位的平均薪资，要求显示平均薪资大于1500，除MANAGER岗位职位，要求按照平均薪资降序排列
	

```
select
		job,avg(sal)
from
		emp
where
		job!='MANAGER'
group by
		deptno
having
		avg(sal)>2500
order by
		sal desc;
```

# distinct

​	distinct只能出现在所有字段前方

```
select distinct job,deptno from emp;
```

​	对job和deptno联合去重，即对job和deptno都相同的数据去重

# 连接查询

## 	内连接

​		等值连接
​		非等值连接
​		自连接(将自己和自己连接，以解决自身和自身比对的问题)

```
select
	a.ename,b.dname
from
	emp a
join
	emp b
on 
	a.ename=b.dname
```

## 	外连接

左外连接(左连接)
右外连接(右连接)

//避免笛卡尔积现象，加条件

```
select
	ename,dname
from
	emp,dept
where
	emp.deptno=dept.deptno
```

//直接指明表名，加快查询速度

```
select
		emp.ename,dept.dname
	from
		emp,dept
	where
		emp.deptno=dept.deptno
```

//表起别名,很重要。效率问题

```
select
	e.ename,d.dname
from
	emp e,dept d
where
	e.deptno=d.deptno
```

注意表的连接是一个a*b*c*...的规模，尽量减少表的连接
	SQL92语法:
		

```
select
			e.ename,d.dname
from
			emp e,dept d
where
			e.deptno=d.deptno
```

​	SQL99语法:
​		

```
select
			e.ename,d.dname
from
			emp e
join
			dept d
on
			e.deptno=d.deptno
```

​	二者实现同一种功能，查询每个员工所在部门名称，显示员工名和部门名，99语法结构更清晰，倡导连接和条件(where)分离.
​	SQL99语法:

```
select
			...
from
			a
join
			b
on
			a和b的连接条件
where
			筛选条件
```

## 总结

**内连接:**
		完成能够匹配上这个条件的数据查询出来
**外连接:**
		将其中一个表完全展示出来，作为主表，不管另一张表有没有与之匹配值，都将其展示出来

```
select
		e.ename,d.dname
from
		emp e right join dept d
on
		e.deptno=d.deptno
```

​	内连接没有主次关系，外连接有主次关系，主表由join前面的关键字决定，join前是left就是join左边为主表，反之right则是右边
实际上
​	内连接和外连接都有关键字 即
​		**innner join**
​	和
​		**outer join**
​	但是我们一般直接省略
查询每个员工的上级领导，要求显示所有员工的名字和领导名

```
select
		a.ename,b.ename
	from
		emp a
	left outer join 
		emp
	on
		a.mgr=b.mgr
```

## 多张表的连接

​		

```
select
		...
from
		a
join
		b
on
		a和b的连接条件
join
		c
on
		a和c的连接条件
right join
		d
on
		a和d的连接条件
```

## 子查询

select语句中嵌套select，被嵌套的select语句称为子查询

### 子查询出现在哪里

```
select
		...(select)
from
		...(select)
where
		...(select)
```

### where 中的子查询

​		找出比最低工资高的员工姓名和工资

```
select
		ename,sal
from
		emp
where
		sal>(select avg(sal) from emp)
```

### from 中的子查询

找出每个岗位平均工资的级别

```
select
		t,s.grade
from
		(select job,avg(sal) as avgsal from emp group by job) t
join 
		salgrade as s
on
		t.avgsal between s.losal and s.hisal_
```

# Union合并查询结果集

​	案例：查询工作岗位是MANAGER和SALESMAN的员工

```sql
select ename ,job from emp where job='MANAGER' or job='SALESMAN';
```

```sql
select ename,job from emp where job in('MANAGER','SALESMAN');
```

若采用union进行匹配

```sql
select ename ,job from emp where job='MANAGER'
union
select ename ,job from emp where job='SALESMAN'
```

union 匹配效率更高，对表连接来说，每连接一次新表，则匹配的次数满足笛卡尔积，成倍地翻，但是union可以减少匹配的次数。在减少匹配次数的情况下，还可以完成两个结果集的拼接

举例：

a连接b连接c

a 10条记录

b 10条记录

c 10条记录

匹配次数是：1000

a连接b一个结果：10*10=100

a连接c一个结果：10*10=100

使用union的话是：100次+100次=200次（union把乘法运算变成了加法运算）

注意事项

//错误，union合并时要求两个结果集的列数相同

```sql
select ename,job from emp where job ='MANAGER'
union
select ename from emp where job='SALESMAN'
```

//MySQL可以，但是oracle不行

```sql
select ename,job from emp where job ='MANAGER'
union
select ename,sal from emp where job='SALESMAN'
```

# limit

## 	 limit作用

​		将查询结果集的一部份取出来，通常使用在分页查询中，分页的作用是提高用户体验，因为一次全部都查出来，用户体验差，可以一页一页翻页看

## 	limit用法

标准结构：limit startIndex（起始位置注意从0开始），length(取的数据个数)

缺省用法:limit 5，表示取前五条

```sql
select ename,sal 
from
	emp
order by
	sal desc
limit 5;(limit 0,5)
```

例子 分页查询

```java
@代码不可运行
public satic void main(String[],args){
	int pagenum=5 //第5页
    int pageSize=10 //每页显示10条
    
    int startIndex=(pagenum-1)*pageSize;
    String sql="select ...limit"+startIndex+","+pageSize
}
```



关于DQL语句的总结

```sql
select
	...
from
	...
where
	...
group by
	...
having
	...
order by
	...
limit
	,,,
```



# 表的创建

​	create table 表名(字段名1 数据类型，字段名2 数据类型，字段名3 数据类型);

create table 表名（

字段名1 数据类型

字段名2 数据类型

字段名3 数据类型

);

表名建议以 t_ 或 tbl_ 开始，可读性强，见名知意

字段名:见名知意

表名和字段名都属于标识符

# mysql数据类型

varchar

​	可变长度的字符串

​	比较智能，节省空间

​	会根据实际数据长度动态分配空间

​	缺点是慢

char

​	注意：char(1)中的数字表示长度，一个汉字“中”也表示一个长度

​	不可变长度的字符串

一般固定长度如性别用char，不固定如姓名用varchar

int

​	整型

bigint 

​	长整型

float

​	单精度浮点型数据

double

​	双精度浮点型数据

date

​	短日期类型

datetime

​	长日期类型

clob

​	字符大对象

​	最多可存储4g的字符串

​	比如：存一篇文章，存一个说明

​	超过255个字符的都要采用clob字符大对象来存储

​	character Large OBject:CLOB

blob

​	二进制大对象

​	Binary Large OBject

​	专门用来存储图片，声音，视频等流媒体数据

​	往BLOB类型的字段上插入数据的时候，例如插入一个图片，视频等

# 创建一个学生表

```sql
create table t_student(
	no int,
    name varchar(32),
    sex char(1),
    age int(3),
    email varchar(255)
);
删除表:
	drop table if exists t_student;
```

# 插入数据insert

语法格式

​	insert into 表名(字段名1，字段名2，字段名3) values (值1，值2，值3);

注意：字段名和值要一一对应，即数量要对应，数据类型要对应

```sql
insert into t_student (no,name,sex,age,email) values (1,'张三','m',20,'zhangsan@qq.com')
```

# 插入日期

字符串转换成日期类型

str_to_date('01-10-1999','%d-%m-%Y');01-10-1999顺序可以变，但和后面的保持一致

不转换直接插入日期

```sql
insert into t_user(id,name,birth) values(2,'leo','1990-10-01')
```

## 日期格式化

date_format日期格式化，将date类型转换成varchar格式

```sql
select id,name,date_format(birth,'%Y/%m/%d') as birth from t_user;
```

# date和datetime

date 和 datetime两个类型的区别

date是短日期，只包括年月日信息

datetime是长日期，包括年月日时分秒

mysql短日期默认格式:%Y-%m-%d

mysql长日期默认格式:%Y-%m-%d %h:%i:%s

```sq
insert into t_user(id,name,birth,create_time) values (1,'zhangsan','1990-10-1','2020-3-18 15:49:50')
```

或者取得现在的时间

```sql
insert into t_user(id,name,birth,create_time) values (1,'zhangsan','1990-10-1',now())89zx
```

# update和delete

## 更新数据update

语法格式

```sq
update t_user set name='jack',birth='2000-10-11'  where id=2;
```

更新所有数据

​	update t_user set name='abc'

## 删除数据delete

语法格式

​	delete from 表名 where 条件;

注意:没有条件，整张表会全部删除

```sql
delete from t_user where id=2;
insert into t_user(id) values(2);
delete from t_user;
```

# insert 一次插入多条记录

```sql
insert into t_user(id,name,birth,create_time) values (
(1,'zs','1980-10-11',now()),
(2,'lisi','1981-10-11',now()),
(3,'wangwu','1982-10-11',now())
);
```

# 利用查询结果快速创建表

```sql
create table emp2 as select * from emp;
```

原理：将一个查询结果当做一张表新建！！！

这个可以完成表的快速复制！！！！

表创建出来，表中的数据也存在了！！！！

============================================

将某几列取出创建一张表

```sql
create table mytable as select empno,ename,from emp where job='MANAGER'
```

一个很少用的操作，将查出来的结果插入表中

```sql
insert into dept_bak select *from dept;
```

# 快速删除表中的数据

```sql
delete from dept_bak;//这种删除数据比较慢
```

## 删除大表(上亿条记录)

## delete语句删除数据的原理

逻辑删除，表中的数据被删除，但是内存不释放，缺点是删除效率低，有点是支持回滚操作，可以恢复数据！！！

## truncate语句删除数据的原理

这种删除效率比较高，表被一次截断，物理删除！！！！

这种删除优点是快！！！！！！

缺点是不支持回滚！！！！！！

大表非常大，删除的的时候使用delete可能需要执行一小时才能删除完，效率较低

可以选择使用truncate删除表中的数据，只需要不到一秒钟就删除结束，效率较高，但是，使用之前，必须仔细询问客户是否真的要删除，并警告删除之后不可恢复。

truncate是删除表中的数据，表结构还在

注意和删除表语句drop table ...区分

truncate只能删除整张表，不支持对单行的删除

# 对表结构的增删改

在实际开发中，需求一旦确认之后，很少需要对表结构进行更改，因为开发进行中时修改表结构成本比较高。动表结构对应java代码就需要修改，成本比较高。这个责任由设计人员承担。

修改表结构不需要写到java程序里的，利用navicat之类的可视化程序修改即可

# 约束

约束对应单词 constraint

在创建表的时候，可以给表中的字段加上一些约束，来保证这个表中的数据的完整性，有效性

约束是为了保证，表中的数据有效

约束包括哪些

非空约束:not null

唯一性约束:unique

主键约束:primary key

外键约束:foreign key

检查约束:check (mysql不支持，oracle支持)

## 非空约束 not null

非空约束not null约束的字段不能为null

```sql
drop table if exists t_vip;
create table t_vip(
	id int,
    name varchar(255) not null
);
```

```sql
insert into t_vip(id,name) values (1,'zs');
insert into t_vip(id,name) values(2,'lisi')
```

小插曲:

xxxx.sql 这种文件被称为SQL脚本文件

SQL脚本文件中编写了大量的SQL语句

我们执行SQL脚本文件的时候，该文件中所有的SQL语句会全部执行

采用 source +拖拽

可以执行SQL脚本

在实际的工作中，第一天到了公司，项目经理会给你一个xxx.sql文件，你执行这个脚本文件，你电脑上的数据库数据就有了

## 唯一性约束

唯一性约束的字段不能重复，但是可以为NULL

```sql
drop table if exists t_vip

create table t_vip(

	id int,

	name varchar(255) unique,

	email varchar(255)

);
```

```sql
insert into t_vip(id,name,email) values (1,'zs','111@qq.com');
insert into t_vip(id,name,email) values (1,'ls','12631@qq.com');
insert into t_vip(id,name,email) values (1,'wu','1114656w@qq.com');
select * from t_vip
```

### 两个字段联合起来具有唯一性

```sql
drop table if exists t_vip;
create table t_vip(
	id int,
    name varchar(255), 
	email varchar(255),
    unique(name,email)
);
```

name 和email两个字段联合起来唯一

=============================================

约束直接添加到列后面叫列级约束

name varchar(255) unique, 
email varchar(255) unique,

unique(name,email)约束不在列后面，称为表级约束

当需要多个字段联合起来添加某一个约束的时候，需要使用表级约束

### unique和not null可以联合吗 ？

```sql
drop table if exists t_vip;
create table t_vip(
	id int,
    name varchar(255) not null unique
);
```

```shell
+-------+--------------+------+-----+---------+-------+
| Field | Type         | Null | Key | Default | Extra |
+-------+--------------+------+-----+---------+-------+
| id    | int          | YES  |     | NULL    |       |
| name  | varchar(255) | NO   | PRI | NULL    |       |
+-------+--------------+------+-----+---------+-------+
```

我们可以看到在key一栏变成了primary key 也就是说在unique和not null作用下，变成了主键约束

在mysql,如果一个字段同时被not null和unique约束的话，该字段自动变成主键字段（注意oracle中不一样！！！！）

## 主键约束

主键约束的相关术语

​		主键约束:就是一种约束

​		主键字段：该字段上添加了主键约束，这样的字段叫：主键字段

​		主键值：主键字段中的每一个值都叫做：主键值

什么是主键，有啥用？

​	主键值是每一行记录的唯一标识！！！

​	主键值是每一行记录的身份证号！！！

记住：任何一张表都应该有主键，没有主键，表无效！！！！

主键的特征：not null+unique（主键值不能是null，同时也不能重复！）

怎么给一张表添加主键约束呢？

```sql
drop table if exists t_vip;
create table t_vip(
	id int primary key,
    name varchar(255)
);
insert into t_vip(id,name) values(1,'zs');
insert into t_vip(id,name) values(2,'lisi');
insert into t_vip(id,name) values(2,'wangwu');//执行报错，2是主键，不能重复
```

### 表级约束

```sql
drop table if exists t_vip；
//id 和name联合起来作为主键：复合主键
create table t_vip(
	id int,
    name varchar(255),
    email varchar(255),
    primary key(id,name)
);
insert into t_vip(id,name,email) values(1,'zhangsan','zhangsan@qq.com');
insert into t_vip(id,name,email) values(1,'lisi','lisi@123.com');
//错误：不能重复
```

实际开发中不建议使用复合主键,建议使用单一主键，主键存在的意义就是去重和非空，一个主键就可以起到这个作用。

### 一张表中主键约束可以加两个吗？

```sql
drop table if exists t_vip;
create table t_vip(
	id int primary key,
    name varchar(255) primary key
);
```

///报错，一张表,主键约束只能添加1个。

==========================================================

### 主键值建议使用：

​	int

​	bigint

​	char等类型

​	不建议使用varchar作为主键，主键值一般都是数字，一般都是定长的。

### 主键的分类

​	自然主键:主键值是一个自然数，和业务没有关系

​	业务主键：主键值和业务紧密关联，例如拿银行卡账号做主键值

​	在实际开发中，是使用业务主键多，还是使用自然主键多？

​		自然主键使用比较多，因为主键只要做到不重复就行，不需要有意义

​		业务主键不好，因为主键一旦和业务挂钩，一旦业务发生变动的时候可能会影响到主键，所以业务主键不建		议使用，一般使用自然主键。

### 自动维护一个主键值

```sql
drop table if exists t_vip;

create table t_vip(

	id int primary key auto_increment,

	name varchar(255)

);
//=================================================
insert into t_vip(name) values ('zhangsan');

insert into t_vip(name) values ('zhangsan');

insert into t_vip(name) values ('zhangsan');
```

## 外键约束(PK)

外键约束涉及到的相关术语：

​	外键约束：一种约束，

​	外键字段：该字段上添加了外键约束

​	外键值：外键字段中的每一个值

业务背景

​	请设计数据库表，来描述“班级和学生的信息”

第一种方案：

| ID   | name     | class | school            |
| ---- | -------- | ----- | ----------------- |
| 1    | zhangsan | 100   | XXXX中学高三100班 |
| 2    | lisi     | 101   | XXXX中学高三101班 |
| 3    | zhangsi  | 100   | XXXX中学高三100班 |
| 4    | lisan    | 101   | XXXX中学高三101班 |
| 5    | zhangli  | 100   | XXXX中学高三100班 |

上述方案数据出现冗余，空间出现浪费

第二种方案：

t_class	班级表    一般称被指向（references）的表为父表

| classno(pk) | classname         |
| ----------- | ----------------- |
| 100         | XXXX中学高三100班 |
| 101         | XXXX中学高三101班 |

t_student	学生表

| no(pk) | name     | cno(PK引用t_calss这张表的classno) |
| ------ | -------- | --------------------------------- |
| 1      | zhangsan | 100                               |
| 2      | lisi     | 101                               |
| 3      | zhangsi  | 100                               |
| 4      | lisan    | 101                               |
| 5      | zhangli  | 100                               |

```sql
drop table if exists t_student;
drop table if exists t_class;
create table t_class(
	classno int primary key,
    classname varchar(255)
);
create table t_student(
	no int primary key auto_increment,
    name varchar(255),
    cno int,
    foreign key(cno) references t_class(classno)
);
insert into t_class(classno,classname) values(100,'XXXX中学高三100班');
insert into t_class(classno,classname) values(101,'XXXX中学高三101班');
//===================================================================
insert into t_student(name,cno) values('zhangsan',100);
insert into t_student(name,cno) values('lisi',101);//注意只能用100和101，因为父表中只有这两个值，保证数据安全
```

注意外键约束是一个约束方式，即约束子表中某字段的值必须被限制在父表中同名字段的值中。如果要联合使用两表查询，需要用连接查询

思考：子表中的外键引用的父表中的某个字段，被引用的这个字段必须是主键吗？

​	不一定是主键，但至少具有unique约束,可以为null

# 存储引擎(了解内容)

什么是存储引擎，有什么用呢？

​	存储引擎是mysql中特有的一个术语，其它数据库中没有。（oracle中有，但是不叫这个名字）

存储引擎一个表存储，组织数据的方式

不同的存储引擎，表存储数据的方式不同

## 怎么给表添加/指定存储引擎呢？

```sql
show create table t_student;
```

运行结果：

```shell
CREATE TABLE `t_student` (
  `no` int NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  `cno` int DEFAULT NULL,
  PRIMARY KEY (`no`),
  KEY `cno` (`cno`),
  CONSTRAINT `t_student_ibfk_1` FOREIGN KEY (`cno`) REFERENCES `t_class` (`classno`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

在建表的时候可以在最后小括号“）”的右边使用：

​	ENGINE来指定存储引擎

​	CHARSET来指定这张表的字符编码方式

结论：

​	mysql默认的存储殷勤是：InnoDB

​	mysql默认的字符编码方式是：utf8

## mysql支持哪些存储引擎

```sql
show engines \G;
```

运行结果：

```shell
*************************** 1. row ***************************
      Engine: MEMORY
     Support: YES
     Comment: Hash based, stored in memory, useful for temporary tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 2. row ***************************
      Engine: MRG_MYISAM
     Support: YES
     Comment: Collection of identical MyISAM tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 3. row ***************************
      Engine: CSV
     Support: YES
     Comment: CSV storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 4. row ***************************
      Engine: FEDERATED
     Support: NO
     Comment: Federated MySQL storage engine
Transactions: NULL
          XA: NULL
  Savepoints: NULL
*************************** 5. row ***************************
      Engine: PERFORMANCE_SCHEMA
     Support: YES
     Comment: Performance Schema
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 6. row ***************************
      Engine: MyISAM
     Support: YES
     Comment: MyISAM storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 7. row ***************************
      Engine: InnoDB
     Support: DEFAULT
     Comment: Supports transactions, row-level locking, and foreign keys
Transactions: YES
          XA: YES
  Savepoints: YES
*************************** 8. row ***************************
      Engine: BLACKHOLE
     Support: YES
     Comment: /dev/null storage engine (anything you write to it disappears)
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 9. row ***************************
      Engine: ARCHIVE
     Support: YES
     Comment: Archive storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
```

mysql支持九大存储引擎，不同mysql支持的引擎不同

## mysql引擎介绍

### MYISAM引擎

• 它管理的表具有以下特征：
	– 使用三个文件表示每个表：
				格式文件 — 存储表结构的定义（mytable.frm）
				数据文件 — 存储表行的内容（mytable.MYD）
				索引文件 — 存储表上索引（mytable.MYI）:索引可以缩小扫描范围，提高扫描效率
	– 可被转换为压缩、只读表来节省空间

提示一下：

​	对一张表来说，只要是主键，或者加有unique约束的字段上会自动创建索引。

MYISAM存储引擎特点：

​	可被转换成压缩，只读表来节省空间

​	这是这种存储引擎的优势

### InnoDB引擎

这是mysql默认的存储引擎，同时也是非常重要的存储引擎

InnoDB支持事务，支持数据库后崩溃后自动恢复机制

InnoDB存储引擎最主要的特点是：非常安全

InnoDB 存储引擎是MySQL 的缺省引擎。
	• 它管理的表具有下列主要特征：
	– 每个 InnoDB 表在数据库目录中以.frm 格式文件表示
	– InnoDB 表空间 tablespace 被用于存储表的内容
	– 提供一组用来记录事务性活动的日志文件
	– 用 COMMIT(提交)、SAVEPOINT 及ROLLBACK(回滚)支持事务处理
	– 提供全 ACID 兼容
	– 在 MySQL 服务器崩溃后提供自动恢复
	– 多版本（MVCC）和行级锁定
	– 支持外键及引用的完整性，包括级联删除和更新

InnoDB最大的特点就是支持事务：

​				以保证数据的安全

### MEMORY引擎

使用 MEMORY 存储引擎的表，其数据存储在内存中，且行的长度固定，这两个特点

使得 MEMORY 存储引擎非常快。
• MEMORY 存储引擎管理的表具有下列特征：
– 在数据库目录内，每个表均以.frm 格式的文件表示。
– 表数据及索引被存储在内存中。
– 表级锁机制。
– 不能包含 TEXT 或 BLOB 字段。
• MEMORY 存储引擎以前被称为HEAP 引擎。

MEMORY引擎优点：查询效率是最高的

缺点：不安全，关机之后数据消失，因为数据和索引都是在内存当中。

# 事务（重点）

## 什么是事务？

一个事物就是一个完整的业务逻辑

####什么是一个完整的业务逻辑####

​	假设转账，从A账户中向B账户中转账10000

​	将A中的钱减去10000

​	将B账户的钱加上10000

​	这就是一个完整的业务逻辑。

以上操作是一个最小的工作单元，要么同时成功，要么同时失败，不可再分。

这两个update语句要求必须同时成功或同时失败，这样才能保证钱是正确的。

## 只有DML语句才会有事务这一说，其它语句和事务无关

insert

delete

update

只有以上的三个语句和事务有关，其它都没有关系

假设所有业务，只要一条DML语句就能完成，还有必要存在事务吗？

正是因为做某件事的时候，需要多条DML语句共同连个起来才能完成，所以需要事务的存在，如果任何一件复杂的事儿都能一条DML语句完成，那么事务就没有存在的价值了

说到底，一条事务其实就是多条DML语句同时成功，或者同时失败。

## 事务的实现

InnoDB引擎：提供一组用来记录事务性活动的日志文件

事物开始：

insert

insert

insert

delete

update

update

事务结束了

在事务的执行过程中，每一条DML语句的操作都会记录到“事务性活动的日志文件”中

在事务的执行过程中，我们可以提交事务，也可以回滚事务

提交事务？
			清空事务性活动的日志文件，将数据全部彻底持久化到数据库表中。

​			提交事务标志着，事务的结束。并且是一种全部成功的结束

回滚事务

​			将之前所有的DML操作全部车削，并且清空事务性活动的日志文件

​			回滚事务标志着，事务的结束，并且是一种全部失败的结束

## 怎么提交事务，回滚事务

提交事务：commit;语句

回滚事务:rollback;语句

事务对应的英语单词是：transaction

测试一下，在mysql当中默认的事务行为是怎样的？
mysql默认情况下是支持自动提交事务的。（自动提交）
什么是自动提交？
		每执行一条DML语句，则提交一次！

这种自动提交实际上是不符合我们的开发习惯，因为一个业务
通常是需要多条DML语句共同执行才能完成的，为了保证数据
的安全，必须要求同时成功之后再提交，所以不能执行一条
就提交一条。

怎么将mysql的自动提交机制关闭掉呢？
	先执行这个命令：start transaction;

演示事务：


		------------------------回滚事务------------------------
		mysql> use bjpowernode;
		Database changed
		mysql> select * from dept_bak;
		Empty set (0.00 sec)
	
		mysql> start transaction;
		Query OK, 0 rows affected (0.00 sec)
	
		mysql> insert into dept_bak values(10,'abc', 'tj');
		Query OK, 1 row affected (0.00 sec)
	
		mysql> insert into dept_bak values(10,'abc', 'tj');
		Query OK, 1 row affected (0.00 sec)
	
		mysql> select * from dept_bak;
		+--------+-------+------+
		| DEPTNO | DNAME | LOC  |
		+--------+-------+------+
		|     10 | abc   | tj   |
		|     10 | abc   | tj   |
		+--------+-------+------+
		2 rows in set (0.00 sec)
	
		mysql> rollback;
		Query OK, 0 rows affected (0.00 sec)
	
		mysql> select * from dept_bak;
		Empty set (0.00 sec)


```shell
	-----------------提交事务--------------------------
	mysql> use bjpowernode;
	Database changed
	mysql> select * from dept_bak;
	+--------+-------+------+
	| DEPTNO | DNAME | LOC  |
	+--------+-------+------+
	|     10 | abc   | bj   |
	+--------+-------+------+
	1 row in set (0.00 sec)

	mysql> start transaction;
	Query OK, 0 rows affected (0.00 sec)

	mysql> insert into dept_bak values(20,'abc
	Query OK, 1 row affected (0.00 sec)

	mysql> insert into dept_bak values(20,'abc
	Query OK, 1 row affected (0.00 sec)

	mysql> insert into dept_bak values(20,'abc
	Query OK, 1 row affected (0.00 sec)

	mysql> commit;
	Query OK, 0 rows affected (0.01 sec)

	mysql> select * from dept_bak;
	+--------+-------+------+
	| DEPTNO | DNAME | LOC  |
	+--------+-------+------+
	|     10 | abc   | bj   |
	|     20 | abc   | tj   |
	|     20 | abc   | tj   |
	|     20 | abc   | tj   |
	+--------+-------+------+
	4 rows in set (0.00 sec)

	mysql> rollback;
	Query OK, 0 rows affected (0.00 sec)

	mysql> select * from dept_bak;
	+--------+-------+------+
	| DEPTNO | DNAME | LOC  |
	+--------+-------+------+
	|     10 | abc   | bj   |
	|     20 | abc   | tj   |
	|     20 | abc   | tj   |
	|     20 | abc   | tj   |
	+--------+-------+------+
	4 rows in set (0.00 sec)
```
## 事务包括四个特性

A：原子性

​	事务是最小的工作单元，不可再分

B：一致性

​	在一个事务中，所有的操作必须同时成功或者同时失败，保证数据的一致性

I：隔离性

​	A事务和B事务之间具有一定隔离

D:持久性

事务的提交就是将数据保存到硬盘上

## 重点研究事务的隔离性和隔离的四个级别

事务和事务之间有隔离，这个隔离有级别

分为4个级别

读未提交：read uncommitted （最低的隔离级别）

​	事务A可以读取到事务B未提交的数据

​	这种隔离级别存在*脏读现象*

​	这种隔离级别一般没人用，大多数数据库隔离级别都是二级起步

读已提交：read committed

​	事务A只能读取到事务B提交之后的数据

​	这种隔离级别解决了什么问题

​		解决了脏读现象

​	这种隔离级别存在什么问题

​		不可重复读取数据

​	不可重复读取数据：事务开启之后，第一次读到的数据是三条，当前事务还没有结束，可能第二次再读取的时候，读到的数据是四条，3不等于4，称为不可重复读取

这种隔离级别是比较真实的数据

​			oracle默认

可重复读：repeated read

​			解决了不可重复读的问题

​			读取的数据是事务开始时的数据，对事务进行时的数据不读取

​			但会出现幻读的问题

​			mysql默认

序列化/串行化：serializable（最高的隔离级别）

​			解决上述所有问题，但是效率最低，这种隔离表示事务排队，不能并发。

## 验证各种隔离级别

查看当前隔离级别：

```shell
select @@tx_isolation;//老版语法
select @@transaction_isolation;//最新的语法
+-------------------------+
| @@transaction_isolation |
+-------------------------+
| REPEATABLE-READ         |
+-------------------------+
```

设置隔离级别

```sql
set global transaction isolation level read committed;//设置全局隔离级别为read committed
```



### 验证read uncommitted

验证的方法是开两个命令窗，模拟两个事务

事务A														事务B

=====================================================

```shell
mysql> use learndatabases;									
							mysql> use learndatabases;
mysql> start transaction;
mysql> select * from t_user;
Empty set 
							mysql> start transaction;
							mysql> insert into t_user values('zhangsan');
mysql> select * from t_user;
+----------+
| name     |
+----------+
| zhangsan |
+----------+
```

可以看到，在事务B还未提交的时候,事务A就已经可以读到事务B的数据了，但实际上事务B并未提交，这导致读到的数据是脏数据。

### 验证read commited

```sql
set global transaction isolation level read committed;//设置全局隔离级别为read committed
```

事务A														事务B

=====================================================

```sql
mysql> use learndatabases;									
							mysql> use learndatabases
mysql>start transaction;
							mysql>start transaction;
mysql>select* from t_user;
+----------+
| name     |
+----------+
| zhangsan |
+----------+
							mysql>insert into t_user values('lisi');
							mysql>insert into t_user values('wangwu');
mysql>select* from t_user;
+----------+
| name     |
+----------+
| zhangsan |
+----------+
							commit;
mysql>select* from t_user;
+----------+
| name     |
+----------+
| zhangsan |
| lisi     |
| wangwu   |
+----------+
```

可以看到，在提交之前，读取的一直是上一次提交的数据，提交后，可以读取到提交后的数据。

不可重复读是指，在事务A未提交前，可以实时地读取到事务B已提交的数据，导致事务A一个事务中不同时间读取到的数据会不一样，也就是说，两次读取结果不一样，结果不会重复，不能复盘。

### 验证repeatable read

```sql
mysql> use learndatabases;									
							mysql> use learndatabases
mysql>start transaction;
							mysql>start transaction;
mysql>select* from t_user;
+------+
| name |
+------+
| lisi |
+------+
							mysql>insert into t_user values('lisi');
							mysql>insert into t_user values('wangwu');
mysql>select* from t_user;
+------+
| name |
+------+
| lisi |
+------+

							mysql>commit;
mysql>select* from t_user;
+------+
| name |
+------+
| lisi |
+------+
mysql>commit;
mysql>select* from t_user;
+--------+
| name   |
+--------+
| lisi   |
| lisi   |
| wangwu |
+--------+
							mysql>start transaction;
							mysql>insert into t_user values('wangwu');
mysql>select* from t_user;
+--------+
| name   |
+--------+
| lisi   |
| lisi   |
| wangwu |
+--------+
```

可以看到，在事务A提交之前，读取的一直是同一个数据，这就避免了不可重复读的问题。只有在事务A和事务B同时提交的情况下，才会在事务A中更新数据;

### 验证 serializabale

```sql
mysql> use learndatabases;									
							mysql> use learndatabases
mysql>start transaction;
							mysql>start transaction;
mysql>select* from t_user;
+--------+
| name   |
+--------+
| lisi   |
| lisi   |
| wangwu |
| wangwu |
+--------+
mysql>insert into t_user values('lisi');
							mysql>select* from t_user;(运行这个语句时事务B命令行会卡住，显示一直在运行，实际上是在排队，																			等待事务A提交后再执行)
mysql>commit;
							+--------+（在事务A提交命令敲完的一瞬间，事务B会立即开始执行）
							| name   |
							+--------+
							| lisi   |
							| lisi   |
							| wangwu |
							| wangwu |
							| lisi   |
							+--------+							
```

# 索引

索引是在数据库表的字段上添加的，是为了提高查询效率存在的一种机制。

一张表的一个字段可以添加一个索引，当然，多个字段联合起来也可以添加索引。

索引相当于一本书的目录，是为了缩小扫描范围而存在的一种机制。

对于一本字典来说，查找某个汉字有两种方式：

​			第一种方式：一页一页挨着找知道找到为止，这种查找方式属于全字典扫描，效率比较低

​			第二种：先通过索引去定位一个大概的位置上，然后做局域性扫描。

t_user

id						name					email					address

------------------------------------------------------------------------------------

1							zhangsan

2							lisi

3							wangwu

4							zhaoliu

5							jack

select *from t_user where name='jack';

以上的这条SQL语句会去name字段上扫描，为什么？

因为查询条件是：name='jack'

如果name字段上没有添加索引，或者说没有给name字段创建索引，MySQL会进行全扫描，会将name字段上的每一个值都对比一遍，效率低

在数据库中索引是需要排序的，采用一个B-Tree的数据结构

## 索引的实现原理

假设有一张用户表：t_user

id(PK)					name						每一行记录在硬盘上都有物理存储编号

100						zhangsan					0x1111
120						lisi								0x2222
99							wangwu					0x8888
88							zhaoliu					  0x9999
101						jack							  0x6666
55							lucy							0x5555
130						tom							  0x7777

提醒1：在任何数据库当中主键上都会自动添加索引对象，id字段上自动有索引，
因为id是PK。另外在mysql当中，一个字段上如果有unique约束的话，也会自动
创建索引对象。

提醒2：在任何数据库当中，任何一张表的任何一条记录在硬盘存储上都有
一个硬盘的物理存储编号。

提醒3：在mysql当中，索引是一个单独的对象，不同的存储引擎以不同的形式
存在，在MyISAM存储引擎中，索引存储在一个.MYI文件中。在InnoDB存储引擎中
索引存储在一个逻辑名称叫做tablespace的当中。在MEMORY存储引擎当中索引
被存储在内存当中。不管索引存储在哪里，索引在mysql当中都是一个树的形式
存在。（自平衡二叉树：B-Tree）

**对于一个添加了索引的字段，字段值会通过排序二叉树分布，在查询字段时，会通过二叉树查询字段值，取出字段值对应的地址，找到具体的值。**

在mysql当中，主键上，以及unique字段上都会自动添加索引

## 什么条件下，我们会考虑给字段添加索引

1.数据量庞大

2.该字段经常出现在where后面，以条件的形式存在，也就是说这个字段总是被扫描

3.该字段很少的DML（delete，insert,update）操作,因为对索引进行DML操作会导致重新排序，降低运行速度

## 索引的创建和删除

索引创建：

```sql
create index emp_ename_index on emp(ename);
```

​		给emp表的ename添加索引，起名为：emp_ename_index

删除索引：

```sql
drop index emp_ename_index on emp;
```

将emp表上的emp_ename_index索引对象删除

## 怎么查看一个SQL语句是否使用了索引进行检索

使用explain解释一个SQL语句

不加索引

```sql
explain select * from emp where ename ='KING';
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | emp   | NULL       | ALL  | NULL          | NULL | NULL    | NULL |   14 |    10.00 | Using where |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
```

可以看到row为14,type为all，表示全表扫描

对ename加索引后

```sql
explain select * from emp where ename ='KING';
+----+-------------+-------+------------+------+-----------------+-----------------+---------+-------+------+----------+-------+
| id | select_type | table | partitions | type | possible_keys   | key             | key_len | ref   | rows | filtered | Extra |
+----+-------------+-------+------------+------+-----------------+-----------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | emp   | NULL       | ref  | emp_ename_index | emp_ename_index | 33      | const |    1 |   100.00 | NULL  |
+----+-------------+-------+------------+------+-----------------+-----------------+---------+-------+------+----------+
```

可以看到加上索引后，row变为1

## 索引的失效

### 失效的第一种情况：

​	select *from emp where ename like ‘%T’;

​	ename上即使添加了索引，也不会走索引，因为模糊匹配当中以‘%’开头	

​	尽量避免模糊查询的时候以"%"开始

​	这是一种优化的手段/策略。

### 失效的第二种情况

​			使用or的时候会失效，如果使用or那么要求or两边的条件字段都要有

索引，才会走索引，有一边没索引就会让索引失效

```sql
 explain select * from emp where ename ='KING'or job='MANAGER';
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | emp   | NULL       | ALL  | NULL          | NULL | NULL    | NULL |   14 |    19.00 | Using where |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
```

### 失效的第三种情况

使用复合索引的时候，没有使用左侧的列查找，索引失效

什么是复合索引？

​					两个字段，或者更多的字段联合起来添加一个索引，叫做复合索引。

```sql
create index emp_job_sal_index on emp(job,sal);

explain select *from emp where job='MANAGER';
+----+-------------+-------+------------+------+-------------------+-------------------+---------+-------+------+----------+-------+
| id | select_type | table | partitions | type | possible_keys     | key               | key_len | ref   | rows | filtered | Extra |
+----+-------------+-------+------------+------+-------------------+-------------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | emp   | NULL       | ref  | emp_job_sal_index | emp_job_sal_index | 30      | const |    3 |   100.00 | NULL  |
+----+-------------+-------+------------+------+-------------------+-------------------+---------+-------+------+----------+-------+
explain select *from emp where sal='800';
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | emp   | NULL       | ALL  | NULL          | NULL | NULL    | NULL |   14 |    10.00 | Using where |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
```

### 失效的第四种情况

where后面的条件参加了运算

```sql
explain select * from emp where sal = 800;
+----+-------------+-------+------+---------------+---------------+---------+-------+------+-------------+
| id | select_type | table | type | possible_keys | key           | key_len | ref   | rows | Extra       |
+----+-------------+-------+------+---------------+---------------+---------+-------+------+-------------+
|  1 | SIMPLE      | emp   | ref  | emp_sal_index | emp_sal_index | 9       | const |    1 | Using where |
+----+-------------+-------+------+---------------+---------------+---------+-------+------+-------------+
```

```sql
mysql> explain select * from emp where sal+1 = 800;
+----+-------------+-------+------+---------------+------+---------+------+------+-------------+
| id | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra       |
+----+-------------+-------+------+---------------+------+---------+------+------+-------------+
|  1 | SIMPLE      | emp   | ALL  | NULL          | NULL | NULL    | NULL |   14 | Using where |
+----+-------------+-------+------+---------------+------+---------+------+------+-------------+
```

### 失效的第5种情况

​		在where当中索引列使用了函数

```sql
explain select * from emp where lower(ename) = 'smith';
+----+-------------+-------+------+---------------+------+---------+------+------+-------------+
| id | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra       |
+----+-------------+-------+------+---------------+------+---------+------+------+-------------+
|  1 | SIMPLE      | emp   | ALL  | NULL          | NULL | NULL    | NULL |   14 | Using where |
+----+-------------+-------+------+---------------+------+---------+------+------+-------------+
```

还有很多失效的情况如where后面使用了隐式的类型转换导致失效并未列举。。。。。

## 索引的分类

### 单一索引

一个字段上添加索引

### 复合索引

两个或更多字段上添加索引

### 主键索引

主键上添加索引

### 唯一性索引

具有unique约束的字段上添加索引

。。。。。

注意，唯一性比较弱的字段上添加索引用处不大

# 视图

视图：不同角度看待同一份数据

## 创建视图

表复制

```sql
 create table dept2 as select * from dept;
```

dept2表中的数据：

```sql
mysql> select * from dept2;
	+--------+------------+----------+
	| DEPTNO | DNAME      | LOC      |
	+--------+------------+----------+
	|     10 | ACCOUNTING | NEW YORK |
	|     20 | RESEARCH   | DALLAS   |
	|     30 | SALES      | CHICAGO  |
	|     40 | OPERATIONS | BOSTON   |
	+--------+------------+----------+
```

创建视图对象

```sql
create view dept2_view as select * from dept2;
```

删除视图对象

```sql
drop view dept2_view;
```

注意：只有DQL语句才能以view的形式创建。
	create view view_name as 这里的语句必须是DQL语句;

## 视图的作用

对视图的增删改查会导致原表被修改，可以将视图理解成指针

//面向视图查询

```sql
select * from dept2_view; 
```

// 面向视图插入

```sql
insert into dept2_view(deptno,dname,loc) values(60,'SALES', 'BEIJING');
```

// 查询原表数据

```sql
select * from dept2;
	+--------+------------+----------+
	| DEPTNO | DNAME      | LOC      |
	+--------+------------+----------+
	|     10 | ACCOUNTING | NEW YORK |
	|     20 | RESEARCH   | DALLAS   |
	|     30 | SALES      | CHICAGO  |
	|     40 | OPERATIONS | BOSTON   |
	|     60 | SALES      | BEIJING  |
	+--------+------------+----------+
```

// 面向视图删除

```sql
delete from dept2_view;
```

​	// 查询原表数据

```sql
select * from dept2;
Empty set (0.00 sec)
```

// 创建视图对象

```sql
create view 
		emp_dept_view
	as
		select 
			e.ename,e.sal,d.dname
		from
			emp e
		join
			dept d
		on
			e.deptno = d.deptno;
```

// 查询视图对象

```sql
mysql> select * from emp_dept_view;
	+--------+---------+------------+
	| ename  | sal     | dname      |
	+--------+---------+------------+
	| CLARK  | 2450.00 | ACCOUNTING |
	| KING   | 5000.00 | ACCOUNTING |
	| MILLER | 1300.00 | ACCOUNTING |
	| SMITH  |  800.00 | RESEARCH   |
	| JONES  | 2975.00 | RESEARCH   |
	| SCOTT  | 3000.00 | RESEARCH   |
	| ADAMS  | 1100.00 | RESEARCH   |
	| FORD   | 3000.00 | RESEARCH   |
	| ALLEN  | 1600.00 | SALES      |
	| WARD   | 1250.00 | SALES      |
	| MARTIN | 1250.00 | SALES      |
	| BLAKE  | 2850.00 | SALES      |
	| TURNER | 1500.00 | SALES      |
	| JAMES  |  950.00 | SALES      |
	+--------+---------+------------+
```

// 面向视图更新

```sql
update emp_dept_view set sal = 1000 where dname = 'ACCOUNTING';
```

//原表数据被更新

```sql
mysql> select * from emp;
	+-------+--------+-----------+------+------------+---------+---------+--------+
	| EMPNO | ENAME  | JOB       | MGR  | HIREDATE   | SAL     | COMM    | DEPTNO |
	+-------+--------+-----------+------+------------+---------+---------+--------+
	|  7369 | SMITH  | CLERK     | 7902 | 1980-12-17 |  800.00 |    NULL |     20 |
	|  7499 | ALLEN  | SALESMAN  | 7698 | 1981-02-20 | 1600.00 |  300.00 |     30 |
	|  7521 | WARD   | SALESMAN  | 7698 | 1981-02-22 | 1250.00 |  500.00 |     30 |
	|  7566 | JONES  | MANAGER   | 7839 | 1981-04-02 | 2975.00 |    NULL |     20 |
	|  7654 | MARTIN | SALESMAN  | 7698 | 1981-09-28 | 1250.00 | 1400.00 |     30 |
	|  7698 | BLAKE  | MANAGER   | 7839 | 1981-05-01 | 2850.00 |    NULL |     30 |
	|  7782 | CLARK  | MANAGER   | 7839 | 1981-06-09 | 1000.00 |    NULL |     10 |
	|  7788 | SCOTT  | ANALYST   | 7566 | 1987-04-19 | 3000.00 |    NULL |     20 |
	|  7839 | KING   | PRESIDENT | NULL | 1981-11-17 | 1000.00 |    NULL |     10 |
	|  7844 | TURNER | SALESMAN  | 7698 | 1981-09-08 | 1500.00 |    0.00 |     30 |
	|  7876 | ADAMS  | CLERK     | 7788 | 1987-05-23 | 1100.00 |    NULL |     20 |
	|  7900 | JAMES  | CLERK     | 7698 | 1981-12-03 |  950.00 |    NULL |     30 |
	|  7902 | FORD   | ANALYST   | 7566 | 1981-12-03 | 3000.00 |    NULL |     20 |
	|  7934 | MILLER | CLERK     | 7782 | 1982-01-23 | 1000.00 |    NULL |     10 |
	+-------+--------+-----------+------+------------+---------+---------+--------+
```

## 视图在实际开发中的作用

```sql
create view 
		emp_dept_view
as
select 
		e.ename,e.sal,d.dname
from
		emp e
join
		dept d
on
		e.deptno = d.deptno;
		
```

假设有一条非常复杂的SQL语句，而这条SQL语句需要在不同的位置上反复使用。
每一次使用这个sql语句的时候都需要重新编写，可以把这条复杂的SQL语句以视图对象的形式新建。在需要编写这条SQL语句的位置直接使用视图对象，可以大大简化开发。并且利于后期的维护，因为修改的时候也只需要修改一个位置就行，只需要修改视图对象所映射的SQL语句。

我们面向视图开发的时候，使用视图的时候可以像使用table一样。
可以对视图进行增删改查等操作。视图不是在内存当中，视图对象也是
	**存储在硬盘上的，不会消失**。

视图对应的语句只能是DQL语句。
		但是视图对象创建完成之后，可以对视图进行增删改查等操作。

增删改查，又叫做：CRUD。
		CRUD是在公司中程序员之间沟通的术语。一般我们很少说增删改查。
		一般都说CRUD。

​		C:Create（增）

​		R:Retrive（查：检索）

​		U:Update（改）

​		D:Delete（删）

# DBA命令

主要直接查文档就行

## 导入导出

在dos窗口上执行

数据导出

```shell
mysqldump learndatabases>D:\learndatabases.sql -uroot -p123456
```

导出到指定的表

```sql
mysqldump learndatabases emp>D:\learndatabases.sql -uroot -p123456
```



数据导入？
		注意：需要先登录到mysql数据库服务器上。
		然后创建数据库：create database learndatabases;
		使用数据库：use learndatabases;
		然后初始化数据库：source D:\learndatabases.sql;

# 数据库设计范式

第一范式：要求任何一张表必须有主键，每一个字段原子性不可再分

第二范式：建立在第一范式的基础上，要求所有非主键字段**完全**依赖主键，不要产生**部分**依赖。

第三范式：建立在第二范式的基础之上，要求所有非主键字段**直接**依赖主键，
	不要产生**传递**依赖。

## 第一范式

​	必须有主键，且每一个字段原子性不可再分

```
学生编号 学生姓名 	联系方式
------------------------------------------
1001		张三		zs@gmail.com,1359999999
1002		李四		ls@gmail.com,13699999999
1001		王五		ww@163.net,13488888888
```



上面的表不满足第一范式，1.没有主键，2.联系方式可以划分为邮箱和手机号

	学生编号(pk) 学生姓名	邮箱地址			联系电话
	----------------------------------------------------
	1001		张三		zs@gmail.com	1359999999
	1002		李四		ls@gmail.com	13699999999
	1003		王五		ww@163.net		13488888888
## 第二范式

建立在第一范式的基础之上，
	要求所有非主键字段必须完全依赖主键，不要产生部分依赖。

```
学生编号 学生姓名 教师编号 教师姓名
----------------------------------------------------
1001			张三		001		王老师
1002			李四		002		赵老师
1003			王五		001		王老师
1001			张三		002		赵老师

```

不满足第一范式，修改为

```sql
学生编号+教师编号(pk)		学生姓名  教师姓名
----------------------------------------------------
1001			001				张三			王老师
1002			002				李四			赵老师
1003			001				王五			王老师
1001			002				张三			赵老师

```

我们可以看出，编号和名字之间存在依赖，故产生了部分依赖，数据冗余,浪费了空间。

我们需要使用三张表和外键来解决这个问题

```
学生表
学生编号(pk)		学生名字
------------------------------------
1001					张三
1002					李四
1003					王五
		
```

```
教师表
教师编号(pk)		教师姓名
--------------------------------------
001					王老师
002					赵老师

```

```
学生教师关系表
id(pk)			学生编号(fk)			教师编号(fk)
------------------------------------------------------
1						1001						001
2						1002						002
3						1003						001
4						1001						002
	
```

多对多，三张表，关系表两个外键

## 第三范式

第三范式建立在第二范式的基础之上
	要求所有非主键字典必须直接依赖主键，不要产生传递依赖。

```sql
学生编号（PK） 学生姓名 班级编号  班级名称
---------------------------------------------------------
1001		张三		01		一年一班
1002		李四		02		一年二班
1003		王五		03		一年三班
1004		赵六		03		一年三班
```

以上表的设计是描述：班级和学生的关系。很显然是1对多关系！
	一个教室中有多个学生。

分析以上表是否满足第一范式？
		满足第一范式，有主键。

分析以上表是否满足第二范式？
		满足第二范式，因为主键不是复合主键，没有产生部分依赖。主键是单一主键。

分析以上表是否满足第三范式？
		第三范式要求：不要产生传递依赖！
		一年一班依赖01，01依赖1001，产生了传递依赖。
		不符合第三范式的要求。产生了数据的冗余。

```
班级表：一
班级编号(pk)				班级名称
----------------------------------------
01							一年一班
02							一年二班
03							一年三班

```

	学生表：多
	学生编号（PK） 学生姓名 班级编号(fk)
	-------------------------------------------
	1001		张三			01			
	1002		李四			02			
	1003		王五			03			
	1004		赵六			03		

一对多，两张表，多的表加外键

## 一对一大表的优化

```
t_user
id		login_name		login_pwd		real_name		email				address........
---------------------------------------------------------------------------
1			zhangsan		123				张三				zhangsan@xxx
2			lisi			123				李四				lisi@xxx
...
```

对于这种信息比较多的表，可以拆为两张表

```
t_login 登录信息表
id(pk)		login_name		login_pwd	
---------------------------------
1				zhangsan		123			
2				lisi			123			
```

```
t_user 用户详细信息表
id(pk)		real_name		email				address........	login_id(fk+unique)
-----------------------------------------------------------------------------------------
100			张三				zhangsan@xxx								1
200			李四				lisi@xxx	
```

采用第二张表fk（外键）唯一的策略保证两张表的一对一的对应关系。