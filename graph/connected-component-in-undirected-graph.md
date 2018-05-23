## Connected Component in Undirected Graph
_update Jul 19, 2017 17:36_

---
[lintCode](http://www.lintcode.com/en/problem/connected-component-in-undirected-graph/)   
[LeetCode](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/description/)      


Find the number connected component in the undirected graph. Each node in the graph contains a label and a list of its neighbors. (a connected component (or just component) of an undirected graph is a subgraph in which any two vertices are connected to each other by paths, and which is connected to no additional vertices in the supergraph.)

** Notice**

Each connected component should sort by label.

**Example**

    Given graph:
    
    A------B  C
     \     |  | 
      \    |  |
       \   |  |
        \  |  |
          D   E
    Return {A,B,D}, {C,E}. Since there are two connected component which is {A,B,D}, {C,E}
    
#### Basic Idea:
对每一个node进行bfs，每次bfs得到的结果就是一个component，同时在visited中标记即可

#### Java Code:
```java
    /**
     * Definition for Undirected graph.
     * class UndirectedGraphNode {
     *     int label;
     *     ArrayList<UndirectedGraphNode> neighbors;
     *     UndirectedGraphNode(int x) { label = x; neighbors = new ArrayList<UndirectedGraphNode>(); }
     * };
     */
    public class Solution {
        /**
         * @param nodes a array of Undirected graph node
         * @return a connected set of a Undirected graph
         */
        public List<List<Integer>> connectedSet(ArrayList<UndirectedGraphNode> nodes) {
            // 对每一个node进行bfs，每次bfs得到的结果就是一个component，同时在visited中标记即可
            List<List<Integer>> res = new ArrayList<>();
            if (nodes == null || nodes.size() == 0) {
                return res;
            }
            Set<UndirectedGraphNode> visited = new HashSet<>();
            for (UndirectedGraphNode node : nodes) {
                bfs(node, visited, res);
            }
            // 这部分排序其实不必要
            Collections.sort(res, new Comparator<List<Integer>>(){
                public int compare(List<Integer> lst1, List<Integer> lst2) {
                    if (lst1.get(0) < lst2.get(0)) return -1;
                    if (lst1.get(0) == lst2.get(0)) return 0;
                    else return 1;
                }
            });
            return res;
        }
        private void bfs(UndirectedGraphNode node, Set<UndirectedGraphNode> visited, 
                        List<List<Integer>> res) {
            if (visited.contains(node)) return;
            Deque<UndirectedGraphNode> queue = new LinkedList<>();
            List<Integer> component = new ArrayList<>();
            component.add(node.label);
            queue.addFirst(node);
            visited.add(node);
            while (! queue.isEmpty()) {
                node = queue.removeLast();
                for (UndirectedGraphNode neighbor : node.neighbors) {
                    if (visited.contains(neighbor)) continue;
                    queue.addFirst(neighbor);
                    visited.add(neighbor);
                    component.add(neighbor.label);
                }
            }
            Collections.sort(component);
            res.add(component);
        }
    }
```

---
_update Sep 8, 2017  23:21_

#### 更新：Union Find Algorithm Solution
求每个 connected component 中的元素，典型的连通性问题，可以使用并查集解决。  
基本思路：
1.  使用并查集统计所有 v 的连通性；
2.  find 所有 v，使用 `Map<Integer, List<Integer>>` 存储；
3.  按照要求排序，输出；

具体地，为了解决这里nodes的label无规律，无法直接作为 id 的问题，我们可以用一个 `Map<Node, Integer>` 为其人为指定 id，这样就可以通过 node 获取其 id。反过来，因为题目中的node是以list的形式给出，我们只需要以index为id，就可以轻易从id找到node。

```python
    # Union Find Algorithm
    class Solution:
        # @param {UndirectedGraphNode[]} nodes a array of undirected graph node
        # @return {int[][]} a connected set of a undirected graph
        def connectedSet(self, nodes):
            def find(x):
                while ids[x] != x:
                    ids[x] = find(ids[x])
                    x = ids[x]
                return x
                
            def union(x, y):
                rootx = find(x)
                rooty = find(y)
                if rootx == rooty:
                    return
                if rank[rootx] < rank[rooty]:
                    ids[rootx] = rooty
                elif rank[rootx] > rank[rooty]:
                    ids[rooty] = rootx
                else:
                    ids[rootx] = rooty
                    rank[rooty] += 1
            
            # initialize Union-Find        
            ids = [i for i in range(len(nodes))]
            rank = [0 for i in range(len(nodes))]
            
            # 将node和id 一一对应
            # 只需要存储node：id的对应，id：node的对应在nodes中是原生的
            # 因为id就是node在nodes中的index
            nodeId = {}
            for i in range(len(nodes)):
                nodeId[nodes[i]] = i
            
            # do union find
            for node in nodes:
                for neighbor in node.neighbors:
                    union(nodeId[node], nodeId[neighbor])
                    
            # 将每个set中的node进行存储
            components = collections.defaultdict(list)
            for node in nodes:
                id = find(nodeId[node])
                components[id].append(node.label)
                
            # 将components从map中移到list中, 并排序
            ret = [sorted(components[i]) for i in components]
            ret.sort(key = lambda component : component[0])
            
            return ret
```

我们看到，最后的排序部分甚至可以写成一行：
```python
    ret = sorted([sorted(components[i]) for i in components], 
                key = lambda component : component[0])
```
对比上面做了同样事情的 Java Code， Python 的 concise 真是令人叹为观止。

<br>

---
_update 2018-05-22 22:01:39_

#### LeetCode 版本 C++ Code:
```cpp
class Solution {
    vector<int> ids;
    vector<int> ranks;
    int count;
    
    void makeSet(int size) {
        ids.resize(size);
        ranks.resize(size);
        count = size;
        for (int i = 0; i < size; ++i) {
            ids[i] = i;
        }
    }
    
    int find(int id) {
        if (ids[id] == id) return id;
        while (ids[id] != id) {
            id = ids[id];
        }
        return id;
    }
    
    void _union(int x, int y) {
        int rootx = find(x);
        int rooty = find(y);
        if (rootx == rooty) return;
        if (ranks[rootx] < ranks[rooty]) ids[rootx] = rooty;
        else if (ranks[rootx] > ranks[rooty]) ids[rooty] = rootx;
        else {
            ranks[rooty]++;
            ids[rootx] = rooty;
        }
        count--;
    }
public:
    int countComponents(int n, vector<pair<int, int>>& edges) {
        makeSet(n);
        for (auto& edge : edges) {
            int u = edge.first, v = edge.second;
            _union(u, v);
        }
        return count;
    }
};
```














