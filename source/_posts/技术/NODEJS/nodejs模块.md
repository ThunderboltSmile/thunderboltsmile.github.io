---
title: nodejs模块机制
date: 2020-04-08 22:37:25
categories:
  - nodejs
  - 学习笔记
---

#### CommonJs规范
为了使nodejs具备开发大型应用的基础能力，CommonJS规范应运而生，其涵盖了模块、二进制、Buffer、
字符集编码、I/O流、进程环境、文件系统、套接字、单元测试、Web服务器网关接口、包管理等。

<!-- more -->

#### 模块规范

1. 模块引用
```javascript
var math = require('math');
```
2. 模块定义
- require方法用于引入模块
- exports对象用于导出当前模块的方法或者变量
- module对象代表模块自身
- exports是module的属性
- 一个文件就是一个模块

```javascript
// math.js
exports.add = function(){
    var sum = 0,i=0,args = arguments,l = args.length;
    while(i<l){
        sum += args[i++]
    }
    return sum;
}
```

```javascript
// program.js
var math = require('math')
exports.increment = function(val){
    return math.add(val,1)
}
```

3. 模块标示
｜模块 = require('模块标示')
每个模块具有独立的空间，他们互不干扰，使得用户完全不必考虑变量污染

#### 模块实现
在node中引入模块，需要经历如下3个步骤
- 路径分析
- 文件定位
- 编译执行
node中的模块分为核心模块和文件模块，核心模块加载速度最快，文件模块为用户自己编写的模块，需要完整的
路径分析、文件定位、编译执行过程，速度比核心模块慢。

a. 优先从缓存加载
Nodejs为了提高性能，会对加载的模块进行缓存
b. 路径分析和文件定位
从加载优先级来讲 缓存>核心模块>路径>自定义模块
- 文件扩展名分析：有时模块标示没有包含扩展名，node会按照 .js,.json,.node的次序补足扩展名，依次尝试。
c. 模块编译
```javascript
// 文件模块的定义
function Module(id, parent) { 
    this.id = id; 
    this.exports = {}; 
    this.parent = parent; 
    if (parent && parent.children) { 
        parent.children.push(this); 
    } 
    this.filename = null; 
    this.loaded = false; 
    this.children = []; 
}
```

定位到具体文件后，Node会新建一个模块对象，然后根据路径载入并编译。对于不同的文件扩展名，载入方法有所
不同
- .js文件 通过fs模块同步读取文件后编译执行
- .node文件 这是C/C++编写的扩展文件，通过dlopen()方法加载最后编译生成的文件
- .json文件 通过fs模块同步读取文件后，用JSON.parse解析返回结果
- 其余扩展名 都被当作.js文件载入
可以通过`require.extensions['.xxx']`的方式来实现加载自定义扩展名文件的能力。但这样会将繁琐的编译加载等过程引入Node的执行过程中
在编译的过程中，Node对获取的javascript文件内容进行了头尾包装。在头部添加了`(function(exports,require,module,__filename,__dirname){`，在尾部添加了`});`。这样每个模块文件之间都进行了作用域隔离。包装之后的代码会通过vm原生模块的`runInThisContext()`方法执行，返回一个具体的function对象。


#### 核心模块
核心分为C/C++编写部分（node项目的src目录下）和javascript编写部分（存放在项目的lib目录下）。
1. javascript核心模块的编译过程
a. 在编译所有C/C++文件之前，编译程序需要将所有的javascript模块文件编译为C/C++代码。
- 将javascript代码转换为C++里的数组，生成node_natives.h头文件
- 启动node时，直接加载进内存中，经历标识符分析后直接定位到内存中。
b. 编译成功的模块缓存到NativeModule._cache对象上，文件模块则缓存到Module._cache对象上
```javascript
function NativeModule(id) { 
    this.filename = id + '.js'; 
    this.id = id; 
    this.exports = {}; 
    this.loaded = false; 
} 
NativeModule._source = process.binding('natives'); 
NativeModule._cache = {};
```
2. C/C++核心模块的编译过程
a. 内建模块的组织形式
在Node中，内建模块的内部结构定义如下：
```c++
struct node_module_struct { 
    int version; 
    void *dso_handle; 
    const char *filename; 
    void (*register_func) (v8::Handle<v8::Object> target); 
    const char *modname; 
};
```
每一个内建模块在定义之后，都通过NODE_MODULE宏将模块定义到node命名空间中，模块的具体初始化方法挂载为结构的register_func成员。
内建模块在执行时，直接加载进内存，无需标识符定位、文件定位以及编译过程。
b. 内建模块的导出
从模块层级间依赖的角度看：文件模块-依赖->核心模块(javascript)-依赖->内建模块(C++)
3. 核心模块的引入流程

`NODE_MODULE(node_os,reg_func)->get_builtin_module("node_os")->process.binding("os")->NativeModule.require("os")->require("os")`

4. 模块调用栈
nodejs核心模块的职责主要有两类：为底层模块提供封装和桥接层，供文件模块调用；纯粹功能模块，不需要跟底层交互，但十分重要。

#### 包与NPM
一个包应该包含如下目录和文件：
- package.json 描述文件
- bin 存放可执行二进制文件
- lib 存放javascript代码
- doc 存放文档
- test 存放单元测试用例

##### 如何发布一个包

1. 编写模块
2. 初始化包描述文件（建议npm init命令来生成）
3. 注册包仓库帐号（npm adduser）
4. 上传包（npm publish <folder>）
5. 安装包 
6. 管理包权限（npm owner）
7. 分析包（npm ls）

#### AMD、CMD规范
AMD模块需要用define来明确定义一个模块，而在Node实现中是隐式包装的，他们的目的是进行作用域隔离，仅在需要的时候被引入，避免通过全局变量或者全局命名空间的方式，以免变量污染和不小心被修改。AMD规范的内容需要通过返回的方式来实现
```javascript
define(['dep1','dep2'],function() { 
 var exports = {}; 
 exports.sayHello = function() { 
 alert('Hello from module: ' + module.id); 
 }; 
 return exports; 
});
```
AMD需要在定义模块时指定所有的依赖，通过形参传递依赖到模块内容中，而CMD支持对依赖的动态引入
```javascript
define(function(require, exports, module) { 
 // The module code goes here 
});
```

