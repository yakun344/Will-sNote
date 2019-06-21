# Floyd Algorithm

_update Sep 4, 2017 13:33_

Floyd Algorithm 是一种用来解决多源最短路径问题（同时求出任意两点间最短路径长度）的动态规划算法，时间复杂度为O\(n^3\); 它的基本思路可以描述如下：

* 如果用matrix来表示Graph，则 `Graph[i][j]` 表示 vi 到 vj 的最短路径长度；
* for k = 1 to n，对每一对 vi，vj 分别考虑以 vk 作为中转能否优化其之间的路径长度；
* 状态转移方程：

  ```java
   f[k][i][j] = min(f[k-1][i][j] , f[k-1][i][k]+f[k-1][k][j])；
   即对每个Vk，尝试优化每一对点之间的最短路径；
  ```

可以参考这段代码：

```java
     for(k=1;k<=n;k++)
        for(i=1;i<=n;i++)
            for(j=1;j<=n;j++)
                if(e[i][j]>e[i][k]+e[k][j])
                     e[i][j]=e[i][k]+e[k][j];
```

最终结果就是Graph中的每个格子 `G[r][c]` 都存放着 Vr 和 Vc 间的最短路径长度；

## Path Reconstruction:

通过修改，可以让 Floyd Algo 具有重建最短路径的能力。基本思路是另外维持一个 v\*v 大小的next矩阵，其中的每一个格子 `next[u][v]` 存储了从 u 到 v 的路径上的下一个节点，例如：

```text
    next[u][v] = k; next[k][v] = v;
    此时我们就可知，从 u 到 v 的最短路径为：u -> k -> v；
```

以下内容摘自Wiki：

```cpp
    let dist be a |V|*|V| array of minimum distances initialized to INF
    let next be a |V|*|V| array of vertex indices initialized to null

    procedure FloydWarshallWithPathReconstruction ()
       for each edge (u,v)
          dist[u][v] ← w(u,v)  // the weight of the edge (u,v)
          next[u][v] ← v
       for k from 1 to |V| // standard Floyd-Warshall implementation
          for i from 1 to |V|
             for j from 1 to |V|
                if dist[i][j] > dist[i][k] + dist[k][j] then
                   dist[i][j] ← dist[i][k] + dist[k][j]
                   next[i][j] ← next[i][k]

    procedure Path(u, v)
       if next[u][v] = null then
           return []
       path = [u]
       while u ≠ v
           u ← next[u][v]
           path.append(u)
       return path
```

