## Introduction to Union Find (disjoint sets) Algorithm
_update Sep 7, 2017  18:06_

---
这也就是传说中的并查集，在CLRS中被叫做 Disjoint Set。这是一种用来解决**动态连通性**问题的算法，主要可以实现如下三个接口：

1.  **makeSet：**  初始化一个并查集，此时并查集中每一个 node 都是独立为自身的代表，也就是说此时每个集合都只有唯一一个元素；
2.  **find(node)：**  在并查集中查找某元素所归属集合的representation并返回，可以用 `find(node1)==find(node2)` 来判断两个元素是否在同一个集合