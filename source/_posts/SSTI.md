---
title: SSTI
date: 2020-06-24 15:42:12
tags: [python_SSTI]
categories: SSTI
---

## SSTI

[toc]

## python_SSTI

### flask

>利用属性:
>`__class__`:返回对象所属的类
>
>`__base__`:字符串形式返回一个类所直接继承的类
>
>`__bases__`:元组形式返回一个类所直接继承的类
>
>`__mro__`:解析方法调用顺序
>
>`__subclasses__`:获取所有类的子类
>
>`__init__`:包含init的类
>
>`function__globals__`:函数作用域下所有的module 方法和所有的变量
>
>