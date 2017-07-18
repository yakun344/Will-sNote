## Sequence Reconstruction
_update Jul 18, 2017 15:35_

---
[lintcode](http://www.lintcode.com/en/problem/sequence-reconstruction/)

Check whether the original sequence org can be uniquely reconstructed from the sequences in seqs. The org sequence is a permutation of the integers from 1 to n, with 1 ≤ n ≤ 10^4. Reconstruction means building a shortest common supersequence of the sequences in seqs (i.e., a shortest sequence so that all sequences in seqs are subsequences of it). Determine whether there is only one sequence that can be reconstructed from seqs and it is the org sequence.

**Example**
    Given org = [1,2,3], seqs = [[1,2],[1,3]]
    Return false
    Explanation:
    [1,2,3] is not the only one sequence that can be reconstructed, 
    because [1,3,2] is also a valid sequence that can be reconstructed.
    
    Given org = [1,2,3], seqs = [[1,2]]
    Return false
    Explanation:
    The reconstructed sequence can only be [1,2].
    
    Given org = [1,2,3], seqs = [[1,2],[1,3],[2,3]]
    Return true
    Explanation:
    The sequences [1,2], [1,3], and [2,3] can uniquely reconstruct 
    the original sequence [1,2,3].
    
    Given org = [4,1,5,2,6,3], seqs = [[5,2,6,3],[4,1,5,2]]
    Return true

#### Basic Idea:
这道题其实就是求是否有唯一的topological sort的结果。什么情况下会有不止一种结果呢？就是当某一时刻有不止一个indegree为0的node的时候。所以我们只需要监测`queue.size() > 1`即可。

具体的，有一些非法输入的情况需要handle。

#### Java Code:
```java
    public class Solution {
        /**
         * @param org a permutation of the integers from 1 to n
         * @param seqs a list of sequences
         * @return true if it can be reconstructed only one or false
         */
        public boolean sequenceReconstruction(int[] org, int[][] seqs) {
            Map<Integer, Set<Integer>> graph = initGraph(org, seqs);
            if (graph == null) return false;
            // count indegree
            Map<Integer, Integer> count = new HashMap<>();
            for (int node : org) {
                count.put(node, 0);
            }
            for (int node : org) {
                for (int neighbor : graph.get(node)) {
                    count.put(neighbor, count.get(neighbor) + 1);
                }
            }
            // bfs, topological sort, check if queue.size() > 1
            Deque<Integer> queue = new LinkedList<>();
            for (int node : org) {
                if (count.get(node) == 0) queue.addFirst(node);
            }
            while (! queue.isEmpty()) {
                if (queue.size() > 1) return false;
                int node = queue.removeLast();
                for (int neighbor : graph.get(node)) {
                    count.put(neighbor, count.get(neighbor) - 1);
                    if (count.get(neighbor) == 0) queue.addFirst(neighbor);
                }
            }
            for (int node : org) {
                if (count.get(node) > 0) return false;
            }
            return true;
        }
        private Map<Integer, Set<Integer>> initGraph(int[] nodes, int[][] seqs) {
            Map<Integer, Set<Integer>> graph = new HashMap<>();
            int nodeNum = 0;
            for (int node : nodes) {
                graph.put(node, new HashSet<Integer>());
            }
            for (int[] edges : seqs) {
                nodeNum += edges.length;
                if (edges.length == 1 && (edges[0] < 1 || edges[0] > nodes.length)) {
                    return null;
                }
                for (int i = 1; i < edges.length; ++i) {
                    int u = edges[i - 1], v = edges[i];
                    if (Math.max(u, v) > nodes.length || Math.min(u, v) < 1) return null;
                    graph.get(u).add(v);
                }
            }
            if (nodeNum < nodes.length) return null;
            return graph;
        }
    }
```

#### Python Code:
```python
    class Solution:
        # @param {int[]} org a permutation of the integers from 1 to n
        # @param {int[][]} seqs a list of sequences
        # @return {boolean} true if it can be reconstructed only one or false
        def sequenceReconstruction(self, org, seqs):
            def initGraph(nodes, seqs):
                count = 0
                graph = {}
                for node in nodes:
                    graph[node] = set()
                for edges in seqs:
                    count += len(edges)
                    if len(edges) == 1:
                        if edges[0] < 1 or edges[0] > len(nodes):
                            return None # case: [[100000000]]
                    for i in range(1, len(edges)):
                        u, v = edges[i - 1], edges[i]
                        if (min(u, v) < 1 or max(u, v) > len(nodes)):
                            return None # case: [[-1, 1000000000]]
                        graph[u].add(v)
                if count < len(nodes): 
                    return None # case: [1], [[],[]]
                return graph
                
            
            graph = initGraph(org, seqs)
            if graph is None: 
                return False
            # count indegree
            count = {}
            for node in graph:
                count[node] = 0
            for node in graph:
                for neighbor in graph[node]:
                    count[neighbor] += 1
            
            # BFS, topological sort, 看queue的size是否会大于1
            queue = collections.deque()
            for node in graph:
                if count[node] == 0:
                    queue.appendleft(node)
            while queue:
                if len(queue) > 1:
                    return False
                node = queue.pop()
                for neighbor in graph[node]:
                    count[neighbor] -= 1
                    if count[neighbor] == 0:
                        queue.appendleft(neighbor)
            
            return not [node for node in graph if count[node] > 0]
```