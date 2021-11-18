# Overview

## 1. 什么样的代码是好的代码
有许多可以用来形容代码的词：

灵活性（flexibility）、可扩展性（extensibility）、可维护性（maintainability）、可读性（readability）、可理解性（understandability）、易修改性（changeability）、可复用（reusability）、可测试性（testability）、模块化（modularity）、高内聚低耦合（high cohesion loose coupling）、高效（high effciency）、高性能（high performance）、安全性（security）、兼容性（compatibility）、易用性（usability）、整洁（clean）、清晰（clarity）、简单（simple）、直接（straightforward）、少即是多（less code is more）、文档详尽（well-documented）、分层清晰（well-layered）、正确性（correctness、bug free）、健壮性（robustness）、鲁棒性（robustness）、可用性（reliability）、可伸缩性（scalability）、稳定性（stability）、优雅（elegant）。

对于一段代码的评价应该是综合性的，要考虑到诸多方面，不是非黑即白。一般来说，好的代码要具有如下特征：

- 有可维护性
- 可读性
- 可扩展性
- 灵活性：flexibility, 易扩展易复用
- 简洁性：kiss原则，keep it simple, stupid
- 可复用性：DRY，do not repeat yourself
- 可测试性

## 2. 主要内容框架
### 1）面向对象
主流的编程范式主要有三种，分别是面向过程，面向对象和函数式编程，其中面向对象是最主流的。因为面向对象的特性（封装，抽象，继承，多态），它成为了很多设计原则与设计模式的实现基础。关于这部分主要需要掌握：

- 面向对象的四大特性：封装、抽象、继承、多态
- 面向对象编程与面向过程编程的区别和联系
- 面向对象分析、面向对象设计、面向对象编程
- 接口和抽象类的区别以及各自的应用场景
- 基于接口而非实现编程的设计思想
- 多用组合少用继承的设计思想
- 面向过程的贫血模型和面向对象的充血模型

### 2）设计原则
SOLID原则是一些比较抽象的总结性原则，直接记忆意义并不大，需要结合具体的场景。常用的设计模式都遵循这些原则。

- SOLID 原则 -SRP 单一职责原则
- SOLID 原则 -OCP 开闭原则
- SOLID 原则 -LSP 里式替换原则
- SOLID 原则 -ISP 接口隔离原则
- SOLID 原则 -DIP 依赖倒置原则
- DRY 原则、KISS 原则、YAGNI 原则、LOD 法则

### 3）设计模式
设计模式是针对软件开发中经常遇到的一些设计问题，总结出来的一套解决方案或者设计思路。常见的设计模式有23种，按照常见于不常见以及应用，分为如下几类。

#### 1. 创建型
- 常用的有：单例模式、工厂模式（工厂方法和抽象工厂）、建造者模式。
- 不常用的有：原型模式。

#### 2. 结构型
- 常用的有：代理模式、桥接模式、装饰者模式、适配器模式。
- 不常用的有：门面模式、组合模式、享元模式。

#### 3. 行为型
- 常用的有：观察者模式、模板模式、策略模式、职责链模式、迭代器模式、状态模式。
- 不常用的有：访问者模式、备忘录模式、命令模式、解释器模式、中介模式。

### 3）代码重构
只要代码不停迭代，就没有一劳永逸的设计，而重构是开发中的重要环节，是保持代码质量不下降的重要手段。重构的工具就是前面的的面向对象思想，设计原则，设计模式。
