---
title: PHP基础学习--数组
date: 2017-07-012
categories:
  - PHP
tags:
  - 基础知识
---
数组是非常常见的数据结构之一，脱胎于堆栈这类的基本数据结构，表示一列数据的集合，在强类型语言中，同一数组的值必须保持一致比如java,c等，但在脚本语言中，类型被弱化，数组的值可以是任意的数据结构的组合。
<!-- more -->
#### 初始化一个空数组
```php
<?php
$arr=array()
?>
```
#### 索引数组
以元素所在的位置作为键名的数组
```php
<?php
//创建一个索引数组，索引数组的键是“0”，值是“苹果”
$fruit=array("苹果");
//$fruit=arry('0'=>'苹果');
print_r($fruit[0]);
?>
```
#### 索引数组的循环
```php
<?php
$fruit=array('苹果','香蕉','菠萝');
for($index=0; $index<3; $index++){
    echo '<br>数组第'.$index.'值是:'.$fruit[$index];
}
foreach($fruit as $key=>$value){
    echo '<br>第'.$key.'值是：'.$value;
}
?>
```
#### 关联数组
初始化关联数组
```php
<?php
$fruit = array(
    'apple'=>"苹果",
    'banana'=>"香蕉",
    'pineapple'=>"菠萝"
); 
?>
```
php的数组还有许多内置的方法，非常好用，PHP的强大之处正在于其函数库功能非常全面见：[php数组 函数](http://php.net/manual/en/book.array.php)
