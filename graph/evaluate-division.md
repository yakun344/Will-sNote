## Evaluate Division
_update Jul,31 2017 22:43_

---
[LeetCode](https://leetcode.com/problems/evaluate-division/description/)

Equations are given in the format A / B = k, where A and B are variables represented as strings, and k is a real number (floating point number). Given some queries, return the answers. If the answer does not exist, return -1.0.

**Example:**

    Given a / b = 2.0, b / c = 3.0.
    queries are: a / c = ?, b / a = ?, a / e = ?, a / a = ?, x / x = ? .
    return [6.0, 0.5, -1.0, 1.0, -1.0 ].

The input is: `vector<pair<string, string>> equations, vector<double>& values, vector<pair<string, string>> queries` , where equations.size() == values.size(), and the values are positive. This represents the equations. Return `vector<double>`.

**According to the example above:**

    equations = [ ["a", "b"], ["b", "c"] ],
    values = [2.0, 3.0],
    queries = [ ["a", "c"], ["b", "a"], ["a", "e"], ["a", "a"], ["x", "x"] ].
    The input is always valid. You may assume that evaluating the queries
    will result in no division by zero and there is no contradiction.

#### Basic Idea:
思路就是有题目所给信息构造一个 graph。例如：对于 a / b = 2，则新建 node a，neighbor b，weight = 2， node b，neighbor a，weight = 1/2。然后我们对每个 query，对 graph 做 dfs，从 query[0] 找到 query[1]，沿途的 edge weight 累乘的积就是结果。

#### Java Code:
```java
    // 正统方法
    public class Solution {
        // 先建graph，然后dfs, 不能用bfs，因为要沿途累乘weight, 跟踪path
        // 图的形式是adjacent list，Map<String, Map<String, Double>>, adj list 是一个map，因为每个neighbor对应一个weight
        public double[] calcEquation(String[][] equations, double[] values, String[][] queries) {
            List<Double> res = new ArrayList<>();
            // init graph
            Map<String, Map<String, Double>> Graph = initGraph(equations, values);
            // 对每个query，dfs graph，从第一个开始找第二个，沿途乘积就是解
            for (String[] query : queries) {
                String start = query[0];
                String target = query[1];
                Set<String> visited = new HashSet<>();
                Double ans = dfs(Graph, start, target, visited, 1);
                if (ans != null) res.add(ans);
                else res.add(-1.0);
            }    
            // convert res list to array
            double[] ret = new double[res.size()];
            for (int i = 0; i < ret.length; ++i) ret[i] = res.get(i);
            return ret;
        }
        private Double dfs(Map<String, Map<String, Double>> Graph,
                           String start, String target, Set<String> visited, double product) {
            if ((! Graph.containsKey(start)) || (! Graph.containsKey(target))) return null;
            if (start.equals(target)) return product;
            if (visited.contains(start)) return null;
            visited.add(start);
            Map<String, Double> neighbors = Graph.get(start);
            for (String neighbor : neighbors.keySet()) {
                Double weight = neighbors.get(neighbor);
                Double ans = dfs(Graph, neighbor, target, visited, product * weight);
                if (ans != null) return ans;
            }
            return null;
        }

        private Map<String, Map<String, Double>> initGraph(String[][] equations, double[] values) {
            Map<String, Map<String, Double>> Graph = new HashMap<>();
            for (int i = 0; i < equations.length; ++i) {
                String d1 = equations[i][0];
                String d2 = equations[i][1];
                double weight = values[i];
                if (! Graph.containsKey(d1)) Graph.put(d1, new HashMap<String, Double>());
                if (! Graph.containsKey(d2)) Graph.put(d2, new HashMap<String, Double>());
                Graph.get(d1).put(d2, weight);
                Graph.get(d2).put(d1, 1 / weight);
            }
            return Graph;
        }
    }
```

#### Python Code:
```python
    class Solution(object):
        def calcEquation(self, equations, values, queries):
            """
            :type equations: List[List[str]]
            :type values: List[float]
            :type queries: List[List[str]]
            :rtype: List[float]
            """
            Graph = self.initGraph(equations, values)
            res = []
            # bfs
            for query in queries:
                start, target = query[0], query[1]
                visited = set()
                ans = self.dfs(Graph, start, target, visited, 1.0)
                if ans is not None: res.append(ans)
                else: res.append(-1.0)
            return res


        # dict<str, dict<str, double>> Graph, str start, str target,
        # set<str> visited, double product
        def dfs(self, Graph, start, target, visited, product):
            if start not in Graph: return None
            if start == target: return product
            if start in visited: return None
            visited.add(start)
            neighbors = Graph[start]
            for neighbor in neighbors:
                weight = neighbors[neighbor]
                ans = self.dfs(Graph, neighbor, target, visited, product * weight)
                if ans is not None: return ans
            return None


        # init graph, dict(str, dict(str, double)).
        def initGraph(self, equations, values):
            Graph = {}
            i = 0
            for equation in equations:
                d1, d2, ans = equation[0], equation[1], values[i]
                i += 1
                if d1 not in Graph: Graph[d1] = {}
                if d2 not in Graph: Graph[d2] = {}
                Graph[d1][d2] = ans
                Graph[d2][d1] = 1 / ans
            return Graph
```

<br>

---
_update 2018-05-24 00:21:10_

#### 更新 C++ BFS Solution:
使用BFS可以更加节省时间，也不需要提前显示建图，但是需要将所有equation的两项互换位置后添加进equations中用来处理倒数的问题。另外query两项相同的情况也要另外处理。

另外，为了记录每个节点的累乘数值，需要建一个inner class(or struct)；

```cpp
class Solution {
    struct Node {
        string end;
        double val;
        Node(string end, double val) {
            this->end = end;
            this->val = val;
        }
    };
public:
    vector<double> calcEquation(vector<pair<string, string>> equations, vector<double>& values,
                                vector<pair<string, string>> queries) {
        vector<double> res;

        int size = equations.size();
        for (int i = 0; i < size; ++i) {
            auto& equation = equations[i];
            equations.push_back(make_pair(equation.second, equation.first));
            values.push_back(1 / values[i]);
        }

        for (auto& query : queries) {
            res.push_back(bfs(equations, values, query));
        }
        return res;
    }

    // bfs 搜索一个query的解
    double bfs(const vector<pair<string, string>>& equations, const vector<double>& values,
               pair<string, string> query) {
        deque<Node> _queue;
        unordered_set<string> visited;

        _queue.push_back(Node(query.first, 1.0));
        bool seenQueryFirst = false; // 标记query.first是否在euqation中出现过，如果没出现过，则不可以返回
        while (! _queue.empty()) {
            Node node = _queue.front();
            _queue.pop_front();
            if (query.first == query.second) {
              if (seenQueryFirst) return 1.0;  
            } else if (node.end == query.second) {
                return node.val;
            }
            for (int i = 0; i < equations.size(); ++i) {
                auto& equation = equations[i];
                if (equation.first == query.first) seenQueryFirst = true;
                if (equation.first == node.end && ! visited.count(equation.first + "," + equation.second)) {
                    visited.insert(equation.first + "," + equation.second);
                    _queue.push_back(Node(equation.second, node.val * values[i]));
                }
            }
        }
        return -1.0;
    }
};
```

#### C++ DFS Solution:
C++ 版本的建图的解法，和之前Java的实现相比有一些优化：

1. 图的形式不必是两层HashMap，外层其实可以直接用vector（list）；
2. 在dfs的过程中，只要出现一次 childRet 不为 -1，就可以终止余下部分的dfs，这一步剪枝可以减少很多工作；

```cpp
class Solution {
public:
    vector<double> calcEquation(vector<pair<string, string>> equations, vector<double>& values, vector<pair<string, string>> queries) {
        vector<double> res;
        auto& graph = *(initGraph(equations, values));
        for (auto& query : queries) {
            unordered_set<string> visited;
            res.push_back(dfs(graph, query.first, query.second, 1.0, visited));
        }
        return res;
    }

    unordered_map<string, vector<pair<string, double>>>* initGraph(const vector<pair<string, string>>& equations,
                                                                   const vector<double>& values) {
        auto* graph = new unordered_map<string, vector<pair<string, double>>>();
        for (int i = 0; i < equations.size(); ++i) {
            string a = equations[i].first, b = equations[i].second;
            double val = values[i];
            (*graph)[a].push_back(make_pair(b, val));
            (*graph)[b].push_back(make_pair(a, 1.0 / val));
        }
        return graph;
    }

    double dfs(unordered_map<string, vector<pair<string, double>>>& graph, string start, string end, double product,
               unordered_set<string>& visited) {
        if (visited.count(start) || ! graph.count(start)) return -1;
        else if (start == end) return product;
        visited.insert(start);
        double ret = -1.0;
        for (const pair<string, double>& neighbor : graph[start]) {
            double childRet = dfs(graph, neighbor.first, end, product * neighbor.second, visited);
            if (childRet != -1) {
                ret = childRet;
                break;
            }
        }
        return ret;
    }
};
```

---
_update 2018-06-24 20:48:22_

### Update，C++ BFS Solution
这次的实现中不再使用自定义struct储存node信息，而是用 pair 存储。Node（Vertex）其实只需要两个field：(上一个分母和当前的value)。

```cpp
class Solution {
    double bfs(vector<pair<string, string>>& equations, vector<double>& values, pair<string, string>& query) {
        // 从query.first 到 query.second bfs 搜索，queue中存入 `pair<当前最后一个query.second, 当前val>`
        deque<pair<string, double>> q;
        unordered_set<string> visited;
        q.push_back(make_pair(query.first, 1.0));
        visited.insert(query.first + "," + to_string(1.0));
        bool meetQueryFirst = false;
        while (! q.empty()) {
            auto vertex = q.front(); q.pop_front();
            if (query.first == query.second && meetQueryFirst) return 1.0;
            for (int i = 0; i < equations.size(); ++i) {
                auto equation = equations[i];
                if (equation.first == query.first) meetQueryFirst = true;
                if (equation.first == vertex.first) {
                    auto nextVertex = make_pair(equation.second, vertex.second * values[i]);
                    if (! visited.insert(nextVertex.first + "," + to_string(nextVertex.second)).second)
                        continue;
                    if (nextVertex.first == query.second) return nextVertex.second;
                    else q.push_back(nextVertex);
                }
            }
        }
        return -1.0;
    }
public:
    vector<double> calcEquation(vector<pair<string, string>> equations, vector<double>& values, vector<pair<string, string>> queries) {
        // create containers
        vector<double> res;

        // 将倒数情况加入equations和values中
        int size = equations.size();
        for (int i = 0; i < size; ++i) {
            auto equation = equations[i];
            equations.push_back(make_pair(equation.second, equation.first));
            values.push_back(1.0 / values[i]);
        }

        // 对每个query，bfs找出答案，存入res中
        for (auto query : queries) {
            res.push_back(bfs(equations, values, query));
        }

        return res;
    }
};
```
