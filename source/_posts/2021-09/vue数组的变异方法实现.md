---
title: vue数组的变异方法实现
date: 2021-09-02 20:20:28
tags: Vue
category: Vue
---

## 函数拦截

仿写实现 vue 的数组变异方法之前，首先要了解函数拦截这个概念。是指在函数原有的基础上增加额外的操作

-   使用一个临时的函数名储存函数
-   重新定义原来的函数
-   定义扩展的功能
-   调用临时的那个函数

```
 function fn() {
     console.log('函数本身的功能');
 }

 let _tFn = fn;
 fn = function () {
     _tFn()
     console.log('新的扩展的功能');
 }
 fn(); // 打印出本身功能 扩展功能
```

## vue 中的实现方式

```
/**  修改数组原型方法 */
let ARRAY_METHOD = [
    'push', 'pop', 'shift', 'unshift', 'reverse', 'sort', 'splice'
];
// 将原型链继承：修改原型链结构
let arr = [];
// 关系 ： arr => Array.prototype => Obj.prototype
//  更换为
// 关系：  arr => 函数拦截(改写的方法) => Array.prototype => Obj.prototype
let array_methods = Object.create(Array.prototype)
ARRAY_METHOD.forEach(method => {
    // console.log('调用拦截的', method);
    array_methods[method] = function () {
        let res = Array.prototype[method].apply(this, arguments)
        return res
    }
})
arr.__proto__ = array_methods;
```

![BG图片](/img/1.jpg)
