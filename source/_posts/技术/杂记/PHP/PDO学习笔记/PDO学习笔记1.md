---
title: PDO简介
date: 2017-08-014
categories:
  - PHP
tags:
  - PDO
  - MYSQL
---

### PDO简介

在PHP开发中，与数据库的交互始终是个绕不开的话题，PHP虽然原生支持许多中数据库，但是对数据库的具体支持程度并不太理想，而PDO`PHP Data Objects`则是我们在大多数场景下使用的数据库连接工具。

>摘自PHP手册：
The PHP Data Objects (PDO) extension defines a lightweight, consistent interface for accessing databases in PHP. Each database driver that implements the PDO interface can expose database-specific features as regular extension functions.
regardless of which database you're using, you use the same functions to issue queries and fetch data.

也就是说，PDO提供了一个操作数据库的统一接口，我们可以使用相同的方式去执行SQL命令而不需要关心面对的是什么类型的数据库。
<!-- more -->
### 安装
在安装好mariadb，mysql等软件后，安装与当前PHP版本相符的PDO，在php.ini中打开相应的扩展即可，如：
```bash
;;;;;;;;;;;;;;;;;;;
; extnsions ;
;;;;;;;;;;;;;;;;;;;

extension=mysql.so
extension=pdo.so
```

### 预定义常量
这里仅就某几个常用的常量作简要介绍
#### PDO::FETCH_LAZY(integer)
>Specifies that the fetch method shall return each row as an object with variable names that correspond to the column names returned in the result set.

即返回一个描述了结果集信息的对象，以列名为键名，以列值为键值。
#### PDO::FETCH_ASSOC(integer)
>Specifies that the fetch method shall return each row as an array indexed by column name as returned in the corresponding result set. If the result set contains multiple columns with the same name, PDO::FETCH_ASSOC returns only a single value per column name.

将结果集的每一行返回一个以列名作为索引，列值作为数组元素的值的数组，倘若同一结果集中存在相同列名的两列数据，该方法仅能返回其中一列。
#### PDO::FETCH_NAMED(integer)
>Specifies that the fetch method shall return each row as an array indexed by column name as returned in the corresponding result set. If the result set contains multiple columns with the same name, PDO::FETCH_NAMED returns an array of values per column name.

基本功能与`PDO::FETCH_ASSOC`相似，只不过在存在多列结果集重名时，以该重名键名对应的数组值为一个数组，该数组包含了结果集中以此键名为名的结果。

#### PDO::FETCH_NUM(integer)
>Specifies that the fetch method shall return each row as an array indexed by column number as returned in the corresponding result set, starting at column 0.

将结果集的每一行以数值索引（按照列的排列顺序）为键名的数组的形式返回。也就是你不知道该结果每一个值代表的是什么意义，只能知道其所在的列在表中的位置。
#### PDO::FETCH_BOTH(integer)
>Specifies that the fetch method shall return each row as an array indexed by both column name and number as returned in the corresponding result set, starting at column 0.

返回一个索引数组，以列明和列序为键名表达同一列的值，结果相间排列。即`[0]=>value0,[name0]=>value0,[1]=>value1,[name1]=>value1`。
#### PDO::FETCH_OBJ(integer)
>Specifies that the fetch method shall return each row as an object with property names that correspond to the column names returned in the result set.

将结果集的每一行以以列名为键名，以列值为键值的对象的形式返回。

### 通过PDO连接数据库
```php
<?php
//在此载入配置文件中的用户名、密码等数据库配置信息
$dsn='mysql:host=localhost;dbname=mydb;charset=utf8';
$user='root';
$pass='pass';
$dbh = new PDO($dsn, $user, $pass);
// 在此使用连接


// 现在运行完成，在此关闭连接
$dbh = null;
?>
```
