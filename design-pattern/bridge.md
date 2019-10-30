# Bridge Pattern
_update Oct 29, 2019_

--

## 1. Introduction
Bridge 起到桥梁作用，用来连接类的**功能层次结构**和**实现层次结构**。

### i. 类的层次结构的两个作用
1. 希望增加新功能时
  > 假设有一个类 Something，当我们想要在 Something 中增加新功能时（想增加一个具体方法），会编写一个Something类的子类，即SomethingGood类，这样就有了一个类的层次结构：
  > ```
  > Something
  >      |
  >      |--SomethingGood
  > ```
  > 这就是为了增加新功能而产生的层次结构。父类具有基本功能，在子类中增加新的功能。  
  > 如果我们想要在 SomethingGood 的基础上增加新的功能，我们可以同样编写一个 SomethigBetter类，这样层次结构就更深了：
  > ```
  > Something
  >    |
  >    |--SomethingGood
  >            |
  >            |--SomethingBetter
  > ```
  > 通常来说，类的层次结构不易过深。

2. 希望增加新实现时
