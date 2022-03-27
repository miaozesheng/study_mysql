> 主要涉及存储引擎 索引 SQL优化 视图/存储过程/触发器 锁 innodb引擎 mysql管理等内容

# 一 MySQL存储引擎
## 1.MySQL体系结构
> 从上往下,依次为连接层--> 服务层--> 引擎层--> 存储层
> - 连接层:最上层是一些客户端和链接服务,主要完成一些类似于连接处理,授权认证,及相关的安全方案.服务器也会为安全接入的每个客户
> 
验证它所具有的的权限.
> - 服务层:主要完成大多数的核心服务功能,如SQL接口,并完成缓存的查询 SQL的分析和优化 部分内置函数的执行 
> 
所有跨存储引擎的功能也在这一层实现,如过程,函数等.
> - 引擎层:存储引擎真正的负责了MySQL中数据的存储和提取,服务器通过api和存储引擎进行通信.不同的此时引擎有不同的功能.
> 
我们可以根据自己的需要,选择不同的存储引擎.
> - 存储层:主要是将数据存储在文件系统之上,并完成与存储引擎的交互.

![image.png](https://cdn.nlark.com/yuque/0/2022/png/21359294/1648308234266-b922c36c-b25c-46a2-b0d1-b7851d887ebf.png#clientId=u37134a15-3a2e-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=333&id=ua3c75a53&margin=%5Bobject%20Object%5D&name=image.png&originHeight=665&originWidth=1000&originalType=binary&ratio=1&rotation=0&showTitle=false&size=433429&status=done&style=none&taskId=u9c310a1e-3fdd-41cb-b12e-715f5a9ae3f&title=&width=500)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/21359294/1648308280222-62ceabb6-3b8c-4752-8e40-7cbc7dd5881c.png#clientId=u37134a15-3a2e-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=427&id=ua93a72f7&margin=%5Bobject%20Object%5D&name=image.png&originHeight=673&originWidth=799&originalType=binary&ratio=1&rotation=0&showTitle=false&size=315891&status=done&style=none&taskId=u2136c3bb-62ba-45a2-8d5b-af33a52efb0&title=&width=507.5)

## 2.存储引擎简介
> 存储引擎就是存储数据 建立索引 更新查询数据等技术的实现 方式.存储引擎是基于表的,而不是基于库的,索引存储引擎也被称之为表类型.

```sql
# 查询当前数据库支持的存储引擎
show engines;

# 创建表my_myisam,并指定存储引擎为MyISAM
create table if not exists my_myisam(
    id int,
    name varchar(10)
) engine = MyISAM;

# 创建表my_memory,并指定存储引擎为Memory
create table if not exists my_memory(
    id int,
    name varchar(10)
) engine = Memory;
```
## 3.存储引擎特点
### 3.1 InnoDB
> 兼并高可可靠性和高性能的通用存储引擎.
> 特点: 事务 外键 行级锁
> - DML操作遵循ACID模型,支持事务
> - 行级锁,提高并发访问性能.
> - 支持外键,保证数据完整性和正确性
> 
磁盘文件中,每一张表都用xxx.ibd标识,存储该表的表结构(frm sdi)数据和索引 参数:innode_file_per_table

```sql
# mysql8之后默认打开,打开说明每一张表对应一个xxx.ibd的文件
show variables like 'innodb_file_per_table';
```
> 在终端文件中打开,使用命令查看xxx.ibd内存储的内容

```shell
miaozesheng@miaozesengdeMBP mybatis % ibd2sdi account.ibd

["ibd2sdi"
,
{
	"type": 1,
	"id": 367,
	"object":
		{
    "mysqld_version_id": 80021,
    "dd_version": 80021,
    "sdi_version": 80019,
    "dd_object_type": "Table",
    "dd_object": {
        "name": "account",
        "mysql_version_id": 80021,
        "created": 20210123032628,
        "last_altered": 20210123032628,
        "hidden": 1,
        "options": "avg_row_length=0;encrypt_type=N;key_block_size=0;keys_disabled=0;pack_record=0;stats_auto_recalc=0;stats_sample_pages=0;",
        "columns": [
            {
                "name": "id",
                "type": 4,
                "is_nullable": false,
                "is_zerofill": false,
                "is_unsigned": false,
                "is_auto_increment": false,
                "is_virtual": false,
                "hidden": 1,
                "ordinal_position": 1,
                "char_length": 11,
                "numeric_precision": 10,
                "numeric_scale": 0,
                "numeric_scale_null": false,
                "datetime_precision": 0,
                "datetime_precision_null": 1,
                "has_no_default": true,
                "default_value_null": false,
                "srs_id_null": true,
                "srs_id": 0,
                "default_value": "AAAAAA==",
                "default_value_utf8_null": true,
                "default_value_utf8": "",
                "default_option": "",
                "update_option": "",
                "comment": "",
                "generation_expression": "",
                "generation_expression_utf8": "",
                "options": "interval_count=0;",
                "se_private_data": "table_id=1074;",
                "engine_attribute": "",
                "secondary_engine_attribute": "",
                "column_key": 2,
                "column_type_utf8": "int",
                "elements": [],
                "collation_id": 255,
                "is_explicit_collation": false
            },
            {
                "name": "uid",
                "type": 4,
                "is_nullable": true,
                "is_zerofill": false,
                "is_unsigned": false,
                "is_auto_increment": false,
                "is_virtual": false,
                "hidden": 1,
                "ordinal_position": 2,
                "char_length": 11,
                "numeric_precision": 10,
                "numeric_scale": 0,
                "numeric_scale_null": false,
                "datetime_precision": 0,
                "datetime_precision_null": 1,
                "has_no_default": false,
                "default_value_null": true,
                "srs_id_null": true,
                "srs_id": 0,
                "default_value": "",
                "default_value_utf8_null": true,
                "default_value_utf8": "",
                "default_option": "",
                "update_option": "",
                "comment": "",
                "generation_expression": "",
                "generation_expression_utf8": "",
                "options": "interval_count=0;",
                "se_private_data": "table_id=1074;",
                "engine_attribute": "",
                "secondary_engine_attribute": "",
                "column_key": 4,
                "column_type_utf8": "int",
                "elements": [],
                "collation_id": 255,
                "is_explicit_collation": false
            },
            {
                "name": "money",
                "type": 6,
                "is_nullable": true,
                "is_zerofill": false,
                "is_unsigned": false,
                "is_auto_increment": false,
                "is_virtual": false,
                "hidden": 1,
                "ordinal_position": 3,
                "char_length": 22,
                "numeric_precision": 22,
                "numeric_scale": 0,
                "numeric_scale_null": true,
                "datetime_precision": 0,
                "datetime_precision_null": 1,
                "has_no_default": false,
                "default_value_null": true,
                "srs_id_null": true,
                "srs_id": 0,
                "default_value": "",
                "default_value_utf8_null": true,
                "default_value_utf8": "",
                "default_option": "",
                "update_option": "",
                "comment": "",
                "generation_expression": "",
                "generation_expression_utf8": "",
                "options": "interval_count=0;",
                "se_private_data": "table_id=1074;",
                "engine_attribute": "",
                "secondary_engine_attribute": "",
                "column_key": 1,
                "column_type_utf8": "double",
                "elements": [],
                "collation_id": 255,
                "is_explicit_collation": false
            },
            {
                "name": "DB_TRX_ID",
                "type": 10,
                "is_nullable": false,
                "is_zerofill": false,
                "is_unsigned": false,
                "is_auto_increment": false,
                "is_virtual": false,
                "hidden": 2,
                "ordinal_position": 4,
                "char_length": 6,
                "numeric_precision": 0,
                "numeric_scale": 0,
                "numeric_scale_null": true,
                "datetime_precision": 0,
                "datetime_precision_null": 1,
                "has_no_default": false,
                "default_value_null": true,
                "srs_id_null": true,
                "srs_id": 0,
                "default_value": "",
                "default_value_utf8_null": true,
                "default_value_utf8": "",
                "default_option": "",
                "update_option": "",
                "comment": "",
                "generation_expression": "",
                "generation_expression_utf8": "",
                "options": "",
                "se_private_data": "table_id=1074;",
                "engine_attribute": "",
                "secondary_engine_attribute": "",
                "column_key": 1,
                "column_type_utf8": "",
                "elements": [],
                "collation_id": 63,
                "is_explicit_collation": false
            },
            {
                "name": "DB_ROLL_PTR",
                "type": 9,
                "is_nullable": false,
                "is_zerofill": false,
                "is_unsigned": false,
                "is_auto_increment": false,
                "is_virtual": false,
                "hidden": 2,
                "ordinal_position": 5,
                "char_length": 7,
                "numeric_precision": 0,
                "numeric_scale": 0,
                "numeric_scale_null": true,
                "datetime_precision": 0,
                "datetime_precision_null": 1,
                "has_no_default": false,
                "default_value_null": true,
                "srs_id_null": true,
                "srs_id": 0,
                "default_value": "",
                "default_value_utf8_null": true,
                "default_value_utf8": "",
                "default_option": "",
                "update_option": "",
                "comment": "",
                "generation_expression": "",
                "generation_expression_utf8": "",
                "options": "",
                "se_private_data": "table_id=1074;",
                "engine_attribute": "",
                "secondary_engine_attribute": "",
                "column_key": 1,
                "column_type_utf8": "",
                "elements": [],
                "collation_id": 63,
                "is_explicit_collation": false
            }
        ],
        "schema_ref": "mybatis",
        "se_private_id": 1074,
        "engine": "InnoDB",
        "last_checked_for_upgrade_version_id": 0,
        "comment": "",
        "se_private_data": "",
        "engine_attribute": "",
        "secondary_engine_attribute": "",
        "row_format": 2,
        "partition_type": 0,
        "partition_expression": "",
        "partition_expression_utf8": "",
        "default_partitioning": 0,
        "subpartition_type": 0,
        "subpartition_expression": "",
        "subpartition_expression_utf8": "",
        "default_subpartitioning": 0,
        "indexes": [
            {
                "name": "PRIMARY",
                "hidden": false,
                "is_generated": false,
                "ordinal_position": 1,
                "comment": "",
                "options": "flags=0;",
                "se_private_data": "id=167;root=4;space_id=17;table_id=1074;trx_id=26888;",
                "type": 1,
                "algorithm": 2,
                "is_algorithm_explicit": false,
                "is_visible": true,
                "engine": "InnoDB",
                "engine_attribute": "",
                "secondary_engine_attribute": "",
                "elements": [
                    {
                        "ordinal_position": 1,
                        "length": 4,
                        "order": 2,
                        "hidden": false,
                        "column_opx": 0
                    },
                    {
                        "ordinal_position": 2,
                        "length": 4294967295,
                        "order": 2,
                        "hidden": true,
                        "column_opx": 3
                    },
                    {
                        "ordinal_position": 3,
                        "length": 4294967295,
                        "order": 2,
                        "hidden": true,
                        "column_opx": 4
                    },
                    {
                        "ordinal_position": 4,
                        "length": 4294967295,
                        "order": 2,
                        "hidden": true,
                        "column_opx": 1
                    },
                    {
                        "ordinal_position": 5,
                        "length": 4294967295,
                        "order": 2,
                        "hidden": true,
                        "column_opx": 2
                    }
                ],
                "tablespace_ref": "mybatis/account"
            },
            {
                "name": "FK_Reference_8",
                "hidden": false,
                "is_generated": false,
                "ordinal_position": 2,
                "comment": "",
                "options": "flags=0;",
                "se_private_data": "id=168;root=5;space_id=17;table_id=1074;trx_id=26888;",
                "type": 3,
                "algorithm": 2,
                "is_algorithm_explicit": false,
                "is_visible": true,
                "engine": "InnoDB",
                "engine_attribute": "",
                "secondary_engine_attribute": "",
                "elements": [
                    {
                        "ordinal_position": 1,
                        "length": 4,
                        "order": 2,
                        "hidden": false,
                        "column_opx": 1
                    },
                    {
                        "ordinal_position": 2,
                        "length": 4294967295,
                        "order": 2,
                        "hidden": true,
                        "column_opx": 0
                    }
                ],
                "tablespace_ref": "mybatis/account"
            }
        ],
        "foreign_keys": [
            {
                "name": "FK_Reference_8",
                "match_option": 1,
                "update_rule": 1,
                "delete_rule": 1,
                "unique_constraint_name": "PRIMARY",
                "referenced_table_catalog_name": "def",
                "referenced_table_schema_name": "mybatis",
                "referenced_table_name": "user",
                "elements": [
                    {
                        "column_opx": 1,
                        "ordinal_position": 1,
                        "referenced_column_name": "id"
                    }
                ]
            }
        ],
        "check_constraints": [],
        "partitions": [],
        "collation_id": 255
    }
}
}
,
{
	"type": 2,
	"id": 22,
	"object":
		{
    "mysqld_version_id": 80021,
    "dd_version": 80021,
    "sdi_version": 80019,
    "dd_object_type": "Tablespace",
    "dd_object": {
        "name": "mybatis/account",
        "comment": "",
        "options": "encryption=N;",
        "se_private_data": "flags=16417;id=17;server_version=80021;space_version=1;state=normal;",
        "engine": "InnoDB",
        "engine_attribute": "",
        "files": [
            {
                "ordinal_position": 1,
                "filename": "./mybatis/account.ibd",
                "se_private_data": "id=17;"
            }
        ]
    }
}
}
]



```
## 

> innodb逻辑存储结构

![image.png](https://cdn.nlark.com/yuque/0/2022/png/21359294/1648347851776-740a3b09-de2a-42f4-a064-81918000520b.png#clientId=u37134a15-3a2e-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=338&id=u39d7b58d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=675&originWidth=1462&originalType=binary&ratio=1&rotation=0&showTitle=false&size=298776&status=done&style=none&taskId=ud6b88c9a-e8bb-48ef-bf45-e8cebb87304&title=&width=731)
### 3.2 MyISAM存储引擎
> 是MySQL早期的默认存储引擎
> 特点:
> - 不支持事务,不支持外键
> - 支持表级锁 不支持行锁
> - 访问速度快
> 
磁盘中涉及的文件有 xxx.MYD xxx.MYI xxx.sdi 分别存储数据 索引 表结构

```json
{
  "mysqld_version_id": 80021,
  "dd_version": 80021,
  "sdi_version": 80019,
  "dd_object_type": "Table",
  "dd_object": {
    "name": "accounts",
    "mysql_version_id": 80021,
    "created": 20201106044602,
    "last_altered": 20201106044602,
    "hidden": 1,
    "options": "avg_row_length=0;key_block_size=0;keys_disabled=0;pack_record=0;server_p_s_table=1;stats_auto_recalc=0;stats_sample_pages=0;",
    "columns": [
      {
        "name": "USER",
        "type": 29,
        "is_nullable": true,
        "is_zerofill": false,
        "is_unsigned": false,
        "is_auto_increment": false,
        "is_virtual": false,
        "hidden": 1,
        "ordinal_position": 1,
        "char_length": 128,
        "numeric_precision": 0,
        "numeric_scale": 0,
        "numeric_scale_null": true,
        "datetime_precision": 0,
        "datetime_precision_null": 1,
        "has_no_default": false,
        "default_value_null": true,
        "srs_id_null": true,
        "srs_id": 0,
        "default_value": "",
        "default_value_utf8_null": true,
        "default_value_utf8": "",
        "default_option": "",
        "update_option": "",
        "comment": "",
        "generation_expression": "",
        "generation_expression_utf8": "",
        "options": "interval_count=0;",
        "se_private_data": "",
        "engine_attribute": "",
        "secondary_engine_attribute": "",
        "column_key": 4,
        "column_type_utf8": "char(32)",
        "elements": [],
        "collation_id": 46,
        "is_explicit_collation": true
      },
      {
        "name": "HOST",
        "type": 29,
        "is_nullable": true,
        "is_zerofill": false,
        "is_unsigned": false,
        "is_auto_increment": false,
        "is_virtual": false,
        "hidden": 1,
        "ordinal_position": 2,
        "char_length": 255,
        "numeric_precision": 0,
        "numeric_scale": 0,
        "numeric_scale_null": true,
        "datetime_precision": 0,
        "datetime_precision_null": 1,
        "has_no_default": false,
        "default_value_null": true,
        "srs_id_null": true,
        "srs_id": 0,
        "default_value": "",
        "default_value_utf8_null": true,
        "default_value_utf8": "",
        "default_option": "",
        "update_option": "",
        "comment": "",
        "generation_expression": "",
        "generation_expression_utf8": "",
        "options": "interval_count=0;",
        "se_private_data": "",
        "engine_attribute": "",
        "secondary_engine_attribute": "",
        "column_key": 1,
        "column_type_utf8": "char(255)",
        "elements": [],
        "collation_id": 11,
        "is_explicit_collation": true
      },
      {
        "name": "CURRENT_CONNECTIONS",
        "type": 9,
        "is_nullable": false,
        "is_zerofill": false,
        "is_unsigned": false,
        "is_auto_increment": false,
        "is_virtual": false,
        "hidden": 1,
        "ordinal_position": 3,
        "char_length": 20,
        "numeric_precision": 19,
        "numeric_scale": 0,
        "numeric_scale_null": false,
        "datetime_precision": 0,
        "datetime_precision_null": 1,
        "has_no_default": true,
        "default_value_null": false,
        "srs_id_null": true,
        "srs_id": 0,
        "default_value": "AAAAAAAAAAA=",
        "default_value_utf8_null": true,
        "default_value_utf8": "",
        "default_option": "",
        "update_option": "",
        "comment": "",
        "generation_expression": "",
        "generation_expression_utf8": "",
        "options": "interval_count=0;",
        "se_private_data": "",
        "engine_attribute": "",
        "secondary_engine_attribute": "",
        "column_key": 1,
        "column_type_utf8": "bigint",
        "elements": [],
        "collation_id": 255,
        "is_explicit_collation": false
      },
      {
        "name": "TOTAL_CONNECTIONS",
        "type": 9,
        "is_nullable": false,
        "is_zerofill": false,
        "is_unsigned": false,
        "is_auto_increment": false,
        "is_virtual": false,
        "hidden": 1,
        "ordinal_position": 4,
        "char_length": 20,
        "numeric_precision": 19,
        "numeric_scale": 0,
        "numeric_scale_null": false,
        "datetime_precision": 0,
        "datetime_precision_null": 1,
        "has_no_default": true,
        "default_value_null": false,
        "srs_id_null": true,
        "srs_id": 0,
        "default_value": "AAAAAAAAAAA=",
        "default_value_utf8_null": true,
        "default_value_utf8": "",
        "default_option": "",
        "update_option": "",
        "comment": "",
        "generation_expression": "",
        "generation_expression_utf8": "",
        "options": "interval_count=0;",
        "se_private_data": "",
        "engine_attribute": "",
        "secondary_engine_attribute": "",
        "column_key": 1,
        "column_type_utf8": "bigint",
        "elements": [],
        "collation_id": 255,
        "is_explicit_collation": false
      }
    ],
    "schema_ref": "performance_schema",
    "se_private_id": 18446744073709551615,
    "engine": "PERFORMANCE_SCHEMA",
    "last_checked_for_upgrade_version_id": 0,
    "comment": "",
    "se_private_data": "",
    "engine_attribute": "",
    "secondary_engine_attribute": "",
    "row_format": 1,
    "partition_type": 0,
    "partition_expression": "",
    "partition_expression_utf8": "",
    "default_partitioning": 0,
    "subpartition_type": 0,
    "subpartition_expression": "",
    "subpartition_expression_utf8": "",
    "default_subpartitioning": 0,
    "indexes": [
      {
        "name": "ACCOUNT",
        "hidden": false,
        "is_generated": false,
        "ordinal_position": 1,
        "comment": "",
        "options": "flags=0;",
        "se_private_data": "",
        "type": 2,
        "algorithm": 4,
        "is_algorithm_explicit": false,
        "is_visible": true,
        "engine": "PERFORMANCE_SCHEMA",
        "engine_attribute": "",
        "secondary_engine_attribute": "",
        "elements": [
          {
            "ordinal_position": 1,
            "length": 128,
            "order": 1,
            "hidden": false,
            "column_opx": 0
          },
          {
            "ordinal_position": 2,
            "length": 255,
            "order": 1,
            "hidden": false,
            "column_opx": 1
          }
        ]
      }
    ],
    "foreign_keys": [],
    "check_constraints": [],
    "partitions": [],
    "collation_id": 255
  }
}
```

### 3.3 Memory存储引擎
> 表数据是存储在内存中的,由于受到硬件问题或断电的影响,只能将这些表作为临时表或者缓存使用.
> 特点:
> - 内存存放,防蚊裤
> - hash索引(默认)
> 
文件:xxx.sdi 存储表结构信息 (由于数据)

## 4.存储引擎选择
![image.png](https://cdn.nlark.com/yuque/0/2022/png/21359294/1648348532952-159e7b8c-3e28-4136-88e8-2787cdd2a76d.png#clientId=u37134a15-3a2e-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=309&id=u257529b6&margin=%5Bobject%20Object%5D&name=image.png&originHeight=617&originWidth=1326&originalType=binary&ratio=1&rotation=0&showTitle=false&size=191457&status=done&style=none&taskId=udf59af97-8ac0-41bb-b634-68022eb9296&title=&width=663)
> 在选择存储引擎时,根据应用系统的特点选择合适的存储引擎.对于复杂的应用系统,还可以根据实际情况选择多种存储银球进行组合.
> - innodb支持外键 行级锁 以及事务,如果应用对于事务的完整性有比较高的要求,在并发条件下要求数据的一致性,数据操作除了插入和查询之后,还有大量的更新和删除操作,那么innodb比较适合.
> - myisam 如果应用是以读和插入操作为主,只有很少的更新和删除操作,并且对于事务的完整性并发性要求不是很高,那么使用myisam比较合适.
> - memory 所有数据存储在内存中,太大的表无法缓存,而且无法保障数据的安全性,通常用于临时表及缓存使用.

# 二 索引
## 1.索引概述

> 索引是帮助mysql高效获取数据的数据结构(有序)
> 优点:
> - 提高数据检索的效率,降低数据库的io成本
> - 通过索引对于数据进行排序,降低数据排序的成本,降低CPU的消耗
> 
缺点:
> - 索引列也是要占用空间的
> - 索引大大提高了查询性能,但是降低了对于表的更新性能

## 2.索引结构
> MySQL的索引是在存储引擎层实现的,所以不同的存储引擎是存在不同的索引结构的主要包含
> - B+Tree索引 最常见的索引类型,大部分引擎都支持
> - Hash索引  底层数据结构是通过哈希表实现的,只有精准匹配索引列的查询才会生效,不支持范围查询
> - R-Truee索引 空间索引 是myisam的一个特殊索引类型,主要用户低了空间数据类型,通常使用较少
> - Full-test索引 是一种通过建立倒排索引 快速匹配文档的方式 类似于Lucene Solr ES

> 二叉树缺点: 顺序插入时,会形成一个链表,查询性能大大降低,大数据量情况下,层级较深,检索速度慢 可以通过红黑树解决
> 红黑树缺点:大数据量情况下,层级较深,检索速度慢 使用B树解决 就是可以有多个开叉方式实现
> B树: 一个节点上会有多个key,一个节点下会有多个子节点,普通父节点上也会存储数据 
> [https://www.cs.usfca.edu/~galles/visualization/Algorithms.html](https://www.cs.usfca.edu/~galles/visualization/Algorithms.html) 演示
> B+树 所有的元素(包括父节点的数据)都会出现在叶子节点上,父节点不会存储数据,只起索引的作用 叶子节点之间生成一个单向链表
> Mysql在原有B+数据的基础上多增加了一个指向相邻叶子节点的指针,相当于前后都可以互相访问. 提高区间访问和排序性能

> hash索引特点:
> 相当于把每一个值进行hash运算放到对应的槽中,槽中存储hash地址和元素值
> - hash索引只能用于对等比较 = in ,不支持范围查询 between >  < 
> - 无法利用hash索引完成排序操作
> - 查询效率高 通常只需要检索一次就好了,效率通常高于b+tree索引

## 3.索引分类
## 

> - 主键索引 针对主键 默认只能有一个
> - 唯一索引 unique 可以有多个 防止列数据重复
> - 常规索引 normal 快速定位特定数据 可以有多个
> - 全文索引 fulltext 查找文本中的关键字,而不是比较索引中的值

> 在innodb存储引擎中,根据索引的存储形式,又可以分为以下两种
> - 聚集索引 将数据存储于索引放到了一块,索引结构的叶子节点保存了行数据 有且只有一个
>    - 聚集索引选取规则: 如果存在主键，主键索引就是聚集索引。 
>    - 如果不存在主键，将使用第一个唯一(UNIQUE)索引作为聚集索引。
>    -  如果表没有主键，或没有合适的唯一索引，则InnoDB会自动生成一个rowid作为隐藏的聚集索 引。
> - 二级索引(辅助索引) 数据与索引分开存储,索引结构的叶子节点关联的是对应的主键 可以存在多个


## 4.索引语法

```sql
# 创建索引 如果不写[]里边的索引分类,那么就是普通索引
create [unique|fulltext] index index_name on table(index_col_name,...);

create index idx_user_name on tb_user(name);
create unique index idx_user_phone on tb_user(phone);
create index idx_user_pro_age_status on tb_user(profession,age,status);

# 查看索引
show index from table_name;
# 删除索引
drop index index_name on table_name;
```

## 5.SQL性能分析
```sql
# 查看执行频次 查询增删改查的语句执行频次
show [session|global] status 

show global status like 'Com_______' 这里有7个下划线
```

![image.png](https://cdn.nlark.com/yuque/0/2022/png/21359294/1648356008709-0544f1ba-effd-4979-bff7-4ae960d72a79.png#clientId=u37134a15-3a2e-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=376&id=u2062934e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=752&originWidth=834&originalType=binary&ratio=1&rotation=0&showTitle=false&size=79034&status=done&style=none&taskId=u727165d7-7384-4db9-b434-1d4865373ce&title=&width=417)
```sql
# 慢查询日志记录了所有执行时间超过指定参数(long_query_time,单位:秒 默认:10秒)的所有SQL语句的日志.
# Mysql的慢查询日志默认发不开启,需要在MySQL的配置文件/etc/my.cnf中配置如下信息

slow_query_log = 1 #配置默认开启慢查询日志
long_query_time=2 #配置规定超过两秒的就算是慢查询SQL

# 配置完成之后(linux中) 慢日志文件在/var/lib/mysql/localhost-slow.log中
show variabls like 'slow_query_log' #查询慢查询日志是否开启
```
```sql
# profile 能够在做SQL优化是帮助我们了解时间都耗费在哪里去了,通过have_profiling参数 能够看到当前MySQL是否支持该操作
show profiles;

select @@have_profiling; # mysql支持是一回事,是否开启是另外一回事.mysql默认是关闭的
select @@profiling;

set[global | session] profiling = 1;# 开启profiling

# 查询近一段时间SQL语句的执行情况
show profiles;
show profile for query 160;
show profile cpu for query 160;
```
```sql
# explain 或者desc命令获取MySQL是如何执行select语句的信息,包括执行过程中表连接及如何连接的顺序等.
explain select * from user;
desc select * from user;

# explain执行各个字段的含义
  其中比较重要的是type key rows extra几个参数

id
  select查询的序列号,标识查询中执行select子句或者是查询表的顺序
  规则:id相同,执行顺序从上到下 id不同,ID越大越先执行
  
select_type
  表示select的类型,常见的取值:
    simple 简单表,不使用表连接或者子查询
    primary 主查询 即外层的查询
    union 使用union关键字时第二个或者更后面的查询语句
    subquery select/where之后包含了子查询
    
type 可以看博客  
  表示连接类型,性能由好到差依次为:
    null 没有查询任何表 如select 'A'
    system 查询系统表 
    const 根据主键或者唯一索引进行访问
    eq_ref 
    ref  使用非唯一性索引(普通索引)进行访问
    range 相当于有范围的index,所以比下边那个快一点
    index 使用了索引,但是会对索引进行遍历和扫描 和全表扫描差不多
    all 全表扫描
possible_key
  表示可能用到的索引
key
  表示实际用到的索引 如果没有用到那么展示为null
key_len 
  表示索引中使用的字节数,该值为索引字段的最大长度,并非实际使用长度,在不损失精确性的前提下,长度越短越好
rows
  MySQL认为必须要执行查询的行数,在innodb中是一个估计值,可能不总是准确的.
filtered
  查询返回的行数占总读取行数的百分比,filtered值越大越好.
extra
  额外展示信息,上述几个字段没有展示出来的信息会在这里展示
```
## [https://blog.csdn.net/dennis211/article/details/78170079](https://blog.csdn.net/dennis211/article/details/78170079)
## 6.索引使用
> 进行联合索引是否生效的判断中,我们可以根据key_len去判断,如果联合索引全部被使用到,那么长度肯定比没有全部使用到长

> 最左前缀法则
> - 联合索引存在多个列,要遵循最左前缀法则,查询的时候从索引的最左列必须存在,并且不跳过索引中的列. 如果跳过某一列,那么该列之后的索引将失效
> - 这里需要特别注意,假设联合索引是三个列,查询的时候只要保证这三个列都存在即可,最左侧的索引列必须存在,这种情况也会走联合索引,对于顺序没有绝对要求
> - 假设存在联合索引 profession,age,status
> - select * from where profession = '软件工程' and age=20 and status = '0'  和select * from where age=20 and status = '0' and  profession = '软件工程' 效果是一致的,只有去掉索引左边的某一个查询列,左边索引才会失效
> - 但是如果我们创建联合索引,那么前后字段还是有要求的.
> 
范围查询
> - 联合索引中,出现范围查询 > < 等,范围查询右侧的列索引失效 但是>= <= 不会失效
> - 假设存在联合索引 profession,age,status
> - select * from tb_user where profession = '软件工程' and age>20 and status = '0' 这里profession和age都会走索引,status不走


> 索引列运算
> - 如果某个字段添加了索引,在查询的时候对于该列进行了函数运算,那么索引失效.直接全表扫描

> 字符串不加引号
> - 字符串类型字段使用时,如果不加引号,索引将失效.

> 模糊查询
> - 如果仅仅是尾部进行模糊匹配,索引不会失效 如果头表进行模糊匹配,索引失效
> - select * from user where profession like '软件%';  profession索引不会失效
> - select * from user where profession like '%软件'; profession 索引失效

> or连接的条件
> - 用or分割开的条件,如果or前的条件中的列有索引,而后面的列没有索引,那么涉及到的索引都不会被用到.  只有两侧都有索引的情况索引才会生效.
> - 假设存在联合索引 profession,age,status 存在主键索引 id 存在唯一索引 phone
> - select * from user where id = 10 or age = 23 索引失效,前边的id索引也会失效
> - select * from user where phone = '15091778987' or age = 23 索引失效,前边的phone索引也会失效
> - 注意,这里age单独使用 且 age不是联合索引的最左列,所以索引失效 如果age是联合索引的最左列,那么也可以生效

> 数据分布影响
> - 如果MySQL优化器评估走索引会比全表更慢,那么不使用索引.
> - is null 和 is not null 是否走索引 不固定,
> - 规则: 使用is null过滤之后只有极少符合条件的数据 那么走索引  如果该列null值较少,过滤之后符合条件的数据很少 使用is null 就会走索引 使用is not null 不走索引

> SQL提示 就是程序员给MySQL一个提示,告诉他你要用哪个索引 跟在表名之后 
> 如果一个字段同时存在于单列索引中和联合索引中的最左列 查询该字段时,MySQL优化器会默认使用联合索引,这个时候就需要使用SQL提示了
> - use index; 告诉数据库用哪个索引
>    - select * from user use index(inx_user_pro) where profssion = '软件工程';
> - ignore index; 告诉数据库不用哪个索引
>    - select * from user ignore index(inx_user_pro) where profesion - '软件工程';
> - force index; 告诉数据库必须使用哪个索引
>    - select * from user force index(idx_user_pro) where professoin = '软件工程';

> 覆盖索引
> 我们在查询过程中,尽量使用覆盖索引,不要使用select * (查询使用了索引,并且需要返回的列,在该索引中已经全部能够找到)
> - 在执行计划的explain中的extra列中,如果出现using index condition 说明查询使用到了索引,但是需要回表查询 一般来说性能会慢一点
> 
回表查询的意思,先根据索引在二级索引中查询,查询到之后再到聚集索引中找具体数据

> 前缀索引
> 当字段类型为字符串时,有时候索引需要很长的字符串,这会让索引变得很大,查询时会浪费大量的磁盘io,影响查询效率.这种情况下,
> 可以只将字符串的一部分前缀建立索引,这样可以大大节约索引空间,从而提高索引效率.
> 前缀长度的确认:
> 可以根据索引的选择性来决定,而选择性是指不重复的索引值(基数)和数据表的记录综述的比值.索引选择性越高则查询效率越高,
> 唯一索引的选择性是1,这是最好的索引选择性,性能也是最好的.
>   	选择性计算公式: count(distinct substring(想要使用前缀索引的列,1,9))/count(*) 如果计算下来是1,那么就极为合适

```sql
create index idx_xxx on table_name(column(n)); # 这里n指的就是截取的前n个字符串
```
> 单列索引和联合索引的使用规则
> 如表中 给name 和 phone 同时添加了单列索引 进行SQL语句查询 select id,phone,name from user where phone = 'xx' and name = 'xx'
> 对该SQL进行执行计划查询,发现最后走的是phone的单列索引,name的单列索引失效了,由于使用的索引中未包含name,那么势必导致回表查询.
> 如果这里使用联合索引,MySQL优化器最终还是只会选择phone的单列索引,此时需要我们进行SQL提示,要求优化器使用联合索引,就不会进行回表查询.
> 在实际业务场景中,如果存在多个查询条件,那么推荐使用联合索引.

## 7.索引设计原则

- 针对于数据量较大 且查询较频繁的表建立索引 (百万条级别)
- 针对于常作为查询条件 where 排序条件 order by 分组 group by 操作的字段建立索引
- 尽量选择区分度高的列作为索引,尽量建立唯一索引,区分度越高,使用索引的效率越高
   - 用户的身份证号,每个人的都不同,所以区分度高
   - 状态 性别字段 存在大量重复 所以区分度低
- 如果是字符串类型的字段,如果字段较长,可以针对字段特点建立前缀索引
- 尽量使用联合索引,减少单列索引 ,查询时,联合索引喝多时候可以覆盖索引,节省存储空间,避免回表,提高查询效率
- 要控制索引的数量,索引并不是多多益善,索引越多,维护索引的代价就越大 会影响增删改的效率
- 如果索引列不能存储null值,建表的时候使用not null进行约束,MySQL优化器知道每列是否包含null 时,可以更好地确定那个索引最有效的用于查询.
# 3 其他SQL语句的优化
## 1.插入数据优化
```sql
#1. 批量插入 每次单条数据的插入,都会进行数据库连接客户端权限校验这些开销,会比较慢 可以进行批量插入,但是一次以500-1000条数据为宜
#2. 使用手动提交事务 如果使用了大量的批量插入语句,每条批量插入语句开始和结束之后,都会进行自动的事务开启和提交 如果我们手动开启
  事务提交,这样大量数据也只会进行一次事务开启和提交,省去了多次进行开启事务和提交事务的操作时间
#3. 主键顺序插入 因为主键顺序插入性能要高于乱序插入的性能

# 如果数据量特别大,可能涉及到百万级别的数据,那么使用insert语句就性能过低,可以使用MySQL提供的load指令进行插入.操作如下:
# 1 客户端连接服务端时,加上参数 --local-infile
mysql --local-infile -u root -p
# 2 设置全局参数local_file为1,开启从本地加载文件导入数据的开关
set global local_infile = 1;
# 3 执行load指令将准备好的数据 加载到表结构中
load data local infile 'root/sql1.log' into table tb_user fields terminated by ',' lines terminated by '\n';

mysql> load data local infile '/Users/miaozesheng/Library/dayday_study/文档/MySQL相关/sql1.log' 
into table user fields terminated by ',' lines terminated by '\n';
```
## 2.主键优化

```sql
# 数据组织方式 
在innodb存储引擎中,表数据都是根据主键顺序组织存放的,这种存储方式的表称为索引组织表(index organized table IOT)

新增数据时 主键乱序的情况,会出现页分裂的现象.
数据删除时 可能出现也合并的情况

# 主键的设计原则
1. 满足业务场景的情况,主键长度要尽量短
 原因在于主键会作为聚集索引,而二级索引下会挂主键,如果主键过长,那么就会导致二级索引会占用较大的存储空间,同时磁盘io也会比较费时
2. 插入数据时,尽量选择顺序插入,选择使用autocrement自增主键 避免页分裂
3. 尽量不要选择uuid做主键或者其他自然主键 如身份证号 
  原因还是uuid和身份证号可能会存在页分裂,而且它们而且无序且长度较长,和上述两个原则都违背.
4. 业务操作时,避免对主键的修改.
  修改会带来主键索引的调整.
```
## 3.order by优化

```sql
MySQL中两种order by形式
1.Using filesort:通过表的索引或者全表扫描,读取满足条件的数据行,然后在排序缓冲区sort buffer中完成排序操作
所有不是通过索引直接返回的排序结果的排序都是filesort排序.
2.Using index:通过有序索引顺序扫描直接返回有序数据,这种情况即为using index 不需要额外排序,操作效率高

order by 后边跟的字段,如果添加了联合索引 如idx_user_age_phone 下边两个语句的执行效率是不一致的,满足最左前缀法则的效率更高.
select id , age ,phone from user order by age , phone;
select id , age ,phone from user order by phone ,age;

# 优化方式
# 1.根据排序字段建立合适的索引,多字段排序时,要满足最左前缀法则
# 2.尽量使用覆盖索引
# 3.多字段排序 一个升序一个降序 建立联合索引时要指定好
# 4.如果不可避免的出现filesort,大数据量排序时,可以适当增大排序缓冲区sort_buffer_size(默认256k)

select @@sort_buffer_size;
show variables like 'sort_buffer_size';
```
## 4.group by 优化
```sql
1.在分组操作是,可以通过建立索引来提高效率
2.分组操作时,索引的使用也是满足最左前缀法则的
```

## 5.limit优化
```json
在大数据量下,如: 同样查询十条数据,从上往下耗时越来越长,而且耗时会长很多.如10000000,10 此时MySQL需要排序前一千万零十条数据,
仅仅返回后十条数据,其他记录全部丢弃,查询排序的代价非常大.

selct * from user limit 0,10;
select * from user limit 100000,10;
select * from use limit 10000000,10;

官方给的优化建议:使用覆盖索引加子查询的形式 
# 该语句低版本MySQL可能不支持 子查询中不允许使用limit语句
select * from user where id in (select id from user order by id limit 10000000,10)
# 我们可以通过表联查的形式实现
select u.* from user u , (select id from user order by id limit 10000000,10) a where a.id = u.id;
```

## 6.count优化

```sql
explain select count(*) from user;
MyISAM引擎吧一个表的总行数存在了磁盘上,因此执行count(*)的时候会直接返回个数,效率很高.但是如果加了where条件,那也芭比Q了
InnoDB比较麻烦,执行count(*)的时候,会把数据一行一行的读出来,然后累计计数.很傻很天真.

优化思路:自己计数.
如使用redis存储数据总数,该表每次插入数据或者删除数据,redis中对应该表的count数据就变化,使用的时候直接去redis数据即可.

count()对于返回的结果集,一行行地判断,如果count函数的参数不是null,累计值就加一,否则不加,最后返回累计值.
count(*) 求取某张表中的总记录值
  InnoDB不会取出所有字段,而是专门做了优化,不取值,服务层直接按行进行累加
count(主键) 也是求表中的总记录数 不判断主键是否为null
  具体做法:存储引擎innodb在引擎层 遍历整张表,取出每一行的主键id,返回给服务层 直接进行累加
count(字段) 求表中该字段不为null的记录数 
  首先判断有没有not null约束 没有还要判断是否值为null 需要把每一行的值取出来 服务层进行判断和累加
count(1) 相当于对于每条数据给个字段,赋值为1然后统计总记录数
  InnoDB遍历整张表,但是不取值,服务层对于返回的每一行放一个数字1进去,直接按行进行累加
  
count(*)=count(1)>count(主键)>count(字段)
```
## 7.update优化
```sql
# 我们在更新数据的时候,最好根据索引字段作为where条件,不然mysql innodb的行级锁,会直接提升为表锁,导致更新不同事务的更新被阻塞
InnoDB的行锁是针对于索引加的锁,而不是针对于数据加的锁,并且该索引不能失效,否则行锁就会升级为表锁.
```
# 4. 锁
## 1.锁的分类
> 按照粒度:
> - 全局锁
> - 表级锁
>    - 表锁 直接锁住整个表的数据
>    - 元数据锁 解决插入记录时表结构发生变化的情况
>    - 意向锁 解决行锁和表锁的冲突
> - 行级锁
>    - 行锁 普通的一行记录上锁
>    - 间隙锁 主键和主键之间的间隙上锁
>    - 临键锁 行锁+临键锁

```sql
介绍:全局锁就是对整个数据库实例加锁,加锁后整个实例就处于制度状态,后续的DML的写语句 DDL语句,已经根据操作的事务提交语句都将被阻塞.
提典型的使用场景是做全库的逻辑备份,对所有表进行锁定,从而获取一致性视图,保证数据的完整性.
```
