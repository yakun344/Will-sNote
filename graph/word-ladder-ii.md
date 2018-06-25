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
    /*
     * @param start: a string
     * @param end: a string
     * @param dict: a set of string
     * @return: a list of lists of string
     */
    public List<List<String>> findLadders(String start, String end, Set<String> dict) {
        List<List<String>> res = new ArrayList<>();
        if (start.equals(end)) {
            res.add(new ArrayList<>());
            res.get(0).add(start);
            return res;
        }
        dict.add(start);
        dict.add(end);
        Map<String, Integer> dists = new HashMap<>();
        dists.put(start, Integer.MAX_VALUE);
        dists.put(end, 0);

        int length = getLength(start, end, dict, dists);
        dfs(start, end, new HashSet<String>(), new ArrayList<String>(), res, length, dists);
        return res;
    }

    private int getLength(String start, String end, Set<String> dict, Map<String, Integer> dists) {
        int length = 1;
        Deque<String> queue = new ArrayDeque<>();
        Set<String> visited = new HashSet<>();
        queue.offerLast(end);
        while (! queue.isEmpty()) {
            int size = queue.size();
            for (int k = 0; k < size; ++k) {
                String curr = queue.pollFirst();
                if (! dict.contains(curr) || visited.contains(curr)) continue;
                else if (curr.equals(start)) return length;
                visited.add(curr);
                dists.put(curr, length);
                for (String neighbor : getNeighbors(curr)) {
                    queue.offerLast(neighbor);
                }
            }
            length++;
        }
        return -1;
    }

    private List<String> getNeighbors(String word) {
        List<String> ret = new ArrayList<>();
        char[] arr = word.toCharArray();
        for (int i = 0; i < arr.length; ++i) {
            char t = arr[i];
            for (char c = 'a'; c <= 'z'; ++c) {
                arr[i] = c;
                ret.add(new String(arr));
            }
            arr[i] = t;
        }
        return ret;
    }

    private void dfs(String start, String end, Set<String> visited, List<String> path,
      List<List<String>> res, int remainSteps, Map<String, Integer> dists) {
        if (remainSteps == 1) {
            if (start.equals(end)) {
                path.add(start);
                res.add(new ArrayList<>(path));
                path.remove(path.size() - 1);
            }
            return;
        }
        path.add(start);
        visited.add(start);
        for (String neighbor : getNeighbors(start)) {
            if (visited.contains(neighbor)) continue;
            if (dists.getOrDefault(neighbor, Integer.MAX_VALUE) < dists.get(start)) {
                dfs(neighbor, end, visited, path, res, remainSteps - 1, dists);
            }
        }
        visited.remove(start);
        path.remove(path.size() - 1);
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
    // 主函数
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

    // 获取最短路径长度，从end出发找start，并记录沿途string到end的距离
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

    // 替换每个字母生成所有距离为1的string
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

    // dfs找所有路径，注意在剪枝的时候只考虑有记录距离并且到end距离小于当前点的neighbor
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

---
_update 2018-06-23 22:32:38_

#### Update, 一些思路缕清
先进行一次 BFS，利用其结果优化DFS。首先，我们选择从 end 出发 bfs 搜索 start，并将沿途经过的 word 加入一个 set 中。当开始DFS的时候，只选择在之前 BFS 时候遇到过的 word 继续搜索。这么做的理论依据是当我们从 end 出发 dfs 找到 start 时，其经过的 vertices 一定包含了所有从start出发到end的最短路径中的节点。
