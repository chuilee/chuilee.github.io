---
layout: post
title: "Hello TypeScript!"
author: "Chuilee"
tags: TypeScript
excerpt_separator: <!--more-->
---

> 记录TypeScript下用法及注意事项

### **什么是TypeScript？**

Typescript 是一个强类型的 JavaScript 超集，支持ES6语法，支持面向对象编程的概念，如类、接口、继承、泛型等。Typescript并不直接在浏览器上运行，需要编译器编译成纯Javascript来运行。

### **TypeScript 中 type 和 interface 的区别?**

**相同点**： 

1. 都可以描述 '对象' 或者 '函数' 

2. 都允许拓展(extends)

**不同点**： 

1. type 可以声明基本类型，联合类型，元组
2. type 可以使用 typeof 获取实例的类型进行赋值
3. 多个相同的 interface 声明可以自动合并

**使用 interface 描述‘数据结构’，使用 type 描述‘类型关系’**


