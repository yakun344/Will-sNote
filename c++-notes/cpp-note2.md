## Cpp Note2: 运算符重载，容器使用，Function Object
_update May 2, 2018  20:10_

---

### 运算符重载和友元函数
#### [这篇文章](https://blog.csdn.net/kingcat666/article/details/44870799) 的分析非常好，下面是关于这篇文章的摘要：

1. 运算符重载的形式主要分为两种，分别是 **类成员函数** 以及 **类的非成员函数**，其中重载为**非成员函数**的通常为**友元函数**，因为可能需要访问参数对象的 private fields;
2. 重载为类成员函数的情况，可以视为如下等价表达：
```cpp
    <对象名>.operator <运算符>(<参数>)

    它等价于

    <对象名><运算符><参数>

  例如：a+b等价于a.operator +(b)。一般情况下，我们采用运算符的习惯表达方式。
```
3. 重载为类的非成员函数的情况，可以视为如下表达：
```cpp
    operator <运算符>(<参数1>,<参数2>)

    它等价于

    <参数1><运算符><参数2>

    例如：a+b等价于operator +(a,b)。
```

4. 一般当运算符有可交换性质的时候，选择重载为友元函数。

在 Java 中是没有重载运算符的，在Java中 override `compare() method` 相当于c++中重载 `< operator`.

### Function Object
在C++中可以通过重载 `()` 运算符使一个Object具有类似function的效果。 