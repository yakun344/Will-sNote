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





