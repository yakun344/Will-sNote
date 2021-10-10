# Topological Sorting

_update Jul 18, 2017 11:11_

[lintcode](http://www.lintcode.com/en/problem/topological-sorting/)

Given an directed graph, a topological order of the graph nodes is defined as follow:

For each directed edge A -> B in graph, A must before B in the order list. The first node in the order can be any node in the graph with no nodes direct to it. Find any topological order for the given graph.

** Notice**

You can assume that there is at least one topological order in the graph.

```
Example
For graph as follow:


The topological order can be:

[0, 1, 2, 3, 4, 5]
[0, 2, 3, 1, 5, 4]
...
```

**Challenge ** Can you do it in both BFS and DFS?

## Basic Idea:

使用Kahn's Algorithm, 因为直接dfs会由于递归栈的关系存在很多隐患，而Kahn算法可以使用BFS。

具体地，说一下大体思路：

1. 首先统计每个node的indegree，存在map或者dict里面。
2. 把indegree为0的node加入res和queue，开始bfs
3. 在bfs的过程中，每次把node的neighbors的indegree都减一（相当于去掉这个node以及其发出的路径），把indegree为0的neighbor加入res和queue，继续bfs。
4. 类似地，dfs的方法就是在遇到neighbor的indegree减一之后变为0时，马上跟进进行dfs。

**Follow up:** 如何判断能否进行 topological sort 呢（即是否有环）？

1. 如果bfs结束之后，仍有indegree > 0的node，则说明至少有一个环。
2. 如果采用下面python dfs的类似方法也是一样，如果仍存在大于0的indegree的node，则有环。
3. (待续)

## Java Code:

```java
    /**
     * Definition for Directed graph.
     * class DirectedGraphNode {
     *     int label;
     *     ArrayList<DirectedGraphNode> neighbors;
     *     DirectedGraphNode(int x) { label = x; neighbors = new ArrayList<DirectedGraphNode>(); }
     * };
     */
    public class Solution {
        /**
         * @param graph: A list of Directed graph node
         * @return: Any topological order for the given graph.
         */ 
        // BFS solution
        public ArrayList<DirectedGraphNode> topSort(ArrayList<DirectedGraphNode> graph) {
            // 先用一个hashmap count indegree
            Map<DirectedGraphNode, Integer> count = new HashMap<>();
            for (DirectedGraphNode node : graph) {
                count.put(node, 0);
            }
            for (DirectedGraphNode node : graph) {
                for (DirectedGraphNode neighbor : node.neighbors) {
                    count.put(neighbor, count.get(neighbor) + 1);
                }
            }
            // 选择indegree为0的所有node，加入res和queue，开始bfs
            ArrayList<DirectedGraphNode> res = new ArrayList<>();
            Deque<DirectedGraphNode> queue = new LinkedList<>();
            for (DirectedGraphNode node : graph) {
                if (count.get(node) == 0) {
                    queue.addFirst(node);
                    res.add(node);
                }
            }
            while (! queue.isEmpty()) {
                DirectedGraphNode node = queue.removeLast();
                for (DirectedGraphNode neighbor : node.neighbors) {
                    count.put(neighbor, count.get(neighbor) - 1);
                    if (count.get(neighbor) == 0) {
                        res.add(neighbor);
                        queue.addFirst(neighbor);
                    }
                }
            }
            return res;
        }
    }
```

## Python Code:

```python
    # Definition for a Directed graph node
    # class DirectedGraphNode:
    #     def __init__(self, x):
    #         self.label = x
    #         self.neighbors = []

    class Solution:
        """
        @param graph: A list of Directed graph node
        @return: A list of graph nodes in topological order.
        """
        def topSort(self, graph):
            def dfs(count, res, node):
                #这里比较关键，如果dfs之后，dfs入口的indegree count仍然为0，则有可能被
                #最外层for loop计算两次，所以这里手动将dfs过的点的indegree设为-1，既可避免重复
                count[node] = -1
                res.append(node)
                for neighbor in node.neighbors:
                    count[neighbor] -= 1
                    if count[neighbor] == 0:
                        dfs(count, res, neighbor)

            # 先用一个dict统计每个node的indegree
            count = collections.defaultdict(int)
            for node in graph:
                for neighbor in node.neighbors:
                    count[neighbor] += 1
            # 然后对所有indegree为0的node进行dfs，每次把相应node所有neighbor的indgree -= 1，
            # 为0的进入下一轮dfs
            res = []
            for node in graph:
                if count[node] == 0:
                    dfs(count, res, node)

            return res
```

_update 2018-05-19 15:36:48_

## C++ DFS Solution

因为topological sort 顺序中考前的node一定最后结束dfs，所以只要进行一次dfs，将每个结束dfs的node顺序插入list尾部，最后将list反向再返回就好了。

```cpp
    public class Solution {
        /*
         * @param graph: A list of Directed graph node
         * @return: Any topological order for the given graph.
         */
        public ArrayList<DirectedGraphNode> topSort(ArrayList<DirectedGraphNode> graph) {
            ArrayList<DirectedGraphNode> res = new ArrayList<>();
            Set<Integer> visited = new HashSet<>();
            for (DirectedGraphNode node : graph) {
                dfs(node, res, visited);
            }
            Collections.reverse(res);
            return res;
        }

        private void dfs(DirectedGraphNode node, List<DirectedGraphNode> res, Set<Integer> visited) {
            if (visited.contains(node.label)) return;
            visited.add(node.label);
            for (DirectedGraphNode neighbor : node.neighbors) {
                dfs(neighbor, res, visited);
            }
            res.add(node);
        }
    }
```

## C++ Kahn's Algorithm Solution

```cpp
    class Solution {
    public:
        /*
         * @param graph: A list of Directed graph node
         * @return: Any topological order for the given graph.
         */
        vector<DirectedGraphNode*> topSort(vector<DirectedGraphNode*>& graph) {
            // 统计每个node的 indegree
            unordered_map<DirectedGraphNode*, int> _count;
            for (auto* node : graph) {
                _count[node];
                for (auto* neighbor : node->neighbors) {
                    ++_count[neighbor];
                }
            }

            // 将 indegree==0 的 node 放入 queue
            deque<DirectedGraphNode*> _queue;
            for (auto _pair : _count) {
                if (_pair.second == 0) _queue.push_back(_pair.first);
            }

            // BFS，每次将当前indegree为0的node的所有neighbors indegree -1，如果为0，
            // 加入 queue
            vector<DirectedGraphNode*> res;
            while (! _queue.empty()) {
                auto* node = _queue.front();
                _queue.pop_front();
                for (auto* neighbor : node->neighbors) {
                    if (--_count[neighbor] == 0) {
                        _queue.push_back(neighbor);
                    }
                }
                res.push_back(node);
            }
            return res;
        }
    };
```
