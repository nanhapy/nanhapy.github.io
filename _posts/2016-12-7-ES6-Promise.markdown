---
layout: post
title:  "Promise"
date:   2016-11-07 11:25:49 +0800
categories: programming javascript
---

**Promise.prototype.then()**

`then()`将接受两个参数：成功回调函数(onFulfilled)和失败回调函数(onRejected),并返回一个新的Promise。

由于返回值是Promise类型，所以可以构成链式调用。

*语法*

>p.then(onFulfilled[, onRejected]);
>
>p.then(function(value) {
>    //fulfillment
>}, function(reason){
>    //rejection
>}) 

1.  如果两个参数都未指定或者不是函数对象。那么返回的新Promise只是简单的采用调用`then()`Promise的值。如果是某一个参数未指定或者不是函数对象，那么返回的Promise只有对应的这个状态会采用原Pormise的状态。

    ```js
    var p = new Promise(function(resolve, reject) {
        setTimeout(function(){
            resolve('time up~');
        }, 5000);
    });

    var p1 = p.then();
    //before 5secs
    console.log(p1); //Promise {[[PromiseStatus]]: "pending", [[PromiseValue]]: undefined}
    //after 5secs
    console.log(p1); //Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: 'time up~'}
    ```

2.  如果onFulFilled或者onRejected抛出异常或者返回一个rejected Promise，`then()`将返回一个rejected Promise。

    +   onFulFilled返回一个rejected Promise

        ```js
        var p1 = Promise.resolve('foo')
        .then(function(){
            return Promise.reject('i feel bad..');
        });
        console.log(p1);//Promise {[[PromiseStatus]]: "rejected", [[PromiseValue]]: "i feel bad.."}
        
        var p2 = p1.then(function(){//not called
            console.log('resolve called');
        },function(reason){
            console.log(reason);//'i feel bad..'
        });
        ```

    +   onFulFilled抛出异常
        
        ```js
        var p1 = Promise.resolve('foo')
        .then(function(){
            throw 'oops~';
        });
        console.log(p1);//Promise {[[PromiseStatus]]: "rejected", [[PromiseValue]]: "oops~"}
        
        var p2 = p1.then(function(){//not called
            console.log('resolve called');
        },function(reason){
            console.log(reason);//'oops~'
        });
        ```

3.  如果onFulFilled或者onRejected返回一个resovled Promise，或者返回任意一个值（包括undefined），`then()`将返回一个resovled Promise。

    +   onRejected返回一个resovled Promise

        ```js
        var p1 = Promise.reject('foo')
        .then(null, function(reason){
            return Promise.resolve('i feel good now.');
        });
        console.log(p1);//Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: "i feel good now."}
        
        var p2 = p1.then(function(text){
            console.log(text); //'i feel good now.'
        },function(reason){//not called
            console.log(reason);
        });
        ```

    +   onFulFilled返回任意一个值

        ```js
        var p1 = Promise.resolve('foo')
        .then(function(text){
            return 'simple value';
        });
        console.log(p1);Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: "simple value"}
        
        var p2 = p1.then(function(text){
            console.log(text); //'simple value'
        },function(reason){//not called
            console.log(reason);
        });
        ```

