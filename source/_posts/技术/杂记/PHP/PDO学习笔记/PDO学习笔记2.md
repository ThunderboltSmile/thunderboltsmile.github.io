---
title: PDO方法简单使用
date: 2017-08-015
categories:
  - PHP
tags:
  - PDO
  - MYSQL
---

PDO提供了许多的方法可以让我们操作数据,主要有:exec(),query(),prepare(),quote(),lastInsertId,setAttribute(),getAttribute(),errorCode(),errorInfo()。
<!--more-->

#### exec()
执行一条SQL语句，并返回受其影响的条数
>`PDO::exec()` executes an SQL statement in a single function call, returning the number of rows affected by the statement.
`PDO::exec()` does not return results from a `SELECT` statement. For a SELECT statement that you only need to issue once during your program, consider issuing `PDO::query()`. For a statement that you need to issue multiple times, prepare a PDOStatement object with `PDO::prepare()` and issue the statement with `PDOStatement::execute()`.

返回值
>`PDO::exec()` returns the number of rows that were modified or deleted by the SQL statement you issued. If no rows were affected, `PDO::exec()` returns 0.

实例：
```php
$sql=<<<EOF
    //your sql here
EOF;
   $res=$pdo->exec($sql);
   echo $res;
```
#### errorCode()&&errorInfo()

>PDO::errorCode() only retrieves error codes for operations performed directly on the database handle. If you create a PDOStatement object through PDO::prepare() or PDO::query() and invoke an error on the statement handle, PDO::errorCode() will not reflect that error. You must call PDOStatement::errorCode() to return the error code for an operation performed on a particular statement handle.

也就是说，errorCode仅仅返回直接作用于数据库的语句产生的错误码，如果想要得到由prepare、query等statement产生的错误码，应该使用`PDOStatement::errorCode()`

>PDO::errorInfo() returns an array of error information about the last operation performed by this database handle. 

errorInfo返回的则是包含具体语法错误的数组，如果想要获取详细的报错信息，可以使用该方法。

#### query()
>PDO::query — Executes an SQL statement, returning a result set as a PDOStatement object

实例：
```php
<?php
function getFruit($conn) {
    $sql = 'SELECT name, color, calories FROM fruit ORDER BY name';
    foreach ($conn->query($sql) as $row) {
        print $row['name'] . "\t";
        print $row['color'] . "\t";
        print $row['calories'] . "\n";
    }
}
?>
```
#### setAttribute
设置连接属性，比如自动提交，错误模式、预处理语句的模拟等，相应的配置可以由`getAttribute()`方法获得。
相关属性见[PDO::setSttribute](http://php.net/manual/zh/pdo.setattribute.php)

#### quote()
`Syntax:public string PDO::quote ( string $string [, int $parameter_type = PDO::PARAM_STR ] )`
>PDO::quote() places quotes around the input string (if required) and escapes special characters within the input string, using a quoting style appropriate to the underlying driver.
If you are using this function to build SQL statements, you are strongly recommended to use PDO::prepare() to prepare SQL statements with bound parameters instead of using PDO::quote() to interpolate user input into an SQL statement. Prepared statements with bound parameters are not only more portable, more convenient, immune to SQL injection, but are often much faster to execute than interpolated queries, as both the server and client side can cache a compiled form of the query.

也就是说，该方法接收字符串类型的sql语句并进行处理，返回处理后的结果。但是官方提醒是不建议使用，请使用prepare，因为prepare “not only more portable, more convenient, immune to SQL injection, but are often much faster to execute than interpolated queries”。

#### prepare()
`public PDOStatement PDO::prepare ( string $statement [, array $driver_options = array() ] )`
这可能是最常用的方法了，准备一条执行语句，返回statement对象或者在准备出错时返回false;

>Prepares an SQL statement to be executed by the PDOStatement::execute() method. The SQL statement can contain zero or more named (:name) or question mark (?) parameter markers for which real values will be substituted when the statement is executed. You cannot use both named and question mark parameter markers within the same SQL statement; pick one or the other parameter style. Use these parameters to bind any user-input, do not include the user-input directly in the query.
You must include a unique parameter marker for each value you wish to pass in to the statement when you call PDOStatement::execute(). You cannot use a named parameter marker of the same name more than once in a prepared statement, unless emulation mode is on.

就是说，在同一条sql语句中，“:name”和“？”不能同时出现，如果使用":name"，那么同一个名字不能出现两次，除非枚举模式开启。
实例1：使用":name"
```php
<?php
/* Execute a prepared statement by passing an array of values */
$sql = 'SELECT name, colour, calories
    FROM fruit
    WHERE calories < :calories AND colour = :colour';
$sth = $dbh->prepare($sql, array(PDO::ATTR_CURSOR => PDO::CURSOR_FWDONLY));
$sth->execute(array(':calories' => 150, ':colour' => 'red'));
$red = $sth->fetchAll();
$sth->execute(array(':calories' => 175, ':colour' => 'yellow'));
$yellow = $sth->fetchAll();
?>
```
实例2：使用"?"
```php
/* Execute a prepared statement by passing an array of values */
$sth = $dbh->prepare('SELECT name, colour, calories
    FROM fruit
    WHERE calories < ? AND colour = ?');
$sth->execute(array(150, 'red'));
$red = $sth->fetchAll();
//or you can use $red = $sth->fetchAll(PDO::FETCH_ASSOC), which will return an object
//you also use $res->setFetchMode(PDO::FETCH_ASSOC) to get an object
$sth->execute(array(175, 'yellow'));
$yellow = $sth->fetchAll();
?>
```
我更喜欢用第二种方式,毕竟。。。命名可是软件工程的三大难题之一呵呵