## 关于几个最短路径算法
_update Jan 6, 2018  0:20_

---
**首先贴一个非常好的博客：[here](https://61mon.com/index.php/archives/195/), 这里的很多观点会来自这个博客。**

#### 关于 Bellman-Ford Algorithm
Bellman-Ford 算法是执行 `relax all edges for |V|-1 times`，时间复杂度为 O(|VE|)。它之所以慢，是因为每次在 relax 的过程中，只有已经确定了最短路劲的点出发的边的 relax 是有意义的，而其他的relax注定没有意义，所以如果想要提高这个算法的效率，我们需要做优化。

Bellman-Ford 的优点是可以处理负权重边，以及判断图中是否有负权回路，判断方法是执行过n-1次之后，再多执行一次，如果发现仍然有边可以被 relax，则说明有负权回路，并且该边就是负权的入口。

#### SPFA 算法，Bellman-Ford 的优化
















