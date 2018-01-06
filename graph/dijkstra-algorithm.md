## Introduction to Dijkstra Algorithm 
_update Sep 4, 2017  0:16_

---
Dijkstra Algorithm 是一种贪心算法实现的单源最短路径（SSSP）算法，可以同时找到从单一起点 S 出发，到其他任意点的最短路径。

**时间复杂度：** 

&emsp; O(n^2) for (Matrix), O(ElogV) for (adj list + heap queue);  

&emsp; Dijkstra 算法的空间复杂度是O(n), 我们只需要存储 n 个最短路径的上限, 对其更新即可. 时间复杂度分析牵扯到一些相对复杂的数据结构, 我们先分析一下它的开销, 之后解释这个事情.设图 G 中的边数是 E , 点数是 V , 则这个算法的开销总共有两个地方: 第一, 对于每一个 G' 的拓展, 我们都需要寻找一个 G' 之外路径长度最小的节点, 假定它的时间代价是O(find_min) 或 O(extract_min) ; 第二, 我们拿到一个新的节点之后需要利用它的所有临边进行松弛操作, 它的时间代价记为 O(update) . 对于后者来说, 我们考虑 Dijkstra 的整体过程, 每一条边都会被用来做一次松弛操作, 因此总共只会执行 E 次的松弛操作. 因此, Dijkstra 算法的时间复杂度 C 如下.
```python
    O(V * findMin + E * update)
```
即我们至多需要 `poll` V 次，`update` E 次，而每次`poll`或者`update`，耗时都是`O(logV)`, 故总时间复杂度为 `O((V+E)logV)`；

#### Basic Idea
Dijkstra 的基本思想是将图中所有的 V 分成两组：visited 和 not visited，同时维持一个 distance array 记录每个 v 到 S 的 距离，distance[s]=0；

每次从 not visited 中选择 distance 最小的 v，将其加入 visited，对所有 v 的 neighbor，release `edge[v][neighbor]`；

直到所有 v 都加入了 visited；

#### 在 Matrix 表示的 Graph 中的实现方法：
Variables：
-  `int[][] Graph`,   `Graph[i][j]` 存放每条边的weight;
-  `int[] distance`,   存放每个节点 v 到 s 的距离；
-  `int[] pred`,       存放每个节点最短路径上的前驱节点
-  `booean[] visited`, 标志每个节点 v 是否已经完成；

Algo：
```python
    先选定 s
    distance[s] = 0
    pred[s] = -1
    
    for i in range(1, V):  # 执行 V - 1 次，每次产生一个结束的节点
        curr = distance最小，且不在visited中的 v  # O(v) 
        visited[curr] = True
        for j in range(v): # 检查每个节点j，看能否用curr优化当前distance[j]
            newDist = Graph[curr][j] + distance[curr]
            if visited[j] != True and newDist < distance[j]:
                distance[j] = newDist
                pred[j] = curr
``` 

#### 在 Adj List 表示的 Graph 中实现的方法：
Variables:
-  `class Vertex {int label, Map<int, int> edges, int minDist}` 其中的edges存放{neighborLabel : weight} 对；
-  `Map<int, Vertex> Graph`, 存放graph；
-  `Map<int, int> pred`, 存放每个节点在路径中的前驱节点；
-  `Set<int> visited`, 标记每个 v 是否完成；
-  `PriorityQueue<Vertex> pq`, 用来优化时间的pq；

Algo：
```python
    先选定 s，将其放入pq，pred[s]=-1, 其余为 INF；
    
    while pq:
        vertex = pq.poll()
        visited[vertex.label] = True
        for neighborLabel in vertex.edges:
            if neighborLabel in visited: 
                continue
            neighbor = Graph[neighborLabel]
            newDist = vertex.minDist + vertex.edges[neighbor]
            if newDist < neighbor.minDist:
                neighbor.minDist = newDist
                pred[neighbor.label] = vertex.label
                pq.offer(neighbor)
```

这里可能会有疑问，如果已经在pq中的v的minDist发生了变化怎么办？其实无须担心，只需更新之后重新将其加入pq即可，这样更新之后的会先被访问，之后被加入 visited，等pq中之前那个v被poll出之后，由于已经在visitd中，会被忽略；

---
_update Sep 6,2017  19:50_

**优化：**

做过了 **[The Maze II](https://will-gxz.gitbooks.io/xiaozheng_algo/content/graph/the-maze-ii.html)** 后，发现其实不需要visited，只需在新node出队的时候判断 `if (distance[node] < node.dist) continue`， 这样就可以排除因为`dist`更新而被重复加入pq的node。而在更新`currNode` 所有 `neighbor` 的时候也无需判断是否在`visited`中，因为检查 `distance[neighbor] > newDistance` 的过程也是 O(1) 的，已经完成的 node 不会通过判断，也就不会被重复处理。

---
_update Jan 6, 2018 0:57_

#### 关于 Dijkstra 处理 negative weight edge 的问题
来自知乎的答案：[这里](https://www.zhihu.com/question/21620069);

>作者：知乎用户
链接：https://www.zhihu.com/question/21620069/answer/24518138
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

>需要注意区分算法与算法的实现这两个层面：先举一个例子，如果要实现一个链表的删除算法，既可以用指针把当前节点的前后节点接起来，也可以给当前节点设置一个标志位，表示它已经被删除。这些都是实现上的细节，并不影响算法的本质。当我们说“从单链表中删除一个节点”的时候，是从算法的逻辑上讲的，怎么实现还可以再商榷。同样，优先队列优化（或者说堆优化）的dij算法通常也有两种实现方式：一种是自己手写一个堆，支持修改优先级，这种实现代码量较大，写的人不多；另一种是允许优先队列中存在一个节点的多个距离标号不同的拷贝，但只有距离标号最小的一个被处理（它出队时的距离标号被认定为源到它的最短路径长度），剩下的都忽略/直接跳过。如果用`C++`的STL的话，这种实现比较方便，因为不需要修改队列中元素的优先级。注意这仅仅是在无法修改优先级时采用的一种折中的办法，是实现上的一个小技巧。（这种技巧，或者说技术，正式名称叫延迟删除：`lazy deletion`）从逻辑上讲，它依旧是不允许已经出队的节点重入队的。另外，这种实现的时间复杂度与前一种实现同为`O( (V+E)*lgV )`，仅仅是大O记号下隐藏的常数变大了。@令狐一 在评论中提到的不是第三种算法（优先队列优化+允许重入队），而是第二种算法（优先队列优化的dij算法）的一种实现方式，并非真正的“允许重入队”。允许重入队的意思是，对于已经出队的节点，如果它的距离标号还能被更新，那么还要进行更新，再次入队。第三种算法和第二种算法的根本区别是：已经出队的节点并非已经确定最短路径的节点，它的距离标号之后还可以进行更新。第三种算法的时间复杂度不好分析，算法的运行时间依赖于输入数据的分布。在非负权图的情况下，此算法与第二种算法行为完全相同，时间复杂度为O（(V+E)*lg V）；而对于有负权边但无负权回路的图，算法的最坏运行时间是顶点数目的指数。(具体参见 [here](https://courses.engr.illinois.edu/cs498374/fa2014/notes/26-sssp.pdf)) 

>不过实际运行起来往往比较快，远好于理论上界，在有负权边而无负权回路的图上，如果运气不是特别差的话，行为通常像是O（E）的。这个算法实在太奇葩了，它既不是Dij，也不是BF；既是Dij的变种，又是BF的变种，是一个混合产物。由于这个算法的运行时间不稳定，有时在算法竞赛中可以构造出来一些图卡这个算法让它退化，之后在国内spfa就有了一些魔改版（尤其是在09年集训队姜碧野的论文之后），乱改松弛/入队/出队等的顺序或者把优先队列换成栈等其他数据结构，彻底乱成了一锅粥。这些变种算法实践中通常都是有效的，然而其最坏时间复杂度或者算法的正确性都很难得到保证，也许在极端情形下会出错或者特别慢，仅仅是OIer/ACMer做题的时候还没遇到，我个人的建议是不要趟这些浑水……


