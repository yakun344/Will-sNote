# Is Graph Bipartite? \(Medium\)

_update Mar 4, 2018 13:33_

[LeetCode](https://leetcode.com/problems/is-graph-bipartite/description/)

Given a graph, return true if and only if it is bipartite.

Recall that a graph is bipartite if we can split it's set of nodes into two independent subsets A and B such that every edge in the graph has one node in A and another node in B.

The graph is given in the following form: `graph[i]` is a list of indexes `j` for which the edge between nodes `i` and `j` exists. Each node is an integer between `0` and `graph.length - 1`. There are no self edges or parallel edges: `graph[i]` does not contain `i`, and it doesn't contain any element twice.

**Example 1:**

```text
Input: [[1,3], [0,2], [1,3], [0,2]]
Output: true
Explanation:
The graph looks like this:
0----1
|    |
|    |
3----2
We can divide the vertices into two groups: {0, 2} and {1, 3}.
```

**Example 2:**

```text
Input: [[1,2,3], [0,2], [0,1,3], [0,2]]
Output: false
Explanation:
The graph looks like this:
0----1
| \  |
|  \ |
3----2
We cannot find a way to divide the set of nodes into two independent subsets.
```

**Note:**

1. `graph` will have length in range `[1, 100]`.
2. `graph[i]` will contain integers in range `[0, graph.length - 1]`.
3. `graph[i]` will not contain `i` or duplicate values.

## Basic Idea:

* **思路 1：（BFS + 2 sets）**

  用两个set `U and V` 表示两个 subset，确保一个set中的node的neighbor都在另一个set中，当发现有两个相连的node在同一个set中时，返回false；

  * **Java Code:**

    ```java
    class Solution {
        public boolean isBipartite(int[][] graph) {
            if (graph == null || graph.length == 0) return true;
            Set<Integer> U = new HashSet<>();
            Set<Integer> V = new HashSet<>();
            Deque<Integer> queue = new ArrayDeque<>();
            for (int node = 0; node < graph.length; ++node) {
                if (! U.contains(node) && ! V.contains(node)) {
                    queue.offerFirst(node);
                    while (! queue.isEmpty()) {
                        int curr = queue.pollLast();
                        for (int neighbor : graph[curr]) {
                            if (U.contains(curr) && U.contains(neighbor)
                               || V.contains(curr) && V.contains(neighbor)) {
                                return false;
                            } else if (U.contains(curr)) {
                                if (V.add(neighbor)) {
                                    queue.offerFirst(neighbor);
                                }
                            } else {
                                if (U.add(neighbor)) {
                                    queue.offerFirst(neighbor);
                                }
                            }
                        }
                    }
                }
            }
            return true;
        }
    }
    ```

* **思路 2：（DFS + 2 sets）**

  和之前的BFS思路类似，也是用两个set表示不同部分，然后再dfs的过程中将相连node放入不同的set，同时检查当前node和其neighbor是否在同一个set中；时间复杂度都是 `O(|E|)`，因为都遍历了所有的边；

  * **Java Code:**

    ```java
    class Solution {
        public boolean isBipartite(int[][] graph) {
            if (graph == null || graph.length == 0) return true;
            Set<Integer> U = new HashSet<>();
            Set<Integer> V = new HashSet<>();
            for (int node = 0; node < graph.length; ++node) {
                if (U.contains(node) || V.contains(node)) {
                    continue;
                } else {
                    U.add(node);
                    if (! dfs(graph, node, U, V)) return false;
                }
            }
            return true;
        }

        private boolean dfs(int[][] graph, int curr, Set<Integer> U, Set<Integer> V) {
            for (int neighbor : graph[curr]) {
                if (U.contains(curr) && U.contains(neighbor)
                   || V.contains(curr) && V.contains(neighbor)) {
                    return false;
                } else if (U.contains(curr)) {
                    if (V.add(neighbor)) {
                        if (! dfs(graph, neighbor, U, V)) return false;
                    }
                } else {
                    if (U.add(neighbor)) {
                        if (! dfs(graph, neighbor, U, V)) return false;
                    }
                }
            }
            return true;
        }
    }
    ```

  * **Java Code:**

    不用 set，而是使用一个vector来记录两种颜色，0 表示未见，1 2 分别表示两个set。

    ```java
        class Solution {
            public boolean isBipartite(int[][] graph) {
                int[] colors = new int[graph.length];
                for (int i = 0; i < graph.length; ++i) {
                    if (colors[i] != 0) continue;
                    colors[i] = 1;
                    if (! dfs(graph, colors, i)) return false;
                }
                return true;
            }

            private boolean dfs(int[][] graph, int[] colors, int v) {
                for (int neighbor : graph[v]) {
                    if (colors[neighbor] == 0) {
                        colors[neighbor] = colors[v] == 1 ? 2 : 1;
                        if (! dfs(graph, colors, neighbor)) return false;
                    } else if (colors[neighbor] == colors[v]) {
                        return false;
                    }
                }
                return true;
            }
        }
    ```

_update 2018-06-27 22:50:08_

#### Update C++ BFS 染色解法

```cpp
  class Solution {
public:
    bool isBipartite(vector<vector<int>>& graph) {
        vector<int> colors(graph.size());
        deque<int> q;
        // 0=not visited, 1=group1, 2=group2

        // BFS, 每次bfs完成之后将其余不相连的node加入queue中继续BFS
        for (int i = 0; i < graph.size(); ++i) {
            if (colors[i] == 0) {
                q.push_back(i);
                colors[i] = 1;
            }
            while (! q.empty()) {
                int curr = q.front(); q.pop_front();
                int currColor = colors[curr];
                for (int neighbor : graph[curr]) {
                    if (colors[neighbor] == 0) {
                        colors[neighbor] = currColor == 1 ? 2 : 1;
                        q.push_back(neighbor);
                    } else if (colors[neighbor] == currColor) {
                        return false;
                    }
                }
            }
        }
        return true;
    }
};
```

_update Feb 25 2019, 21:53_

### Update

在vmw的onsite中遇到了这道题的变种，但因为和上次做题的时间相隔了近一年，几乎想不起最优解的做法，于是在面试中给出了一个现场想出来的解，应该可以work，只是会比较别扭。所以说还是需要注意对从前做过的题目进行复习，尤其是Graph和Tree以及DP这种套路比较强的类型题目。

**双色涂色法的思考：**  
虽然我们是想办法将图分成两部分，使得任意一部分内部node之间没有边相连，但事实上如果一个connected component自身和整个图但其他部分没有联系，我们是可以自由决定将这个component的两部分的摆放顺序的。所以在一开始的时候，对于没有被染色过的node，我们可以直接将其染为1。 接下来我们只需要对与该node相连的component之间交替染色，同时检查是否valid。所以，我们可以BFS或者DFS，都不重要，因为我们只需要每次从一个node出发，将其所在的connected component nodes交替染色。

