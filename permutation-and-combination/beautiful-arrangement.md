## Beautiful Arrangement
_update Jul 5, 2017 12:36_

---

[leetcode](https://leetcode.com/problems/beautiful-arrangement/#/description)

Suppose you have N integers from 1 to N. We define a beautiful arrangement as an array that is constructed by these N numbers successfully if one of the following is true for the ith position (1 ≤ i ≤ N) in this array:

*  The number at the ith position is divisible by i.
*  i is divisible by the number at the ith position.

Now given N, how many beautiful arrangements can you construct?

    Example 1:
    Input: 2
    Output: 2
    Explanation: 
    
    The first beautiful arrangement is [1, 2]:
    
    Number at the 1st position (i=1) is 1, and 1 is divisible by i (i=1).
    
    Number at the 2nd position (i=2) is 2, and 2 is divisible by i (i=2).
    
    The second beautiful arrangement is [2, 1]:
    
    Number at the 1st position (i=1) is 2, and 2 is divisible by i (i=1).
    
    Number at the 2nd position (i=2) is 1, and i (i=2) is divisible by 1.
**Note:**
N is a positive integer and will not exceed 15.

#### Basic Idea:
这道题目实际是要求出满足某种条件的所有的 [1-N] 这 N 个数组的排列组合的个数，那么思路首先肯定是生成所有的排列组合，然后 count 满足条件的个数。因为我们可以单独判断每个组合是否满足条件，我们可以一边生成，一边判定。

接下来，具体到这道题，我们想到可以对每一位数，考虑剩下候选数中满足条件的数，然后对应这些选择分别继续考虑下一位数，这样就得到了一个可行的 recursion 的思路。同时具体到这道题，我们注意到前面的数，例如1，2，相比后面的数例如14，15更容易整除其他数，如果从前面开始检测会需要很深的递归之后才能发现行不通，所以我们倾向于 downward 的方法，即从 N 开始，逐位往左检测。

那么，我们应该如何跟踪当前path，记录下一层可用的候选数列表呢？In Java， we can maintain a `used = new int[N + 1]`, 从 1 开始每一位一一对应，当某个数被用掉，就将其设为1。Or better，we can just use an int(32) as a bit map to represent used array, since the largest N we will get is 15, so it is suffice to use an int(32). In python, we can simply remove the number from `remaining list`, and pass the new list to next level, since it's always easier to do something like `remaining[:i] + remaining[i+1:]` in python.

解决了细节问题之后，我们注意到在我们 recurse 的过程中会遇到重复情况，比如当 N == 4 时，对于前两位是 `1,2` 或者 `2,1`，我们的下一层 recursion 都是一样的 （第3位开始，候选为3，4）。于是，我们可以引入dp的思想, use memoization table。In Python, we can maintain a `cached = {}`, use `(pos, remaining)` as the key of the dictionary。In java, it is better to use bit map `int used` to represent used array, and we maintain a `HashMap<String, Integer> cached`，use `pos + "," + used` as the key.

**Analysis:**

 到这里，我们基本上就完成了整个设计并进行了优化。经过测试，在python中，from left to right with memoization 提速明显。在java中，由于调用hashmap本身需要较多时间，在系统给定 test cases 中没有比直接 from right to left 快，但是使用customized test case， N > 17 之后，dp 的方法就明显快过 brute force。

#### Python Code:
```python
    # brute force, from right to left
    class Solution(object):
        def countArrangement(self, N):
            """
            :type N: int
            :rtype: int
            """
            def dfs(N, pos, used):
                if pos == 0: return 1
                ret = 0
                for i in range(1, N + 1):
                    if not used[i] and (pos % i == 0 or i % pos == 0):
                        used[i] = 1
                        ret += dfs(N, pos - 1, used)
                        used[i] = 0
                return ret
            
            used = [0] * (N + 1)
            return dfs(N, N, used)
            
    # using memoization, right to left
    class Solution(object):
        def countArrangement(self, N):
            """
            :type N: int
            :rtype: int
            """
            cache = {}
            def dfs(pos, remaining, cache):
                if not remaining:
                    return 1
                key = pos, remaining
                if key in cache:
                    return cache[key]
                ret = 0
                for i in range(len(remaining)):
                    x = remaining[i]
                    if x % pos == 0 or pos % x == 0:
                        ret += dfs(pos - 1, remaining[:i] + remaining[i + 1:], cache)
                cache[key] = ret
                return ret
                
            remaining = tuple(i for i in range(1, N + 1))
            return dfs(N, remaining, cache)
```

#### Java Code:
```java
    // brute force with 'used' array, from right to left, faster 
    public class Solution {
        public int countArrangement(int N) {
            if (N == 0) return 0;
            int[] used = new int[N + 1];
            return dfs(N, N, used);
        }
        private int dfs(int N, int pos, int[] used) {
            if (pos == 1) return 1;
            int ret = 0;
            for (int i = 1; i < N + 1; ++i) {
                if (used[i] == 0 && (i % pos == 0 || pos % i == 0)) {
                    used[i] = 1;
                    ret += dfs(N, pos - 1, used);
                    used[i] = 0;
                }
            }
            return ret;
        }
    }
    
    // 如果要使用memoization，则需要使用bitmask来代替used数组，因为存储数组作为Key在java中不方便
    // from left to right, key 为string： “pos,used”
    public class Solution {
        Map<String, Integer> cached;
        public int countArrangement(int N) {
            cached = new HashMap<>();
            return dfs(N, N, 0); // used is initialized to be 0 (all numbers in [1-N] are available.
        }
        private int dfs(int N, int pos, int used) {
            if (pos == 0) return 1;
            String key = pos + "," + used;
            if (cached.containsKey(key)) return cached.get(key);
            int ret = 0;
            for (int i = 1; i <= N; ++i) {
                if (((1 << i) & used) == 0 && (pos % i == 0 || i % pos == 0)) {
                    ret += dfs(N, pos - 1, used | (1 << i));
                }
            }
            cached.put(key, ret);
            return ret;
        }
    }
```



