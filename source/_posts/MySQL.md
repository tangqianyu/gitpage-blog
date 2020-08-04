---
title: MySQL基础
date: 2020-07-29 22:12:00
tags: 基础
categories: MySQL
---

## 1、初识MySQL

### 1.1、为什么学习数据库

1、岗位技能需求
2、现在的世界,得数据者得天下
3、存储数据的方法
4、程序,网站中,大量数据如何长久保存?
5、**数据库是几乎软件体系中最核心的一个存在**。

### 1.2、什么是数据库

数据库 ( **DataBase** , 简称**DB** )

**概念** : 长期存放在计算机内,有组织,可共享的大量数据的集合,是一个数据 "仓库"

**作用** : 保存,并能安全管理数据(如:增删改查等),减少冗余...

**数据库总览** :

- 关系型数据库 ( SQL )
  + MySQL , Oracle , SQL Server , SQLite , DB2 , ...
  + 关系型数据库通过外键关联来建立表与表之间的关系
- 非关系型数据库 ( NOSQL )
  + Redis , MongoDB , ...
  + 非关系型数据库通常指数据以对象的形式存储在数据库中，而对象之间的关系通过每个对象自身的属性来决定

### 1.3、什么是DBMS

数据库管理系统 ( **D**ataBase **M**anagement **S**ystem )

数据库管理软件 , 科学组织和存储数据 , 高效地获取和维护数据

![640](640.png)

为什么要说这个呢?

因为我们要学习的MySQL应该算是一个数据库管理系统

### 1.4、MySQL简介

![740](740.png)

**概念** : 是现在流行的开源的,免费的 关系型数据库

**历史** : 由瑞典MySQL AB 公司开发，目前属于 Oracle 旗下产品。

**特点** :
  - 免费 , 开源数据库
  - 小巧 , 功能齐全
  - 使用便捷
  - 可运行于Windows或Linux操作系统
  - 可适用于中小型甚至大型网站应用

官网 : [https://www.mysql.com/](https://www.mysql.com/)

**这里建议大家使用压缩版,安装快,方便.不复杂**

**软件下载**

mysql5.7 64位下载地址:

[https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.19-winx64.zip](https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.19-winx64.zip])

电脑是64位的就下载使用64位版本的！

#### 1.4.1、安装步骤

1、下载后得到zip压缩包.

2、解压到自己想要安装到的目录，本人解压到的是D:\Environment\mysql-5.7.19

3、添加环境变量：我的电脑->属性->高级->环境变量

`选择PATH,在其后面添加: 你的mysql 安装文件下面的bin文件夹`

4、编辑 my.ini 文件 ,注意替换路径位置

```ini
[mysqld]
basedir=D:\Program Files\mysql-5.7\
datadir=D:\Program Files\mysql-5.7\data\
port=3306
skip-grant-tables
```

5、启动管理员模式下的CMD，并将路径切换至mysql下的bin目录，然后输入mysqld –install (安装mysql)

6、再输入  mysqld --initialize-insecure --user=mysql 初始化数据文件

7、然后再次启动mysql 然后用命令 mysql –u root –p 进入mysql管理界面（密码可为空）

8、进入界面后更改root密码

```shell
update mysql.user set authentication_string=password('123456') where user='root' and Host = 'localhost';
```

9、刷新权限

```shell
flush privileges;
```
10、修改 my.ini文件删除最后一句skip-grant-tables

11、重启mysql即可正常使用

```shell
net stop mysql
net start mysql
```

12、连接上测试出现以下结果就安装好了

![840](840.png)



## 2、数据库操作

### 2.1、结构化查询语句分类

![940](940.webp)

### 2.2、数据库操作

#### 2.2.1、命令行操作数据库

创建数据库 :  create database [if not exists] 数据库名;

删除数据库 : drop database [if exists] 数据库名;

查看数据库 : show databases;

使用数据库 : use 数据库名;


### 2.3、创建数据表

属于DDL的一种，语法 :

```sql
create table [if not exists] `表名`(
   '字段名1' 列类型 [属性][索引][注释],
   '字段名2' 列类型 [属性][索引][注释],
  #...
   '字段名n' 列类型 [属性][索引][注释]
)[表类型][表字符集][注释];

```
**说明** : 反引号用于区别MySQL保留字与普通字符而引入的 (键盘esc下面的键).

### 2.4、数据值和列类型

列类型 : 规定数据库中该列存放的数据类型

#### 2.4.1、数值类型

![1040](1040.webp)

#### 2.4.2、字符串类型

![1140](1140.webp)

#### 2.4.3、日期和时间型数值类型

![1240](1240.webp)

#### 2.4.4、NULL值

- 理解为 "没有值" 或 "未知值"

- 不要用NULL进行算术运算 , 结果仍为NULL

### 2.5、数据字段属性

<font color=red size=4>UnSigned</font>

- 无符号的

- 声明该数据列不允许负数

<font color=red size=4>ZEROFILL</font>

- 0填充的

- 不足位数的用0来填充 , 如int(3),5则为005

<font color=red size=4>Auto_InCrement</font>

- 自动增长的 , 每添加一条数据 , 自动在上一个记录数上加 1(默认)

- 通常用于设置主键 , 且为整数类型

- 可定义起始值和步长

  + 当前表设置步长(AUTO_INCREMENT=100) : 只影响当前表

  + SET @@auto_increment_increment=5 ; 影响所有使用自增的表(全局)

<font color=red size=4>NULL 和 NOT NULL</font>

- 默认为NULL , 即没有插入该列的数值

- 如果设置为NOT NULL , 则该列必须有值

<font color=red size=4>DEFAULT</font>

- 默认的

- 用于设置默认值

- 例如,性别字段,默认为"男" , 否则为 "女" ; 若无指定该列的值 , 则默认值为"男"的值  

```sql
-- 目标 : 创建一个school数据库
-- 创建学生表(列,字段)
-- 学号int 登录密码varchar(20) 姓名,性别varchar(2),出生日期(datatime),家庭住址,email
-- 创建表之前 , 一定要先选择数据库

CREATE TABLE IF NOT EXISTS `student` (
`id` int(4) NOT NULL AUTO_INCREMENT COMMENT '学号',
`name` varchar(30) NOT NULL DEFAULT '匿名' COMMENT '姓名',
`pwd` varchar(20) NOT NULL DEFAULT '123456' COMMENT '密码',
`sex` varchar(2) NOT NULL DEFAULT '男' COMMENT '性别',
`birthday` datetime DEFAULT NULL COMMENT '生日',
`address` varchar(100) DEFAULT NULL COMMENT '地址',
`email` varchar(50) DEFAULT NULL COMMENT '邮箱',
PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8

-- 查看数据库的定义
SHOW CREATE DATABASE school;
-- 查看数据表的定义
SHOW CREATE TABLE student;
-- 显示表结构
DESC student;  -- 设置严格检查模式(不能容错了)SET sql_mode='STRICT_TRANS_TABLES';
```

### 2.6、数据表的类型

#### 2.6.1、设置数据表的类型

```sql
CREATE TABLE 表名(
   -- 省略一些代码
   -- Mysql注释
   -- 1. # 单行注释
   -- 2. /*...*/ 多行注释
)ENGINE = MyISAM (or InnoDB)

-- 查看mysql所支持的引擎类型 (表类型)
SHOW ENGINES;
```
MySQL的数据表的类型 : **MyISAM** , **InnoDB** , HEAP , BOB , CSV等...

常见的 MyISAM 与 InnoDB 类型：

![1340](1340.webp)

经验 ( 适用场合 )  :

- 适用 MyISAM : 节约空间及相应速度

- 适用 InnoDB : 安全性 , 事务处理及多用户操作数据表

#### 2.6.2、数据表的存储位置

- MySQL数据表以文件方式存放在磁盘中

  + 包括表文件 , 数据文件 , 以及数据库的选项文件

  + 位置 : Mysql安装目录\data\下存放数据表 . 目录名对应数据库名 , 该目录下文件名对应数据表

- 注意 :

  + *.frm -- 表结构定义文件

  + *.MYD -- 数据文件 ( data )

  + *.MYI -- 索引文件 ( index )

  + InnoDB类型数据表只有一个 *.frm文件 , 以及上一级目录的ibdata1文件

  + MyISAM类型数据表对应三个文件 :  

![1440](1440.png)

#### 2.6.3、设置数据表字符集

- 我们可为数据库,数据表,数据列设定不同的字符集，设定方法 :

  + 创建时通过命令来设置 , 如 : CREATE TABLE 表名()CHARSET = utf8;

  + 如无设定 , 则根据MySQL数据库配置文件 my.ini 中的参数设定

### 2.7、修改数据库

#### 2.7.1、修改表 ( ALTER TABLE )

修改表名 :ALTER TABLE 旧表名 RENAME AS 新表名

添加字段 : ALTER TABLE 表名 ADD字段名 列属性[属性]

修改字段 :

- ALTER TABLE 表名 MODIFY 字段名 列类型[属性]

- ALTER TABLE 表名 CHANGE 旧字段名 新字段名 列属性[属性]

删除字段 :  ALTER TABLE 表名 DROP 字段名

#### 2.7.2、删除数据表

语法：DROP TABLE [IF EXISTS] 表名

- IF EXISTS为可选 , 判断是否存在该数据表

- 如删除不存在的数据表会抛出错误

#### 2.7.3、其他

1. 可用反引号（`）为标识符（库名、表名、字段名、索引、别名）包裹，以避免与关键字重名！中文也可以作为标识符！

2. 每个库目录存在一个保存当前数据库的选项文件db.opt。

3. 注释：
  单行注释 # 注释内容
  多行注释 /* 注释内容 */
  单行注释 -- 注释内容       (标准SQL注释风格，要求双破折号后加一空格符（空格、TAB、换行等）)
   
4. 模式通配符：
  _   任意单个字符
  %   任意多个字符，甚至包括零字符
  单引号需要进行转义 \'
   
5. CMD命令行内的语句结束符可以为 ";", "\G", "\g"，仅影响显示结果。其他地方还是用分号结束。delimiter 可修改当前对话的语句结束符。

6. SQL对大小写不敏感 （关键字）

7. 清除已有语句：\c


## DML语言 