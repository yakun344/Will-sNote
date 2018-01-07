## Remove Substrings
_update Sep 14,2017  21:40_

---
[LeetCode](http://www.lintcode.com/en/problem/remove-substrings/)

Given a string s and a set of n substrings. You are supposed to remove every instance of those n substrings from s so that s is of the minimum length and output this minimum length.

**Example**

    Given s = ccdaabcdbb, substrs = ["ab", "cd"]
    Return 2

**Explanation: **

ccdaabcdbb -> ccdacdbb -> cabb -> cb (length = 2)

#### Basic Idea:
因为移除 substring 的顺序会对结果造成影响，我们需要使用 bfs 搜索所有可能的删除顺序，输出最短的最终string长度。

1.  使用 `s.indexOf(pattern, start)`(java) or `s.find(pattern, start)`(python) 获得要删除的 substring 的index;
2.  对于每个 pattern, 每层bfs删除一个，但是需要考虑到不同位置，所以操作流程是：先得到第一个出现的 pos，将删掉pattern之后的string加入queue，把 start 调整到 pos + 1，对原string继续，直到输出 -1；
3.  当所有 pattern 都不在结果中出现时，也就是 queue 为空时，结束；

#### Java Code:
```java
    public class Solution {
        /**
         * @param s a string
         * @param dict a set of n substrings
         * @return the minimum length
         */
        public int minLength(String s, Set<String> dict) {
            if (s == null || s.length() == 0) return 0;
            Deque<String> queue = new LinkedList<>();
            Set<String> visited = new HashSet<>();
            queue.addFirst(s);
            visited.add(s);
            int minLen = s.length();
            
            while (! queue.isEmpty()) {
                String curr = queue.removeLast();
                for (String pattern : dict) {
                    int pos = -1;
                    for (;;) {
                    // 考虑每个pattern的所有出现位置，分别把删除相应位置之后的string加入queue
                        pos = curr.indexOf(pattern, pos + 1);
                        if (pos == -1) break;
                        String out = curr.substring(0, pos) 
                                     + curr.substring(pos + pattern.length(), curr.length());
                        if (visited.contains(out)) continue;
                        visited.add(out);
                        queue.addFirst(out);
                    }
                }
                minLen = Math.min(minLen, curr.length());
            }
            return minLen;
        }
    }
```

#### Python Code
```python
    class Solution:
        # @param {string} s a string
        # @param {set} dict a set of n substrings
        # @return {int} the minimum length
        def minLength(self, s, dict):
            if not s:
                return 0
            queue = collections.deque()
            visited = set()
            minLen = len(s)
            
            queue.appendleft(s)
            visited.add(s)
            while queue:
                curr = queue.pop()
                for pattern in dict:
                    pos = -1
                    while 1:
                        pos = curr.find(pattern, pos + 1)
                        if pos == -1:
                            break
                        out = curr[: pos] + curr[pos + len(pattern) :]
                        if out in visited:
                            continue
                        visited.add(out)
                        queue.appendleft(out)
                minLen = min(minLen, len(curr))
            
            return minLen
```

#### Thoughts：
起初拿到题目只想到需要考虑不同顺序的删除，但是没想出实现的方法。现在想来，应该是对 s.indexOf(pattern, start) 函数不够熟悉。

最终的思路可以这么理解：对于input s，BFS 的第一层考虑了所有只删除一个 pattern 的情况，第二层再深入一层，考虑所有删除 两个 pattern 的情况，以此类推。
> 例如，对于 s=‘ababcc’，pattern={‘a’，‘b’}；  
第一层为删除一个 ‘a’ 或者删除一个 ‘b’ 的结果，即 ‘babcc’，‘abbcc’，‘aabcc’，’abacc‘；







