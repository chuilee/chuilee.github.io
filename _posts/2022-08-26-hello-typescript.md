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

### ** type 和 interface 的区别?**

**相同点**： 

1. 都可以描述 '对象' 或者 '函数' 

2. 都允许拓展(extends)

**不同点**： 

1. type 可以声明基本类型，联合类型，元组
2. type 可以使用 typeof 获取实例的类型进行赋值
3. 多个相同的 interface 声明可以自动合并

**使用 interface 描述‘数据结构’，使用 type 描述‘类型关系’**

### **简单介绍一下 TypeScript 模块的加载机制？**

假设有一个导入语句 `import { a } from "moduleA"`; 

1. 首先，编译器会尝试定位需要导入的模块文件，通过绝对或者相对的路径查找方式； 

2. 如果上面的解析失败了，没有查找到对应的模块，编译器会尝试定位一个`外部模块声明`（.d.ts）； 
3. 最后，如果编译器还是不能解析这个模块，则会抛出一个错误 `error TS2307: Cannot find module 'moduleA'.`

### **TypeScript 泛型中的 T、K、V 等到底是个啥？**

**常见的泛型变量名称**：

- T（Type）表示类型
- K（Key）表示对象中键的类型
- V（Value）表示对象中值的类型
- E（Element）表示元素类型 
- U（unknown）表示不指定泛型变量的实际类型
