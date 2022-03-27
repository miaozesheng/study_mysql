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

# 使用流程函数赋值操作
update user
set user.location_id = (case when salary > 10000 then 10 else 20 end)
where location_id is null
```
## 6.MySQL中的数据类型
![image.png](https://cdn.nlark.com/yuque/0/2022/png/21359294/1648139488564-284f8b8d-9525-4b8d-830c-549944842c28.png#clientId=u388a1b1b-84ed-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=209&id=ub8859a30&margin=%5Bobject%20Object%5D&name=image.png&originHeight=418&originWidth=1126&originalType=binary&ratio=1&rotation=0&showTitle=false&size=256045&status=done&style=none&taskId=u4fcdcf0f-f665-4c85-a56e-a3b5d4bd06c&title=&width=563)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/21359294/1648139500360-2f83fc8a-2f79-45ed-a420-020a992960ba.png#clientId=u388a1b1b-84ed-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=162&id=ud4a6d379&margin=%5Bobject%20Object%5D&name=image.png&originHeight=323&originWidth=1531&originalType=binary&ratio=1&rotation=0&showTitle=false&size=269478&status=done&style=none&taskId=ubff03a01-48a2-4879-815e-e1748835ea6&title=&width=765.5)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/21359294/1648139508935-4f55695a-a748-4ab5-a61b-61d6412dc266.png#clientId=u388a1b1b-84ed-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=114&id=u33f9ea5e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=228&originWidth=1272&originalType=binary&ratio=1&rotation=0&showTitle=false&size=173178&status=done&style=none&taskId=u79c16582-e15d-4399-a282-b7c487b211a&title=&width=636)
## 7. MySQL中的约束
```sql
# 约束作用在表中字段上的规则,用于限制存储在表中的数据.
# 保证数据库中数据的正确性,有效性和完整性.

# 添加外键的语法
# 1.创建表结构时添加外键
create table 表名(
  字段名 数据类型,
  字段名 数据类型,
  ...
  [constraint] [外键名称] foreign key (外键字段名) references 主表(主表列名)
);



# 2.修改表结构时添加外键
alter table 表名 add constraint 外键名称 foreign key (外键字段名) references 主表(主表列名);

alter table emp add constraint  fk_emp_dept_id foreign key (dept_id) references dept;

# 3. 删除外键
alter table 表名 drop foreign key 外键名称;

alter table emp drop foreign fk_emp_dept_id;

# 外键约束的删除/更新行为

alter table emp add constraint  fk_emp_dept_id foreign key (dept_id) references dept on update cascade on delete cascade;
alter table emp add constraint  fk_emp_dept_id foreign key (dept_id) references dept on update set null on delete set null;

```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/21359294/1648289855942-76a891b5-58ea-4550-8b97-bcad9d3262e2.png#clientId=uf5bc53c6-a021-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=180&id=u3bee662a&margin=%5Bobject%20Object%5D&name=image.png&originHeight=359&originWidth=1102&originalType=binary&ratio=1&rotation=0&showTitle=false&size=157146&status=done&style=none&taskId=u11b47572-1295-44ee-8979-bb511e4440d&title=&width=551)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/21359294/1648291182659-3ec9a49d-f4ee-4bc6-92bc-cc4922de98ff.png#clientId=uf5bc53c6-a021-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=192&id=ub5116c5a&margin=%5Bobject%20Object%5D&name=image.png&originHeight=383&originWidth=1359&originalType=binary&ratio=1&rotation=0&showTitle=false&size=167610&status=done&style=none&taskId=u127653f9-7608-4357-b9cd-cd68c226390&title=&width=679.5)

## 8 MySQL多表查询语句
```sql
# 多表关系 
一对多 常见的部门和员工的关系,一个员工归属于一个部门 ,一个部门下可以有多个员工
多对多 常见的学生和课程的关系,一个课程可以被多个学生选择 一个学生可以选择多门课程 这种情况我们一般会建立中间表,存储学生和课程的主键来进行关联(核心思想)
一对一 常见的情况是一张表的基础字段放在表1中,详情字段放在表2中,提升操作效率 拆表之后任意一方可以加入外键关联另一张表的主键,并且设置唯一约束,可以保证一对一


# 多表概述
# 假设emp表中有20个员工,其中两个员工没有部门id,那么下边这条SQL将只能查出来18条数据,这里会和后边的左联右联不一样.
select * from emp ,dept where emp.dept_id = dept.id;


# 内连接 假设存在AB两张表,那么查询两张表交集数据
# 隐式内连接
select 字段列表 from 表一,表二,... where 条件...;
# 显示内连接
select 字段列表 from 表一 [inner] join 表二 on 连接条件...;

# 外连接 分为左外连接和右外连接 
# 左外连接 假设存在AB两张表,那么查询左表所有数据 及两张表交集部分数据
select 字段列表 from 表一  left [outer] join 表二 on 连接条件...;
# 右外连接 假设存在AB两张表,那么查询右表所有数据 及两张表交集部分数据
select 字段列表 from 表一  right [outer] join 表二 on 连接条件...;
# 自连接 当前表与自身的连接查询 自连接必须使用表别名
  select 字段列表 from 表一 别名1  right [outer] join 表二 别名2 on 连接条件...;
# 联合查询 union 和 union all union会把重复数据去掉 union all会展示重复数据
select * from emp
union [all]
select * from emp2

# 子查询 sql语句中嵌套select语句 称为嵌套查询 又称子查询 子查询外部语句可以是增删改查的任意一个,内部语句只能是查询语句


# 根据子查询的结果不同,分为下边四个类型:
  # 标量子查询 子查询的结果为单个值
    # 子查询返回的结果为单个值,数字 日期 字符串等 常用的操作符 = <> > >= < <= 
    
    example1:查询部门是销售部的全部员工信息
      select id from dept where name = '销售部' #这里查询出来的结果只有一个id
      select * from emp where dept_id = 4;
    
      select * from emp where dept_id = (select id from dept where name = '销售部');

    example2:查询入职日期在miaozesheng之后的全部员工
      select entrydate from emp where name = 'miaozesheng'
      select * from emp where entrydate>'2021-04-01'
      
      select * from emp where entrydate>(select entrydate from emp where name = 'miaozesheng')
    
    
  # 列子查询 子查询的结果为一列
    # 子查询返回的结果是一列(可以是多行) 常用的操作符 in , not in , any , some , all
     example1:查询销售部和市场部的所有员工信息
       select id from dept where name = '销售部' or '市场部';
       select * from emp where dept_id in (2,4);
       
       select * from emp where dept_id in (select id from dept where name = '销售部' or '市场部')
     example2:查询比财务部所有人工资都高的员工信息
       -- 首先查出财务部所有人员id 根据人员id查出所有对应工资 最后查出工资大于工资的员工
       -- all后面会跟一个SQL salary>all(sql) 意思就是 sql会查询到多条salary数据 salary>all(sql) 即为salary要大于所有sql查出来的数据
       select * from emp where salary > all (select salary from emp where dept_id = (select id from dept where name = '财务部'))
       -- 另外一种解决方式:
       select * from emp where salary > (select max(salary) from emp where dept_id = (select id from dept where name = '财务部'))
     example3:查出比研发部任意一人工资高的员工信息
       select * from emp where salary> any(select salary from emp where dept_id = (select id from dept where name = '研发部'))
       -- 另一种解决方式
       select * from emp where salary > (select min(salary) from emp where dept_id = (select id from dept where name = '研发部'))
  
  
  # 行子查询 子查询的结果为一行
    # 行子查询返回的结果为一行 常用的操作符为 = , <> , in , not in
    example1:查询与张无忌的薪资及直属领导相同的员工信息;
    select salary,manager_id from emp where name = '张无忌'
    
    select * from emp where (salary,manager_id) = (select salary,manager_id from emp where name = '张无忌');
  
  # 表子查询 子查询的结果为一个表(多行多列) 
    # 常用的操作符 in 经常出现在from之后
    example1:查询与'miaozesheng','miaozewang'的职位和薪资相同的员工信息
    select job ,salary from emp where name = 'miaozesheng' or 'miaozewang' -- 这里可能会查询出多条数据,也就是多行多列
    
    select * from emp where (job,salary) in (select job ,salary from emp where name = 'miaozesheng' or 'miaozewang');
    
    example2:查询入职日期是2021-04-01之后的员工信息及其部门信息
    select * from emp where entrydate>'2021-04-01' -- 后续部门信息需要用这里查询出来的结果作为表去联立其他表
    
    select * from (select * from emp where entrydate>'2021-04-01') tb1 left join dept tb2 on tb1.dept_id = tb2.id

# 根据子查询的位置不同 分为where之后 from之后 select之后

# 多表查询案例

# 查询拥有员工的部门id和部门名称 这里需要注意的点是,我们使用内连接完成,取交集可以确定部门一定存在员工
select d.name,d.id from emp,dept where emp.dept_id = dept.id;

# 新表salarygrade 存在三个字段 工资等级 对应最低工资 对应最高工资 如A 10000,100000 B 1000,9999 C 0 ,999
# 查询所有员工的工资等级 特别注意连接条件 emp.salary>= salarygrade.losal and emp.salary<= salarygrade.hisal
select * from emp e , salarygrade s where e.salary>=s.losal and e.salary<= s.hisal

# 查询研发部的所有员工的信息及工资等级
# 表 emp salarygrade dept 连接条件 emp.dept_id = dept.id 和 emp.salary between salarygrade.losal and salarygrade.hisal
select e.*,s.grade from emp e , dept d , salarygrade s 
where emp.dept_id = dept.id 
and (emp.salary between salarygrade.losal and salarygrade.hisal)
and e.name = '研发部'

# 查询低于本部门平均工资的员工信息
select avg(e.salary) from emp where e.dept_id = '1'
select avg(e.salary) from emp where e.dept_id = '2'

select e2.* from emp e2 where salary < (select avg(e1.salary) from emp where e1.dept_id = e2.dept_id )
# 查询所有的部门信息,并统计部门的员工人数
select d.id,d.name,(select count(*) from emp e where e.dept_id = d.id) 人数 from dept d;


```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/21359294/1648294348527-38c6d491-996b-495e-8321-e4e506c0c9be.png#clientId=uf5bc53c6-a021-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=314&id=u73cad924&margin=%5Bobject%20Object%5D&name=image.png&originHeight=627&originWidth=1553&originalType=binary&ratio=1&rotation=0&showTitle=false&size=172609&status=done&style=none&taskId=uc3dced6d-b8cd-4e6d-9991-29f88694cd3&title=&width=776.5)
## 10 事务相关
```sql
# 事务简介
  事务是一组操作的集合,它是一个不可分割的单位,事务会把所有的操作作为一个整体向系统提交或者撤销请求,这些操作要么同时成功,要么同时失败.
  开启事务之后,多条SQL执行语句,即使前几条已经执行成功了,但是如果没有commit,那么数据不会改变.

# 事务操作
# 通过系统配置  查看/设置事务提交方式
select @@autocommit;
set @@autocommit = 0;
# 提交事务
commit;
# 回滚事务
rollback;

# 显式的开启事务
start transaction; 或 begin;

# 事务四大特性
  原子性 atomicity 事务是不可分割的最小操作单元,要么全部成功,要么全部失败
  一致性 consistency 事务完成是,所有数据保持一致状态
  隔离性 isolation 数据库系统提供的隔离机制,保证事务在不受外部并发操作影响的环境中运行
  持久性 durability 事务一旦提交或者回滚,他对数据库中的数据的公变就是永久的
  
# 并发事务问题
  脏读 一个事务读到了另外一个事务没有提交的数据
  不可重复读 一个事务先后读取了同一条数据两次,但是两次读取的数据不一致
  幻读 一个事务按照条件查询数据时,没有对应的数据行,但是插入数据时,发现该数据行已经存在了,好像出现了幻影.

# 事务隔离级别
  读未提交 可能出现脏读 可重复读 幻读
  读已提交 可能出现可重复读 幻读(Oracle默认隔离级别)
  可重复读 可能独显幻读  (Mysql默认隔离级别)
  串行化   由于所有语句都是串行执行,不会存在任何问题,但是效率会特别低

# 查看当前事务隔离级别 REPEATABLE-READ
select @@transaction_isolation;
# 设置事务隔离级别 针对会话级别 当前会话和全局
set session  transaction isolation level [read uncommited | read committed | repeatable read | serializable]
set global transaction isolation level [read uncommited | read committed | repeatable read | serializable]
```
