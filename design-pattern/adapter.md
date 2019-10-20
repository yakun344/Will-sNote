# Adapter Pattern
_update Oct 20, 2019_

在开发过程中经常会遇到现有的程序无法直接食用，需要做适当的变换之后才能使用的情况，这种用于填补“现有程序” 和 “所需程序” 之间差异的设计模式就是 Adapter Pattern.Adapter pattern 也被称为 Wrapper pattern。

Adapter Pattern 有两种：
  1. 类适配器 Class Adapter 模式（使用继承）
  2. 对象适配器 Object Adapter 模式 （使用委托delegation，composite）
