## Introduction to Dijkstra Algorithm 
_update Sep 4, 2017  0:16_

---
Dijkstra Algorithm 是一种贪心算法实现的单源最短路径（SSSP）算法，可以同时找到从单一起点 S 出发，到其他任意点的最短路径。

时间复杂度：O(n^2) (Matrix), O(ElogV) (adj list + heap queue);

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
-  `class Vertex {int label, Map<int, int> edges, int minDist}`
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
        for neighbor in vertex.edges:
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






