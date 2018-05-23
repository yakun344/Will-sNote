## Word Ladder II
_update Jul 21, 2017 21:42_

---
[LintCode](http://www.lintcode.com/en/problem/word-ladder-ii/)

Given two words (start and end), and a dictionary, find all shortest transformation sequence(s) from start to end, such that:

Only one letter can be changed at a time
Each intermediate word must exist in the dictionary

**Notice**

*  All words have the same length.
*  All words contain only lowercase alphabetic characters.

**Example**

    Given:
      start = "hit"
      end = "cog"
      dict = ["hot","dot","dog","lot","log"]
    Return
      [
        ["hit","hot","dot","dog","cog"],
        ["hit","hot","lot","log","cog"]
      ]
      
#### Basic Idea:
这道题目的难点是要求出所有最短路径。之前的 **[Word Ladder I](https://will-gxz.gitbooks.io/xiaozheng_algo/content/graph/word-ladder.html)**只要求输出最短路径的长度，我们只需要做一次BFS即可，但是现在要求所有的最短路径，我们就需要结合DFS。因为BFS得到的结果是一个一个分层的点，我们所能知道的只有起点到达某个状态需要几步，

具体地，我们可以先用BFS确定最短路径的长度，然后用DFS找到所有路径。我们还可以利用BFS的结果进行**优化**：
1.  从 end 开始做 BFS 找 start，记录沿途各点到 end 的距离，找到 start 就可以停止。
2.  将其他点与 end 的距离都标记为无穷。
3.  当我们DFS的时候，首先只选择有距离记录的相邻点开始，并且每次都只选择更近的点继续DFS，这样可以避免很多没有必要的搜索。当步数超过最短步数时则停止dfs。
4.  注意dfs出口的细节。

#### Java Code:
```java
    public class Solution {
        /**
          * @param start, a string
          * @param end, a string
          * @param dict, a set of string
          * @return a list of lists of string
          */
        public List<List<String>> findLadders(String start, String end, Set<String> dict) {
            List<List<String>> res = new ArrayList<>();
            if (start.equals(end)) {
                res.add(new ArrayList<String>());
                res.get(0).add(start);
                return res;
            }
            
            dict.add(start);
            dict.add(end);
            Map<String, Integer> distances = new HashMap<>();
            for (String node : dict) {
                distances.put(node, Integer.MAX_VALUE);
            }
            distances.put(end, 1); // 令自身距离为1
            
            // bfs
            int minStep = bfs(end, start, dict, distances);
    
            // dfs
            Set<String> visited = new HashSet<>();
            dfs(start, end, new ArrayList<String>(), res, visited, distances, dict, minStep);
            return res;
        }
        
        // 从start开始dfs搜索end，每次只选择距离end更近的点深入
        private void dfs(String node,
                        String target,
                        List<String> path, 
                        List<List<String>> res,
                        Set<String> visited, 
                        Map<String, Integer> distances,
                        Set<String > dict, 
                        int remainSteps) {
        // 这里用 1 做终点是因为题目的定义是路径长度，当start == end的时候，长度是1
        // 当一步就可以从start到end时，长度是2，所以定1为终点。
            if (remainSteps < 1) return;
            if (remainSteps == 1) {
                if (node.equals(target)) {
                    path.add(node);
                    res.add(new ArrayList<String>(path));
                    path.remove(path.size() - 1);
                    return;
                } else {
                    return;
                }
            }
            
            visited.add(node);
            path.add(node);
            for (String neighbor : getNeighbors(node, dict, visited)) {
                if (distances.get(neighbor) >= distances.get(node)) continue;
                dfs(neighbor, target, path, res, visited, distances, dict, remainSteps - 1);
            }
            visited.remove(node);
            path.remove(path.size() - 1);
        }
        
        // 用BFS从end开始搜索start，标记各点到end的距离
        private int bfs(String node, String target, Set<String> dict, Map<String, Integer> distances) {
            Deque<String> queue = new LinkedList<>();
            Set<String> visited = new HashSet<>();
            queue.addFirst(node);
            visited.add(node);
            int step = 1;
            while (! queue.isEmpty()) {
                int size = queue.size();
                step++;
                for (int i = 0; i < size; ++i) {
                    node = queue.removeLast();
                    for (String neighbor : getNeighbors(node, dict, visited)) {
                        distances.put(neighbor, step);
                        if (neighbor.equals(target)) return step;
                        queue.addFirst(neighbor);
                        visited.add(neighbor);
                    }
                }
            }
            return -1; // fail
        }
        
        // 求所有相邻一个编辑距离在dict中不在visited中的neighbor
        private List<String> getNeighbors(String node, Set<String> dict, Set<String> visited) {
            List<String> neighbors = new ArrayList<>();
            for (int i = 0; i < node.length(); ++i) {
                for (int c = 'a'; c <= 'z'; ++c) {
                    String neighbor = replace(node, i, (char)c);
                    if (! dict.contains(neighbor)) continue;
                    if (visited.contains(neighbor)) continue;
                    neighbors.add(neighbor);
                }
            }
            return neighbors;
        }
        
        private String replace(String str, int index, char c) {
            char[] arr = str.toCharArray();
            arr[index] = c;
            return String.valueOf(arr);
        }
    }
```

#### Python Code:
```python
    class Solution:
        # @param start, a string
        # @param end, a string
        # @param dict, a set of string
        # @return a list of lists of string
        def findLadders(self, start, end, dict):
            if start == end:
                return [[start]]
            dict = set(dict)
            dict.add(start)
            dict.add(end)
            
            # bfs找从end到start最短路径, 并沿途记录各点距离end的路径长度
            distances = {node : float('INF') for node in dict}
            distances[end] = 1
            minStep = self.bfs(end, start, dict, distances)
            
            # dfs
            res = []
            self.dfs(start, end, dict, set(), distances, minStep, res, [])
            return res
            
        # 使用BFS找到从end到start的最短路径长度
        # str start, str target, set<str> dict
        def bfs(self, start, target, dict, distances):
            visited = set()
            queue = collections.deque()
            queue.appendleft(start)
            visited.add(start)
            step = 1
            while queue:
                step += 1
                size = len(queue)
                for i in range(size):
                    node = queue.pop()
                    for neighbor in self.getNeighbors(node, dict, visited):
                        if neighbor == target: return step
                        visited.add(neighbor)
                        queue.appendleft(neighbor)
                        distances[neighbor] = step
                                    
        
        # str node, set<str> dict, set<str> visited
        def getNeighbors(self, node, dict, visited):
            neighbors = []
            for i in range(len(node)):
                for c in range(ord('a'), ord('z') + 1):
                    neighbor = node[: i] + chr(c) + node[i + 1 :]
                    if neighbor in dict and neighbor not in visited:
                        neighbors.append(neighbor)
            return neighbors
            
        
        # 使用DFS找到所有长度等于最短路径的从start到end的路径
        def dfs(self, start, target, dict, visited, distances, remainSteps, res, path):
            # 出口
            if remainSteps < 1: return
            if remainSteps == 1:
                if start == target:
                    path.append(start)
                    res.append(path[:])
                    del path[-1]
                else:
                    return
            
            visited.add(start)
            path.append(start)
            for neighbor in self.getNeighbors(start, dict, visited):
                # 只选择距离end更近的点
                if distances[start] <= distances[neighbor]: 
                    continue
                self.dfs(neighbor, target, dict, visited, distances, remainSteps - 1, res, path)
            del path[-1]
            visited.remove(start)
```

<br>

---
_update 2018-05-23 18:16:44_
#### C++ Code:
很长时间没有写比较复杂的搜索类题目，一些细节已经忘记了。

1. 需要跟踪 step 数量的时候用分层 BFS 要记得加上关于 queue.size 的循环。
2. 在图的DFS中，如果要获取全部路径，需要考虑到两条路径会在某一节点合并的问题，这种情况下就需要对 visited set 也进行 backtrack，保证下级dfs不会影响到上级对其他路径的搜索。

```cpp
class Solution {
public:
    /*
     * @param start: a string
     * @param end: a string
     * @param dict: a set of string
     * @return: a list of lists of string
     */
    vector<vector<string>> findLadders(string &start, string &end, unordered_set<string> &dict) {
        vector<vector<string>> res;
        if (start == end) {
            res.push_back({start});
            return res;
        }
        dict.insert(start);
        dict.insert(end);
        unordered_map<string, int> dists;
        dists[end] = 0;
        dists[start] = 0x7fffffff;
        int length = getLength(dict, dists, start, end);
        vector<string> path;
        unordered_set<string> visited;
        dfs(dict, start, end, path, res, visited, length, dists);
        return res;
    }
    
    int getLength(unordered_set<string>& dict, unordered_map<string, int>& dists, string start, string end) {
        unordered_set<string> visited;
        deque<string> _queue;
        _queue.push_back(end);
        int step = 1;
        while (! _queue.empty()) {
            int size = _queue.size();
            for (int k = 0; k < size; ++k) {
                string curr = _queue.front();
                _queue.pop_front();
                if (! dict.count(curr) || visited.count(curr)) {
                    continue;
                } else if (curr == start) {
                    cout << step << endl;
                    return step;
                } else {
                    dists[curr] = step;
                    visited.insert(curr);
                }
                vector<string> neighbors = getNeighbors(curr);
                for (string& neighbor : neighbors) {
                    _queue.push_back(neighbor);
                }
            }
            ++step;
        }
        return -1;
    } 
    
    vector<string> getNeighbors(const string& word) {
        vector<string> ret;
        for (int i = 0; i < word.size(); ++i) {
            string neighbor = word;
            for (char c = 'a'; c <= 'z'; ++c) {
                neighbor[i] = c;
                ret.push_back(neighbor);
            }
        }
        return move(ret);
    }
    
    void dfs(unordered_set<string>& dict, string start, string end, vector<string>& path, 
      vector<vector<string>>& res, unordered_set<string>& visited, int remainSteps, 
      unordered_map<string, int>& dists) {
        if (remainSteps == 1) {
            if (start == end) {
                path.push_back(start);
                res.push_back(path);
                path.pop_back();
            }
            return;
        }
        else if (visited.count(start)) return;
        path.push_back(start);
        visited.insert(start);
        for (string neighbor : getNeighbors(start)) {
            if (! dists.count(neighbor) || dists[neighbor] >= dists[start]) continue;
            dfs(dict, neighbor, end, path, res, visited, remainSteps - 1, dists);
        }
        visited.erase(start);
        path.pop_back();
    }
};
```















