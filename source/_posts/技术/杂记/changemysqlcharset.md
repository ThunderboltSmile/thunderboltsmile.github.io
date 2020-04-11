---
title: 修改mariadb数据库字符集为utf8
categories:
  - linux
tags: 
  - db
  - charset
---

为了让Mariadb支持中文，还是要做不少工作的,尤其是在已经创建了数据表发现错误的情况下,因为数据库和数据表的编码方式在创建时已经确定（没有指定则为默认字符集）,因此修改配置文件对已经创建的数据库和数据表不会生效

<!-- more -->

### 修改已经生成的数据

#### 修改数据库字符集

```shell
ALTER DATABASE dbname DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
```

#### 修改表的编码方式

```shell
ALTER TABLE `test` DEFAULT CHARACTER SET utf8;
#该命令用于将表test的编码方式改为utf8；
```

#### 修改表中字段字符集

```shell
ALTER TABLE `tbname` CHANGE `keyname` `keyname` VARCHAR(20) CHARACTER SET utf8 NOT NULL;
#该命令用于将表ttname字段keyname的编码方式改为utf8
```

### 修改数据库配置

#### 修改my.cnf

```bash
[client]
default-character-set=utf8  #添加此行

[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
#添加以下2行
character-set-server=utf8      
init_connect='SET NAMES utf8'

[mysql]
no-auto-rehash
default-character-set=utf8     #添加此行

[mysqld_safe]
log-error=/var/log/mariadb/mariadb.log
pid-file=/var/run/mariadb/mariadb.pid
```


#### 重启服务

```shell
service mariadb  restart
```

#### 查看字符集

```shell
mysql -uroot -p

show variables like 'character_set%';

show create table 表名;  #查看表的字符编码
```

### pdo中文乱码问题

在使用pdo连接数据库时，应该指定编码格式

```shell

$pdo = new PDO("mysql:host=localhost;dbname=data1;charset=utf8;","root","root");

```

### 避免导入数据有中文乱码的问题

#### 将数据编码格式保存为utf-8

设置默认编码为utf8：

```shell
set names utf8;
```

设置数据库db_name默认为utf8:

```shell
ALTER DATABASE `db_name` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
```

设置表tb_name默认编码为utf8:

```shell
ALTER TABLE `tb_name` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
```

导入：

```shell
LOAD DATA LOCAL INFILE '/path/to/data/file' INTO TABLE yjdb;
```

#### 将数据编码格式保存为ansi(即GBK或GB2312)

设置默认编码为gbk：

```shell
set names gbk;
```

设置数据库db_name默认编码为gbk:

```shell 
ALTER DATABASE `db_name` DEFAULT CHARACTER SET gbk COLLATE gbk_chinese_ci;
```

设置表tb_name默认编码为gbk:

```shell
ALTER TABLE `tb_name` DEFAULT CHARACTER SET gbk COLLATE gbk_chinese_ci;
```

导入：

```shell
LOAD DATA LOCAL INFILE 'C:\\gbk.txt' INTO TABLE yjdb;
```

注:
1.UTF8不要导入gbk，gbk不要导入UTF8;
2.dos下不支持UTF8的显示;