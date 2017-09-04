## Introduction to Floyd Algorithm
_update Sep 4, 2017  13:33_

---
Floyd Algorithm 是一种用来解决多源最短路径问题（同时求出任意两点间最短路径长度）的动态规划算法，时间复杂度为O(n^3); 它的基本思路可以描述如下：
-  如果用matrix来表示Graph，则 `Graph[i][j]` 表示 vi 到 vj 的最短路径长度；
-  for k = 1 to n，对每一对 vi，vj 分别考虑以 vk 作为中转能否优化其之间的路径长度；

可以参考这段代码：
```java
     for(k=1;k<=n;k++)
        for(i=1;i<=n;i++)
            for(j=1;j<=n;j++)
                if(e[i][j]>e[i][k]+e[k][j])
                     e[i][j]=e[i][k]+e[k][j];
```

最终结果就是Graph中的每个格子 `G[r][c]` 都存放着 Vr 和 Vc 间的最短路径长度；


