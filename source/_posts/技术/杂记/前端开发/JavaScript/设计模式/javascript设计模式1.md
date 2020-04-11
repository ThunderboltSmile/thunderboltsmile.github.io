---
title: JavaScript设计模式--单例模式
date: 2017-08-014
categories:
  - JavaScript
  - 读书笔记
tags:
  - 设计模式
  - JavaScript
---

本博客的设计模式系列均为《javascript设计模式与开发实践》一书的读书笔记，仅用于个人学习和交流。

单例模式：保证一个类仅有一个实例，并提供一个访问它的全局访问点。
<!-- more -->
### 基本实现
用一个变量来标记当前是否为某个类创建过对象，若是，则在下一次试图创建该类对象时直接返回创建过的实例。
```javascript
//构造函数
let Singleton=function(name){
    this.name=name;
    this.instance=null;
};
//定义方法
Singleton.propertype.getName=function(){
    alert(this.name)
};
//公开的获取接口
Singleton.getInstance=function(name){
    if(!this.instance){
        this.instance=new Singleton(name);
    }
    return this.instance;
}

let a=Singleton.getInstance('a');
let b=Singleton.getInstance('b');

alert(a===b); //true
```
我们可以让这段代码稍微函数式一点
```javascript
var Singleton = function( name ){
 this.name = name;
};

Singleton.prototype.getName = function(){
 alert ( this.name );
};

Singleton.getInstance = (function(){
    var instance=null;
    return function(name){
        if(!instance){
            instance = new Singleton( name );
        }
        return instance;
    }
})(); 
```
### 透明的单例模式
上面的代码在获取对象时，使用的是getInstance,而不是new方法，这多少会造成一些困惑。同时，该单例类的扩展性也受到了限制，我们需要一种透明的，可以用new方法获得，并且低耦合的单例类。
```javascript
var CreateDiv=(function(){
    var instance;
    var CreateDiv=function(html){
        if(instance){
           return instance;
        }
        this.html = html;
        this.init();
        return instance = this;
    };
    CreateDiv.prototype.init = function(){
        var div = document.createElement( 'div' );
        div.innerHTML = this.html;
        document.body.appendChild(div);
    };
    return CreateDiv;
})();
var a=new CreateDiv('sven1');
var b=new CreateDiv('sven2');
alert (a===b); // true
```
现在我们获得了透明的单例类，但是代码的耦合还是存在。我们需要将控制单例唯一性的逻辑抽取出来单独管理。
```javascript
var CreateDiv=function(html){
    this.html=html;
    this.init();
};
CreateDiv.proptotype.init=function(){
    var div=document.createElement('div');
    div.innerHTML=this.html;
    document.body.appendChild(div);
};
//构造代理类来管理单例的唯一性逻辑
var ProxySingletonCreateDiv=function(){
    var instance;
    return function(html){
        if(!instance){
            instance=new CreateDiv(html)
        }
        return instance;
    }
}
var a=new ProxySingletonCreateDiv('sven1');
var b=new ProxySingletonCreateDiv('sven2');
alert (a===b); //true
```
这样我们就将创建实例和控制单例唯一性的逻辑分开来，减少了耦合，增强了代码的可复用性。

### javascript中的单例模式
虽然es6已经引入了class来表示类，但是在实际生产环境下，许多功能使用脚本语言的非类的部分更加灵活。比如绑定事件等，在单例模式的作用下，可以保证某个事件只被绑定一次。
```javascript
var getSingle=function(fn){
    var result;
    return function(){
        return result||(result=fn.apply(this,arguments))
    }
}
/*将绑定事件或者其他什么逻辑写进一个带有返回值的函数，则使用getSingle处理该函数时，能够确保函数只被执行一次*/
var bindEvent = getSingle(function(){
    document.getElementById( 'div1' ).onclick = function(){
    alert ( 'click' );
 }
 return true;
});
var render = function(){
    console.log( '开始渲染列表' );
    bindEvent();
};
render();
render();
render();
/*可以看到，render 函数和 bindEvent 函数都分别执行了 3 次，但 div 实际上只被绑定了一个事件。*/
```
在每次执行getSingle时，由于result被作为结果返回给使用者定义的变量，此时的result是以引用类型传递，于是整个上下文产生了闭包，每次调用所产生的result都会被保留在相应的闭包中（不会被垃圾回收机制销毁），从而使得单例的唯一性得到了保证。

