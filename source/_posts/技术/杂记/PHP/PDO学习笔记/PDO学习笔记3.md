---
title: PDO事务
date: 2017-08-016
categories:
  - PHP
tags:
  - PDO
  - MYSQL
---
>Transactions are typically implemented by "saving-up" your batch of changes to be applied all at once; this has the nice side effect of drastically improving the efficiency of those updates. In other words, transactions can make your scripts faster and potentially more robust (you still need to use them correctly to reap that benefit).

PDO内置了开启事务的方法`PDO::beginTransaction() `，当开启事务后执行的命令全部有效时，执行`PDO::commit()`才会将最终的结果确定，
如果其中一个命令执行失败，则事务机制会将数据库的状态还原至事务开启之前。即调用` PDO::rollBack() `使得事务发生回滚。在使用PDO链接支持事务的数据库时，每一个操作都隐式地开启了一个事务，此时会执行自动提交，即autocommit，而对不支持事务的数据库引擎开启事务时，会报一个严重错误。
mysql需要将引擎设置为innodb才能够支持事务。
<!-- more -->
实例：
```php
<?php
try {
  $dbh = new PDO('odbc:SAMPLE', 'db2inst1', 'ibmdb2', array(PDO::ATTR_PERSISTENT => true));
  echo "Connected\n";
} catch (Exception $e) {
  die("Unable to connect: " . $e->getMessage());
}

try {  
  $dbh->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

  $dbh->beginTransaction();
  $dbh->exec("insert into staff (id, first, last) values (23, 'Joe', 'Bloggs')");
  $dbh->exec("insert into salarychange (id, amount, changedate) 
      values (23, 50000, NOW())");
  $dbh->commit();
  
} catch (Exception $e) {
  $dbh->rollBack();
  echo "Failed: " . $e->getMessage();
}
?>
```

一旦出错，就会调用回滚，所有参与事务的操作将无效。

