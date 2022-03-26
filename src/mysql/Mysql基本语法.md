## 1.SQL之DDL 数据库操作
```sql
# 展示所有的数据库
show databases;

# 查询当前数据库
select database();

# 创建数据库
create database [if not exists] 数据库名[default charset 字符集] [collate 排序规则];

# 删除数据库
drop database[if exists] 数据库名;

# 使用数据库
use 数据库;

# 展示当前数据中所有的表
show tables;

# 查看表的详细信息
desc 表名;

# 查询指定表的建表语句
show create table 表名; 

# 建表语句
create table tb_user(
  id int comment '编号',
  name varchar(50) comment '名称',
  age int comment '年龄',
  gender varchar(1) comment '性别'
)comment '用户表';

# 在表中增加字段
alter table 表名 add 字段名 类型(长度) [comment 注释] [约束];
alter table emp add nickname varchar(20) comment '增加字段nickname';

# 修改某个字段数据类型
alter table 表命 modify 字段名 新数据类型(长度)

# 修改某个字段字段名和字段类型
alter table 表名 change 旧字段名 新字段名 类型(长度) [comment 注释] [约束];
alter table emp change nickname username varchar(20) comment '修改用户名及数据长度';

# 删除某个字段
alter table 表名 drop 字段名;

# 删除某个表
drop table [ if exists] 表名;

# 删除某个表 并重新创建该表 其实只是删除数据,结构会被重新创建出来
truncate table 表名;

# 重命名某个表
alter table 表名 rename to 新表名;

```
## 2.SQL之DML
```sql
# 给指定字段添加数据
insert into user (column1,column2,...) values (value1,value2,...);
# 给全部字段添加数据
insert into user values (value1,value2,...);
# 批量添加数据
insert into user values (value1,value2,...),(value1,value2,...),(value1,value2,...);

# 修改数据
update table_name
set column_name1 = value1,
    column_name2 = value2,
    ...
[where condition]; 

# 删除数据
delete from table_name where condition;
```
## 3.SQL值DQL 
```sql
SELECT
    字段列表 第五步
FROM
    表名列表 第一步
WHERE 
    条件列表 第二步
GROUP BY 
    分组字段列表 第三步
HAVING
    分组后条件列表 第四步
ORDER BY
    排序字段列表 第六步
LIMIT
    分页参数
    
# 聚合函数 将一列数据作为一个整体,进行纵向运算 count max min avg sum
# 使用聚合函数的时候,所有的null值都是不参与运算的
# 分组查询 
# where和having的区别 where是在分组之前进行过滤,不满足where条件,不参与分组
# having是对分组之后的结果进行过滤 where不能对聚合函数进行判断,having可以

select gender,count(*) from user group by gender;
select gender,avg(age) from user group by gender;

# 查询年龄小于45岁的员工，并根据工作地址分组，获取员工数量大于等于3的工作地址
select count(*) from emp where age<45 group by workaddress having count(*) > 3

select * from user limit 起始索引,查询记录数;
```
## 4.SQL 之 DCL 数据库控制语言
```sql
# 用来管理数据库用户 控制数据库的访问权限

# 查询用户
use mysql;

select * from user;
# 这里主机名的作用是，在哪一个主机上，当前用户可以访问mysql % 表示任意主机
# 创建用户
create user '用户名'@'主机名' identified by '密码';
# 修改用户
alter user '用户名'@'主机名' identified with mysql_native_password by '新密码';
# 删除用户
drop user '用户名'@'主机名';

# 创建用户user，只能在当前主机localhost访问
create user 'user'@'localhost' identified by '123456';
# 创建用户user2 可以在任意主机访问该数据库 密码为123456
create user 'user2'@'%' identified by '123456'
# 修改user2的密码为1234
alter user 'user2'@'%' identified with mysql_native_password by '1234';

# 删除user@localhost用户
drop user 'user'@'localhost';

# 权限控制

#查询权限
show grants for '用户名'@'主机名';
#授予权限  如果授予所有数据库所有权限 权限列表使用all 使用*.*即可
grant 权限列表 on 数据库名.表名 to '用户名'@'主机名';
#撤销权限
revoke 权限列表 on 数据库名.表名 from '用户名'@'主机名';

show grants for 'user2'@'%';
grant all on *.* to 'user2'@'%';
revoke all on *.*  from 'user2'@'%';

```
## 5 SQL中常用的函数
```sql
# 流程函数 可以在sql语句中实现条件筛选，从而提高语句的效率
if(value,t,f) 如果value为true，那么返回t，否则返回false
ifnull（value1,value2）如果value1不为空，那么返回value1，如果value1为空，那么返回value2

select if(true,'OK','Error');//OK
select ifnull('ok','default');//ok
select ifnull('','Default');//返回''空字符串

# 需求，查询一个表的员工姓名和工作地址，如果在北京和上海，那么返回一线城市，其他返回二线城市
select name,workaddress from emp;

select 
name, 
（case workaddress 
when '北京' then '一线城市' 
when '上海' then '一线城市' 
else '二线城市' 
end）as '工作在几线城市'
from emp;

select
    id,
    name,
(case when math>=85 then '优秀' when math between 60 and 84 then '及格' else '不及格' end) as '数学',
 (case when english>=85 then '优秀' when math between 60 and 84 then '及格' else '不及格' end) as '英语',
( case when chinese>=85 then '优秀' when math between 60 and 84 then '及格' else '不及格' end) as '语文'
from score;
```
## 6.MySQL中的数据类型
![image.png](https://cdn.nlark.com/yuque/0/2022/png/21359294/1648139488564-284f8b8d-9525-4b8d-830c-549944842c28.png#clientId=u388a1b1b-84ed-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=209&id=ub8859a30&margin=%5Bobject%20Object%5D&name=image.png&originHeight=418&originWidth=1126&originalType=binary&ratio=1&rotation=0&showTitle=false&size=256045&status=done&style=none&taskId=u4fcdcf0f-f665-4c85-a56e-a3b5d4bd06c&title=&width=563)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/21359294/1648139500360-2f83fc8a-2f79-45ed-a420-020a992960ba.png#clientId=u388a1b1b-84ed-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=162&id=ud4a6d379&margin=%5Bobject%20Object%5D&name=image.png&originHeight=323&originWidth=1531&originalType=binary&ratio=1&rotation=0&showTitle=false&size=269478&status=done&style=none&taskId=ubff03a01-48a2-4879-815e-e1748835ea6&title=&width=765.5)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/21359294/1648139508935-4f55695a-a748-4ab5-a61b-61d6412dc266.png#clientId=u388a1b1b-84ed-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=114&id=u33f9ea5e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=228&originWidth=1272&originalType=binary&ratio=1&rotation=0&showTitle=false&size=173178&status=done&style=none&taskId=u79c16582-e15d-4399-a282-b7c487b211a&title=&width=636)
