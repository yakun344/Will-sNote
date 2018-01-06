## 关于几个最短路径算法
_update Jan 6, 2018  0:20_

---
**首先贴一个非常好的博客：[here](https://61mon.com/index.php/archives/195/), 这里的很多观点会来自这个博客。**

#### 关于 Bellman-Ford Algorithm
Bellman-Ford 算法是执行 `relax all edges for |V|-1 times`，时间复杂度为 O(|VE|)。它之所以慢，是因为每次在 relax 的过程中，只有已经确定了最短路劲的点出发的边的 relax 是有意义的，而其他的relax注定没有意义，所以如果想要提高这个算法的效率，我们需要做优化。

Bellman-Ford 的优点是可以处理负权重边，以及判断图中是否有负权回路，判断方法是执行过n-1次之后，再多执行一次，如果发现仍然有边可以被 relax，则说明有负权回路，并且该边就是负权的入口。

#### SPFA 算法，Bellman-Ford 的优化
SPFA(Shortest Path Faster Algorithm) 是由西南交大的段凡丁在上世纪九十年代提出的，它旨在通过使用一个 FIFO 的 queue 来对 Bellman Ford 算法进行优化，最终将时间复杂度控制在 `O(k * E)`, 其中 k 是每个vertex的平均入队次数，在 sparse graph 中一般小于 2，而在 dense graph 中不稳定。

**实现方法：**   
&emsp; 在Bellman-Ford的基础上，维持一个queue用来存放接下来需要visit的点（这里的visit指的是relax它的出边）。每次取出 peek 的元素 u，relax 它所有的出边 `(u,v)`，如果 v 的距离变得更小，且 v 不在 queue 中，则将其加入 queue 中，等待之后更新它的 neighbors。直到队列为空。

&emsp; 判断一个 vertex 是否在 queue 中，可以另外维护一个 `boolean[] visited`，每次 poll 出队首之后令其 visited 中对应值变为 false，enqueue 后则变为 true；

**优化：**  
另外，SPFA 算法有两个优化策略：SLF 和 LLL。

1.  SLF，Small Label First 策略，设要加入的节点是 j，队首元素为 i，若dist[j] < dist[i]，则将 j 插入队首，否则插入队尾；
2.  LLL，Large Label Last 策略，设队首元素为 i，队列中所有 dist 值的平均值为 x，若dist[i] > x则将 i 插入到队尾，查找下一元素，直到找到某一 i 使得dist[i] <= x，则将 i 出队进行松弛操作。

SLF 可使速度提高 15 ~ 20%，SLF + LLL 可提高约 50%。 但这两个优化本身是需要额外的复杂度的，甚至可能需要重新设计一套数据结构，来高效完成队首与队尾的插入。













