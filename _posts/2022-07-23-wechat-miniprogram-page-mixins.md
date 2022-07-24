---
layout: post
title: "重读behaviors的合并规则"
author: "Chuilee"
tags: 微信小程序开发
excerpt_separator: <!--more-->
---
> behaviors 是用于组件间代码共享的特性，类似于一些编程语言中的 “mixins” 或 “traits”。
每个 behavior 可以包含一组属性、数据、生命周期函数和方法。组件引用它时，它的属性、数据和方法会被合并到组件中，生命周期函数也会在对应时机被调用。 每个组件可以引用多个 behavior ，behavior 也可以引用其它 behavior 。
<!--more-->

### 同名字段的覆盖和组合规则

组件和它引用的 `behavior` 中可以包含同名的字段，对这些字段的处理方法如下：

- 如果有同名的属性 (properties) 或方法 (methods)：
  1. 若组件本身有这个属性或方法，则组件的属性或方法会覆盖 `behavior` 中的同名属性或方法；
  2. 若组件本身无这个属性或方法，则在组件的 `behaviors` 字段中定义靠后的 `behavior` 的属性或方法会覆盖靠前的同名属性或方法；
  3. 在 2 的基础上，若存在嵌套引用 `behavior` 的情况，则规则为：`引用者 behavior` 覆盖 `被引用的 behavior` 中的同名属性或方法。
- 如果有同名的数据字段 (data)：
  - 若同名的数据字段都是对象类型，会进行对象合并；
  - 其余情况会进行数据覆盖，覆盖规则为： `引用者 behavior` > `被引用的 behavior` 、 `靠后的 behavior` > `靠前的 behavior`。（优先级高的覆盖优先级低的，最大的为优先级最高）
- 生命周期函数不会相互覆盖，而是在对应触发时机被逐个调用：
  - 对于不同的生命周期函数之间，遵循组件生命周期函数的执行顺序；
  - 对于同种生命周期函数，遵循如下规则：
    - `behavior` 优先于组件执行；
    - `被引用的 behavior` 优先于 `引用者 behavior` 执行；
    - `靠前的 behavior` 优先于 `靠后的 behavior` 执行；
  - 如果同一个 `behavior` 被一个组件多次引用，它定义的生命周期函数只会被执行一次。
