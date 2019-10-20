# Iterator
_update Oct 19, 2019_

---

```

 _____________________               ____________________
| Aggregate interface |             | Iterator interface |
|---------------------|  create     |--------------------|
| iterator()          |------------>| hasNext()          |
|_____________________|             | next()             |
          △                         |____________________|
          |                                   △
          |                                   |
          |                                   |
__________|____________              _________|___________
|   ConcreteAgregate  |             |  ConcreteIterator   |
|---------------------|             |---------------------|
| iterator()          |<----------<>| agregate field      |
|_____________________|             |---------------------|
                                    | hasNext()           |
                                    | next()              |
                                    |_____________________|

```

Iterator 模式的目的是将遍历与实现分离开来，这样在遍历的时候只需要调用 ConcreteIterator 类实现的方法，而不需要调用 ConcreteAgregate 的方法。而这里使用接口则是为了弱化具体的类之间的耦合。

## 相关设计模式
### 1. Visitor 模式
在访问元素的过程中对元素进行相同的处理。

### 2. Composite 模式
Composite模式是具有递归结构的模式，使用Iterator模式比较困难。

### 3. Factory Method 模式
在 iterator 方法中生成 Iterator instance的 时候可能会使用 Factory Method 模式。
