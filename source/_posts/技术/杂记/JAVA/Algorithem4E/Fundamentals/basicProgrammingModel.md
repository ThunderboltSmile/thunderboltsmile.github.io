---
title: 《Algorithms》基础篇--基本编程模式
date: 2017-08-18
categries:
  - 算法
  - 读书笔记
tags: 
  - 随笔
  - JAVA
---
今天开始复习数据结构与算法方面的知识，使用的教材是《Algorithms》第四版，希望在这个过程中学习到有用的知识。顺便也温习一下JAVA语言。

#### Java程序的基本结构
一个Java程序通常包含一系列的静态方法和数据类型的声明，不论是Java还是其他的现代编程语言，我们使用以下五个component来编写程序：
* Primitive data types 准确地定义程序中的整型，数字，和布尔值的意义。
* Statement 允许我们创建一个变量并赋予其值，控制执行流或造成单因子影响，我们使用六种声明方式：declarations, assignments, conditionals, loops, calls, and returns
* Arrays 允许我们使用同一类型的多个值
* Static methods 允许我们封装并重用编码，以相互独立的模块来构建程序。
* Strings 是一串字符序列，java内置了一些针对strings的操作
* Input/Output 可以使程序和外部世界相联系。
* Data abstraction 的扩展封装和重用允许我们自定义数据类型，以支持面向对象的编程方式。

运行一个java程序涉及到与操作系统或程序运行环境的交互，为了清晰和经济，我们在一种叫做叫做虚拟终端的环境下描述这些行为。
触发一个Java程序，我们首先使用javac命令类对齐进行编译，然后使用java命令使其运行。

#### Primitive data types and expressions
数据结构是值和对值的操作的集合，java语言中基本的数据类型包含以下四种：
* int 整型+算术操作符(int)
* real numbers 实数+算数操作符(double)
* Booleans 布尔值+逻辑运算符(boolean)
* Characters 字母和符号(char)
##### 类型转换
将double转换成int时，会将小数部分去除而不是四舍五入。最好的实践是只同时对同一种类型的数据做操作。

#### Arrays
>An array stores a sequence of values that are all of the same type. 
The method that we use to refer to individual values in an array is numbering and then indexing them

##### Creating and initializing an array. 

* Declare the array name and type 
* Create the array
* Initialize the array values.

```java
double[] a;//声明类型和名字
a = new double[N];//创建一个数组
//or 
//double[] a = new double[N];
//or
//int[] a = { 1, 1, 2, 3, 5, 8 };
```
> the default initial value of variables of type double in a Java array is 0.0
The default initial value is zero for numeric types and false for type boolean. 

##### Using an array
>After declaring and creating an array, you can refer to any individual value anywhere `you would use a variable name `in a program by `enclosing an integer index in square brackets` after the array name. Once we create an array, its size is fixed. A program can refer to the length of an array a[] with the code a.length. 

注意：数组名是对数组位置的记录(指针类型)，如果数组名字发生传递，则在传递后仍然指向同一数组，如下：
```java
int[] a = new int[N];
a[i] = 1234;
int[] b = a;
b[i] = 5678; // a[i] is now 5678.
```

##### Two-dimensional arrays
>A two-dimensional array in Java is `an array of one-dimensional arrays`. A two-dimensional array may be ragged (its arrays may all be of differing lengths), but we most often work with (for appropriate parameters M and N) M-by-N two-dimensional arrays that are arrays of M rows, each an array of length N (so it also makes sense to refer to the array as having N columns). 

> to create the array,we specify the number of rows followed by the number of columns after the type name (both within square brackets):
    `double[][] a = new double[M][N];`

整数类型默认初始化为0，布尔值则默认为false。



