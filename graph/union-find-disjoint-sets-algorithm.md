## Introduction to Union Find (disjoint sets) Algorithm
_update Sep 7, 2017  18:06_

---
#### Introduction:
这也就是传说中的并查集，在CLRS中被叫做 Disjoint Set。这是一种用来解决**动态连通性**问题的算法，主要可以实现如下三个接口：

1.  **makeSet：**  初始化一个并查集，此时并查集中每一个 node 都是独立为自身的代表，也就是说此时每个集合都只有唯一一个元素；
2.  **find(node)：**  在并查集中查找某元素所归属集合的representation并返回，可以用 `find(node1)==find(node2)` 来判断两个元素是否在同一个集合；
3.  **union(node1, node2)：**  合并 node1 和 node2 所在的集合；

#### Representation:
并查集有两种主要的实现方式，分别是：

1.  链式实现：
> 这种实现方式理论上耗时更多，因为每次进行 union 操作的时候都要花费 O(n) 的时间改变每个 node 的parent。即使使用 weighted-union heuristic 进行优化，仍然要消耗 O(m + nlogn) 的时间（m 为操作数，n 为元素个数）。正是因为这种方法更加耗时，这里不多做介绍。

2.  Disjoint-set Forests:
> 也就是传说中的
