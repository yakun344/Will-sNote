# Adapter Pattern
_update Oct 20, 2019_

在开发过程中经常会遇到现有的程序无法直接使用，需要做适当的变换之后才能使用的情况，这种用于填补“现有程序” 和 “所需程序” 之间差异的设计模式就是 Adapter Pattern.Adapter pattern 也被称为 Wrapper pattern。

Adapter Pattern 有两种：

  1. 类适配器 Class Adapter 模式（使用继承）
  2. 对象适配器 Object Adapter 模式 （使用委托delegation，composite）

## 1. Class Adapter Pattern
### 举例，Banner class
**现有类**：Banner，它有两个方法，分别是 `showWithParen` 和 `showWithAster`，分别可以将字符串用 `()` 和 `**` 括起来输出。

**需求**：Interface Print，有两个方法，分别是 `printWeak` 和 `printStrong`，对应之前两种输出字符串的格式。

于是我们要做的就是使用 Banner 写一个实现了 Print 接口的类：PrintBanner，使用 showWithParen 实现了 printWeak, 使用 Banner 的 showWithAster 实现了 printStrong。 这样 PrintBanner 就拥有了适配器功能。

#### UML
```
  __________
 |   Main   |
 |__________|
      |
      | uses
      |
 _____V_______           ______________         _______________
| Interface   |  impl   |   class      | ext   |   class       |
|   Print     |◁--------| PrintBanner  |------▷|   Banner      |
|-------------|         |--------------|       |---------------|
| printWeak   |         | printWeak    |       | showWithParen |
| printStrong |         | printStrong  |       | showWithAster |
|_____________|         |______________|       |_______________|

```

这里用一个class PrintBanner 继承 Banner，同时实现接口 Print，这样它就可以调用 Banner 类的方法来实现Print的方法，这就是通过“继承”实现了适配。

## 2. Object Adapter Pattern
这里要使用 "委托(delegation)" 来实现adapter。在Java中，"委托"是指将某个方法中的实际处理交给其他instance.

### 举例，同上，但Print是class
在这个情境下，由于Java无法多继承，我们无法继承 Print 的同时继承 Banner，于是我们就需要使用委托模式。我们实现一个类 `PrintBanner`, 它在 banner 字段中保存了一个 Banner 的 instance，这样 `PrintBanner` 类继承 `Print`，然后用其中的 Banner instance 来实现 `printWeak` 和 `printStrong` 方法。

#### UML
```
  __________
 |   Main   |
 |__________|
      |
      | uses
      |
 _____V_______           ______________         _______________
|   Class     |  ext    |   class      |  has  |   class       |
|   Print     |◁--------| PrintBanner  |<>---->|   Banner      |
|-------------|         |--------------|       |---------------|
| +printWeak  |         | - banner     |       | +showWithParen|
| +printStrong|         |--------------|       | +showWithAster|
|_____________|         | + printWeak  |       |_______________|
                        | + printStrong|
                        |______________|
```

## 3. Adapter 模式中登场的角色总结
#### i. Target
负责定义所需方法，在这里的例子中，就是Print。

#### ii. Client
负责使用Target所定义的方法进行具体操作，在这里的例子中，就是Main。

#### iii. Adaptee
被适配的角色，持有特定方法，但是和需求的方法不同。在这里的例子中，就是 Banner。

#### iv. Adapter
这个就是 adapter 本身，在这里的例子中，就是 PrintBanner。在 Class Adapter pattern 中，Adapter 通过继承来使用 Adaptee，而在 Object Adapter pattern 中，通过委托来使用 Adaptee。

## 4. 何时使用
### i. 限制排错范围
当在开发过程中要用到现有的类，而现有当类已经well tested，此时可以用 Adapter Pattern 对现有类进行适配，生成新的类，如果有Bug，我们就可以明确知道Bug不再 Adaptee 中，进而可以排查 Adapter 的bug即可。

### ii. 修改现有类以适配新接口
另外，有时候我们需要稍微修改现有的类（Adaptee）来适应新的接口，但是修改现有的类就需要重新测试，于是此时可以选择在 Adapter 中进行适配修改。

### iii. 版本兼容
可以使用 Adapter 模式来对旧版本进行兼容，旧版本扮演 Target 角色，新版本扮演 Adaptee 角色，然后用 Adapter 来使用新版本的类来实现旧版本的接口。

## 5. 相关设计模式
1. Bridge 模式
> Adapter 模式用于连接 API 不同的类，而 Bridge 模式则是用来连接类的功能层次结构与实现层次结构。
2. Decorator 模式
> Adapter 模式用于填补不同API之间的间隙，而 Decorator 模式则可以在不改变API的前提下增加功能。
