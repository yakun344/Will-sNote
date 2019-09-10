# Word Ladder

_update Jul 21, 2017 16:45_

[LintCode](http://www.lintcode.com/en/problem/word-ladder/)

Given two words \(start and end\), and a dictionary, find the length of shortest transformation sequence from start to end, such that:

* Only one letter can be changed at a time
* Each intermediate word must exist in the dictionary

 **Notice**

* Return 0 if there is no such transformation sequence.
* All words have the same length.
* All words contain only lowercase alphabetic characters.

**Example**

```text
Given:
start = "hit"
end = "cog"
dict = ["hot","dot","dog","lot","log"]
As one shortest transformation is "hit" -> "hot" -> "dot" -> "dog" -> "cog",
return its length 5.
```

## Basic Idea:

这道题目其实是一个隐式图搜索的题目，要求的是最短的变化路径长度，所以首选BFS。 ![](../../.gitbook/assets/screen-shot-2017-07-21-at-4.49.42-pm%20%281%29.png) 如上就是这个图的例子（来自jiuzhang）。 实现的基本思路就是每次考虑距离当前word只有一个编辑距离的dict中的词，如果还没有visit过，则加入queue，分层bfs，记录step数。 **细节:** 在生成 1 编辑距离的neighbors时，如果对所有dict中的单词逐一检查，是`O(#words in dict * length)` 的时间复杂度，所以这里采用生成所有 1 编辑距离的变化的单词，然后查看dict中是否有这个词，是`O(26 * length^2)`的复杂度，在dict中word很多而长度较短的时候，更有优势。

## python code:

```python
    class Solution:
        # @param start, a string
        # @param end, a string
        # @param dict, a set of string
        # @return an integer
        def __init__(self):
            self.visited = set()

        def ladderLength(self, start, end, dict):
            if start == end:
                return 1
            dict.add(end)

            # bfs
            queue = collections.deque()
            queue.appendleft(start)
            step = 1
            while queue:
                size = len(queue)
                step += 1
                for i in range(size):
                    node = queue.pop()
                    for neighbor in self.getNeighbors(node, dict):
                        if neighbor == end: return step
                        queue.appendleft(neighbor)
                        self.visited.add(neighbor)
            return 0

        def replace(self, str, index, c):
            lst = list(str)
            lst[index] = c
            return ''.join(lst)

        def getNeighbors(self, str, dict):
            ret = []
            for i in range(len(str)):
                for c in range(ord('a'), ord('z') + 1):
                    neighbor = self.replace(str, i, chr(c))
                    if neighbor in dict:
                        if neighbor in self.visited:
                            continue
                        ret.append(neighbor)
            return ret
```

## java code:

```java
    public class Solution {
        /**
          * @param start, a string
          * @param end, a string
          * @param dict, a set of string
          * @return an integer
          */
        private Set<String> visited;

        public int ladderLength(String start, String end, Set<String> dict) {
            if (start.equals(end)) return 1;
            dict.add(end); // 先把end加入dict，否则无法直接判断终点
            visited = new HashSet<String>();
            Deque<String> queue = new LinkedList<>();
            queue.addFirst(start);
            int step = 1;
            while (! queue.isEmpty()) {
                int size = queue.size();
                step++;
                for (int i = 0; i < size; ++i) {
                    String node = queue.removeLast();
                    for (String neighbor : getNeighbors(node, dict)) {
                        // 无需判断是否在visited中，因为getNeighbors已经判断过了
                        if (neighbor.equals(end)) return step;
                        queue.addFirst(neighbor);
                        visited.add(neighbor);
                    }
                }
            }
            return 0;
        }

        private String replace(String str, int index, char c) {
            char[] arr = str.toCharArray();
            arr[index] = c;
            return String.valueOf(arr);
        }

        private List<String> getNeighbors(String node, Set<String> dict) {
            List<String> ret = new ArrayList<>();
            for (int i = 0; i < node.length(); ++i) {
                for (int c = 'a'; c <= 'z'; ++c) {
                    String str = replace(node, i, (char)c);
                    if (dict.contains(str)) {
                        if (! visited.contains(str)) {
                            ret.add(str);
                        }
                    }
                }
            }
            return ret;
        }
    }
```

_update 2018-06-23 22:07:35_

## Update C++ BFS Solution

```cpp
class Solution {
public:
    int ladderLength(string &start, string &end, unordered_set<string> &dict) {
        if (start == end) return 1;
        dict.insert(start);
        dict.insert(end);
        deque<string> q;
        q.push_back(start);
        unordered_set<string> visited;
        visited.insert(start);
        int step = 1;
        while (! q.empty()) {
            int size = q.size();
            for (int i = 0; i < size; ++i) {
                string curr = q.front(); q.pop_front();
                vector<string> nextWords;
                getNextWord(curr, dict, nextWords);
                for (string nextWord : nextWords) {
                    // visit 当前word，检查其是否已经visited，如果不是，将其加入visited set
                    if (! visited.insert(nextWord).second) continue;
                    if (nextWord == end) return step + 1;
                    q.push_back(nextWord);
                }
            }
            ++step;
        }
        return -1;
    }
private:
    void getNextWord(const string& curr, const unordered_set<string>& dict, vector<string>& res) {
        for (int i = 0; i < curr.size(); ++i) {
            for (int j = 0; j < 25; ++j) {
                string s = curr;
                s[i] = 'a' + j;
                if (s != curr && dict.count(s)) {
                    res.push_back(s);
                }
            }
        }
    }
};
```

_update Sep 9, 2019_

## Update, Leetcode version

Leetcode 上的版本和lintcode有些许出入，但只需要略加修改即可：

### Java Code:
```java
    public class Solution {
        /**
          * @param start, a string
          * @param end, a string
          * @param dict, a set of string
          * @return an integer
          */
        private Set<String> visited;

        public int ladderLength(String start, String end, Set<String> dict) {
            if (start.equals(end)) return 1;
            dict.add(end); // 先把end加入dict，否则无法直接判断终点
            visited = new HashSet<String>();
            Deque<String> queue = new LinkedList<>();
            queue.addFirst(start);
            int step = 1;
            while (! queue.isEmpty()) {
                int size = queue.size();
                step++;
                for (int i = 0; i < size; ++i) {
                    String node = queue.removeLast();
                    for (String neighbor : getNeighbors(node, dict)) {
                        // 无需判断是否在visited中，因为getNeighbors已经判断过了
                        if (neighbor.equals(end)) return step;
                        queue.addFirst(neighbor);
                        visited.add(neighbor);
                    }
                }
            }
            return 0;
        }

        private String replace(String str, int index, char c) {
            char[] arr = str.toCharArray();
            arr[index] = c;
            return String.valueOf(arr);
        }

        private List<String> getNeighbors(String node, Set<String> dict) {
            List<String> ret = new ArrayList<>();
            for (int i = 0; i < node.length(); ++i) {
                for (int c = 'a'; c <= 'z'; ++c) {
                    String str = replace(node, i, (char)c);
                    if (dict.contains(str)) {
                        if (! visited.contains(str)) {
                            ret.add(str);
                        }
                    }
                }
            }
            return ret;
        }
    }
```
