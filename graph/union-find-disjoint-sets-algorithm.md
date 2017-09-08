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
> 也就是传说中的树型实现方法。通过 _union by rank_ 和 _path compression_ 两种方法的优化之后，可以达到 `O(m * α(n))≈O(m) (m 为操作数，n 为元素个数)` 时间内完成 m 个操作的时间复杂度，也就是 O(1)。这里的 `α(n)` 是一个增长极其缓慢的函数，实际应用中不会超过 4。

#### Implementation
Disjoint-set Forests 的实现思路, 参考 wiki：[Disjoint Set](https://en.wikipedia.org/wiki/Disjoint-set_data_structure);

[这里](https://www.youtube.com/watch?v=ID00PMy0-vE) 还有一个图杀老师讲并查集的视频。

[这里](https://neo1218.github.io/unionfind/) 也有一个中文实现的教程；

**基础版本的实现（使用传统的tree node结构）：**

这种实现方式和CLRS或WIKI中完全一致，定义新的Node class，然后依照wiki中的伪代码实现。
```java
    import java.util.*;    
    class UnionFind {
        private class Node {
            int val, rank;
            Node parent;
            public Node(int val) {
                this.val = val;
                this.rank = 0;  // 高度，一般来说只有root的rank有意义
                this.parent = this;
            }
        }
        // 用以存放 int 和 node 的对应关系
        private Map<Integer, Node> disjointSet;
    
        // if x is not already in the set, add x
        public void makeSet(int x) {
            if (disjointSet == null) disjointSet = new HashMap<>();
            if (! disjointSet.containsKey(x)) {
                disjointSet.put(x, new Node(x));
            }
        }
    
        public Node find(int x) {
            if (! disjointSet.containsKey(x)) return null;
            Node nodex = disjointSet.get(x);
            return find(nodex);
        }
        // find the root of x, do path compression along recursion
        private Node find(Node nodex) {
            // path compression
            while (nodex.parent != nodex) {
                nodex.parent = find(nodex.parent);
                nodex = nodex.parent;
            }
            return nodex;
        }
    
        public void union(int x, int y) {
            Node rootx = find(x);
            Node rooty = find(y);
            if (rootx == null || rooty == null) return;
            // union by rank 优化
            if (rootx.rank < rooty.rank) {
                rootx.parent = rooty;
            } else if (rootx.rank > rooty.rank) {
                rooty.parent = rootx;
            } else {
                // 只有当合并之前两者rank相同的时候才需要增加rank
                rootx.parent = rooty;
                rooty.rank++;
            }
        }
    
        public static void main(String[] args) {
            // test
            UnionFind uf = new UnionFind();
            // 1,2,3,4,5 五个set
            uf.makeSet(1);
            uf.makeSet(2);
            uf.makeSet(3);
            uf.makeSet(4);
            uf.makeSet(5);
            // for (int i = 1; i < 6; ++i) System.out.println(uf.find(i).val);
    
            // 合并 1，2 以及 3，4，5
            uf.union(1,2);
            uf.union(3,4);
            uf.union(4,5);
            // for (int i = 1; i < 6; ++i) System.out.println(uf.find(i).val);
    
            // 合并 1，5
            uf.union(1, 5);
            for (int i = 1; i < 6; ++i) System.out.println(uf.find(i).val);
        }
    }
```

**简化版本实现（数组实现）：**
这种实现方式更加简短，但是需要预先设定 disjoint set 的 maxSize。








