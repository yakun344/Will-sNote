# Shortest Distance to a Character
_update Oct 25 2018, 9:48_

---
[LeetCode](https://leetcode.com/problems/shortest-distance-to-a-character/)

Given a string S and a character C, return an array of integers representing the shortest distance from the character C in the string.

**Example 1:**

    Input: S = "loveleetcode", C = 'e'
    Output: [3, 2, 1, 0, 1, 0, 0, 1, 2, 2, 1, 0]
 

**Note:**

1. S string length is in [1, 10000].
2. C is a single character, and guaranteed to be in string S.
3. All letters in S and C are lowercase.

<br/>

### Basic Idea:
[发在leetcode里面的解答](https://leetcode.com/problems/shortest-distance-to-a-character/discuss/185702/One-Pass-O(N)-Java-Solution-with-explanation)  
基本思想就是只进行一次扫描，动态更新一个int变量 `prev`，表示前一个target的index。用一个stack来记录从`prev`到下一个target之间的char的index。遇到下一个target的时候，poll出来每个char的index，计算最近距离，然后`prev = current target index`；

这个解法的时间复杂度为O(n), 空间复杂度O(n);

#### Java
```java
class Solution {
    public int[] shortestToChar(String S, char C) {
        int prev = -10000;
        Deque<Integer> stack = new ArrayDeque<>();
        int[] ret = new int[S.length()];
        for (int i = 0; i < S.length(); ++i) {
            if (S.charAt(i) == C) {
                while (! stack.isEmpty()) {
                    int peek = stack.pollLast();
                    ret[peek] = Math.min(peek - prev, i - peek);
                }
                prev = i;
            } else {
                stack.offerLast(i);
            }
        }
        while (! stack.isEmpty()) {
            int peek = stack.pollLast();
            ret[peek] = peek - prev;
        }
        return ret;
    }
}
```
