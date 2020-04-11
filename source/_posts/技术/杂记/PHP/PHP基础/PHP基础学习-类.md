---
title: PHP基础学习--类和对象
date: 2017-07-13
categories:
  - PHP
tags:
  - 基础知识
---

类和对象是语言用于构建程序的基本的component，PHP中的对象和类也不例外，我们可以将相关的操作通过类封装成不同的模块加以复用，即可构建出强大灵活的应用程序。
<!-- more -->
#### 定义类及其方法
```php
<?php
//定义一个类
class Car {
    var $name = '汽车';
    function getName() {
        return $this->name;
    }
}
//实例化一个car对象
$car = new Car();
$car->name = '奥迪A6'; //设置对象的属性值
echo $car->getName();  //调用对象的方法 输出对象的名字
?>
```
#### public、protected、private属性的区别
在声明类属性时，默认都为public，外部可以访问。一般通过`->`对象操作符来访问对象的属性或者方法，对于静态属性则使用`::`双冒号进行访问。当在类成员方法内部调用的时候，可以使用`$this`伪变量调用当前对象的属性。
受保护的属性(protected)与私有属性(private)不允许外部调用，在类的成员方法内部是可以调用的。
```php
<?php
class Car{
    public $name="汽车";
    protected $color="白色";
    private $price="1000000";
    public getColor(){
        return $this->color;
    }
}
$car=new Car();
echo $car->name;
echo $car->getColor();//"白色",类的成员方法可以调用内部属性
echo $car->color;//出错
```

#### 方法、静态方法
方法就是在类中的function，很多时候我们分不清方法与函数有什么差别，在面向过程的程序设计中function叫做函数，在面向对象中function则被称之为方法。使用关键字`static`修饰的，称之为静态方法，静态方法不需要实例化对象，可以通过类名直接调用，操作符为双冒号`::`。
```php
<?php
class Car {
    public static function getName() {
        return '汽车';
    }
​}
echo Car::getName(); //结果为“汽车”
?>
```
#### 构造函数、析构函数
在类中使用`__construct()`定义一个构造函数，具有构造函数的类，会在每次对象创建的时候调用该函数，因此常用来在对象创建的时候进行一些初始化工作。
在子类中如果定义了`__construct`则不会调用父类的`__construct`，如果需要同时调用父类的构造函数，需要使用`parent::__construct()`显式的调用。
```php
<?php
class Car {
   function __construct() {
       print "父类构造函数被调用\n";
   }
}
class Truck extends Car {
   function __construct() {
       print "子类构造函数被调用\n";
       parent::__construct();
   }
}
$car = new Truck();
?>
```
析构函数使用`__destruct()`进行定义,析构函数指的是当某个对象的所有引用被删除，或者对象被显式的销毁时会执行的函数。
```php
<?php
class Car {
   function __construct() {
       print "构造函数被调用 \n";
   }
   function __destruct() {
       print "析构函数被调用 \n";
   }
}
$car = new Car(); //实例化时会调用构造函数
echo '使用后，准备销毁car对象 \n';
unset($car); //销毁时会调用析构函数
?>
```
#### static关键字
static用于定义类中的静态属性和方法。静态属性与方法可以在不实例化类的情况下调用，直接使用类名`::`方法名的方式进行调用。静态属性不允许对象使用`->`操作符调用。
静态方法中，`$this`伪变量不允许使用。可以使用`self，parent，static`在内部调用静态方法与属性。
```php
<?php
class Car {
    private static $speed = 10;
    
    public static function getSpeed() {
        return self::$speed;//不允许使用$this
    }
    
    public static function speedUp() {
        return self::$speed+=10;
    }
}
class BigCar extends Car {//继承了Car
    public static function start() {
        parent::speedUp();
    }
}
$func = 'getSpeed';
BigCar::start();
echo BigCar::$func();//可以使用引用类型来表示方法名
?>
```
#### 访问控制
被定义为public的类成员可以在任何地方被访问。被定义为protected的类成员则可以被其自身以及其子类和父类访问。被定义为private的类成员则只能被其定义所在的类访问。若方法采用`var`定义，则被视为公有。
如果构造函数定义成了私有方法，则不允许直接实例化对象了，这时候一般通过静态方法进行实例化，在设计模式中会经常使用这样的方法来控制对象的创建，比如单例模式只允许有一个全局唯一的对象。
```php
<?php
class Car {
    private function __construct() {
        echo 'object create';
    }

    private static $_object = null;
    public static function getInstance() {
        if (empty(self::$_object)) {
            self::$_object = new Car(); //内部方法可以调用私有方法，因此这里可以创建对象
        }
        return self::$_object;
    }
}
//$car=new Car();//这里不允许直接实例化对象
$car=Car::getInstance();//通过静态方法来获得一个实例
?>
```
#### 重载
PHP中的重载指的是动态的创建属性与方法，是通过魔术方法来实现的。属性的重载通过`__set`，`__get`，`__isset`，`__unset`来分别实现对不存在属性的赋值、读取、判断属性是否设置、销毁属性。
```php
<?php
class Car {
    private $ary=array();
    public function __set($key, $val){
        $this->ary[$key] = $val;
    }
    public function __get($key){
        if (isset($this->ary[$key])){
            return $this->ary[$key];
        }
        return null;
    }
    public function __isset($key){
        if (isset($this->ary[$key])){
            return true;
        }
        return false;
    }
    public function __unset($key){
        unset($this->ary[$key]);
    }
}
$car = new Car();
$car->name = '汽车';//name属性动态创建并赋值
echo $car->name;//'汽车'
?>
```
方法的重载通过`__call`来实现，当调用不存在的方法的时候，将会转为参数调用`__call`方法，当调用不存在的静态方法时会使用`__callStatic`重载。
```php
<?php
class Car {
    public $speed = 0;
    public function __call($name,$args){
        if($name=='speedUp'){
            $this->speed+=10;
        }
    }
}
$car=new Car();
$car->speedUp(); //调用不存在的方法会使用重载
echo $car->speed;//10
?>
```
#### 比较、复制、序列化
##### 比较
当同一个类的`两个实例的所有属性都相等`时，可以使用比较运算符`==`进行判断，当需要判断`两个变量是否为同一个对象的引用`时，可以使用全等运算符`===`进行判断。
##### 复制
在一些特殊情况下，可以通过关键字`clone`来复制一个对象，这时`__clone`方法会被调用，通过这个魔术方法来设置属性的值。
```php
<?php
class Car {
    public $name='car';
    public function __clone(){
        $obj=new Car();
        $obj->name=$this->name;
    }
}
$a=new Car();
$a->name='new car';
$b=clone $a;
var_dump($b);
?>
```
##### 序列化
可以通过`serialize`方法将对象序列化为字符串，用于`存储或者传递数据`，然后在需要的时候通过`unserialize`将字符串反序列化成对象进行使用。
```php
<?php
class Car {
    public $name = 'car';
    
    public function __clone() {
        $obj = new Car();
        $obj->name = $this->name;
    }
}
$a = new Car();
$str = serialize($a); //对象序列化成字符串
echo $str.'<br>';
$c = unserialize($str); //反序列化为对象
var_dump($c);
?>
```
输出
```shell
O:3:"Car":1:{s:4:"name";s:3:"car";}
object(Car)#3 (1) {
["name"]=>
string(3) "car"
}
```
感觉这个序列化貌似并不常用，即使序列化了也还是只有PHP的反序列化方法可以识别并反序列化。
