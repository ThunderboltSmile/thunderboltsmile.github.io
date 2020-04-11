---
title: PHP基础学习--session与cookie
date: 2017-07-015
categories:
  - PHP
tags:
  - 基础知识
---

### 简介

Cookie是存储在客户端浏览器中的数据,我们通过Cookie来跟踪与存储用户数据。一般情况下，Cookie通过HTTP headers从服务端返回到客户端。多数web程序都支持Cookie的操作，因为Cookie是存在于HTTP的标头之中，所以必须在其他信息输出以前进行设置，类似于header函数的使用限制。

PHP通过`setcookie`函数进行cookie的设置，任何从浏览器发回的cookie，PHP都会自动将其存储在`$_COOKIE`的全局变量中，我们可以`$_COOKIE[key]`的形式来读取某个cookie值。

HP中的Cookie具有非常广泛的使用，经常用来存储用户的登录信息，购物车等，且在使用会话Session时通常使用Cookie来存储会话id来识别用户，Cookie具备有效期，当有效期结束之后，Cookie会自动的从客户端删除。同时为了进行安全控制，Cookie还可以设置域跟路径。
<!-- more -->
### setcookie

>setcookie() defines a cookie to be sent along with the rest of the HTTP headers. Like other headers, cookies must be sent before any output from your script (this is a protocol restriction). This requires that you place calls to this function prior to any output, including <html> and <head> tags as well as any whitespace.

```php
<?php
$value = 'something from somewhere';
setcookie("TestCookie", $value);
setcookie("TestCookie", $value, time()+3600);  /* expire in 1 hour */
setcookie("TestCookie", $value, time()+3600, "/~rasmus/", "example.com", 1);
?>
```
参数:
* cookie name
* cookie value
* The time the cookie expires. This is a Unix timestamp .you'll most likely set this with the time() function plus the number of seconds before you want it to expire.
* The path on the server in which the cookie will be available on.
* The (sub)domain that the cookie is available to.

如果想要删除cookie，将过期时间设为当前时间之前，value设为空即可。`setcookie('test', '', time()-1); `或者

    header("Set-Cookie:test=1393832059; expires=".gmdate('D, d M Y H:i:s \G\M\T', time()-1));

#### session or cookie ?

cookie:存储在客户端，可以被任意篡改，大小有限制。
session:存储在服务端，无大小限制，通过session_id进行用户识别，PHP的session_id在默认情况下通过cookie进行保存。

```php
?php
//开始使用session
session_start();
//设置一个session
$_SESSION['test'] = time();
//显示当前的session_id
echo "session_id:".session_id();
echo "<br>";

//读取session值
echo $_SESSION['test'];

//销毁一个session
unset($_SESSION['test']);
echo "<br>";
var_dump($_SESSION);
?>
```

默认情况下，session是以文件形式存储在服务器上的，因此当一个页面开启了session之后，会独占这个session文件，这样会导致`当前用户`的其他并发访问无法执行而等待。可以采用缓存或者数据库的形式存储来解决这个问题。

#### 使用session保存用户信息

```php
<?php
session_start();
//假设用户登录成功获得了以下用户数据
$userinfo = array(
    'uid'  => 10000,
    'name' => 'spark',
    'email' => 'spark',
    'sex'  => 'man',
    'age'  => '18'
);
header("content-type:text/html; charset=utf-8");

/* 将用户信息保存到session中 */
$_SESSION['uid'] = $userinfo['uid'];
$_SESSION['name'] = $userinfo['name'];
$_SESSION['userinfo'] = $userinfo;

//* 将用户数据保存到cookie中的一个简单方法 */
$secureKey = '123456'; //加密密钥
$str = serialize($userinfo); //将用户信息序列化
//用户信息加密前
$str = base64_encode(mcrypt_encrypt(MCRYPT_RIJNDAEL_256, md5($secureKey), $str, MCRYPT_MODE_ECB));
//用户信息加密后
//将加密后的用户数据存储到cookie中
setcookie('userinfo', $str);

//当需要使用时进行解密
$str = mcrypt_decrypt(MCRYPT_RIJNDAEL_256, md5($secureKey), base64_decode($str), MCRYPT_MODE_ECB);
$uinfo = unserialize($str);
echo "解密后的用户信息：<br>";
print_r($uinfo);
```
