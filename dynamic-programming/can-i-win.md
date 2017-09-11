## Can I Win
_update Sep 10,2017  20:55_

---
[LeetCode](https://leetcode.com/problems/can-i-win/description/)

In the "100 game," two players take turns adding, to a running total, any integer from 1..10. The player who first causes the running total to reach or exceed 100 wins.

What if we change the game so that players cannot re-use integers?

For example, two players might take turns drawing from a common pool of numbers of 1..15 without replacement until they reach a total >= 100.

Given an integer maxChoosableInteger and another integer desiredTotal, determine if the first player to move can force a win, assuming both players play optimally.

You can always assume that maxChoosableInteger will not be larger than 20 and desiredTotal will not be larger than 300.

**Example**

    Input:
    maxChoosableInteger = 10
    desiredTotal = 11
    
    Output:
    false
    
**Explanation:**    
1. No matter which integer the first player choose, the first player will lose.
2. The first player can choose an integer from 1 up to 10.
3. If the first player choose 1, the second player can only choose integers from 2 up to 10.
4. The second player will win by choosing 10 and get a total = 11, which is >= desiredTotal.
5. Same with other integers chosen by the first player, the second player will always win.

#### Basic Idea:
又是一道典型零和游戏问题，解决这类问题要抓住一个原则，那就是：**！canWin()** 就代表输定了，所以在每一步检查所有可能决策的时候，只有发现一条路径使得 `canWin(next) == false`，我们就选择这个决策，并且可以直接返回，因为这样做对面已经没有取胜机会。

接下来，具体地，这道题提到最大Integer不会超过20，说明我们可以轻松地用一个32bit 的 int 来做 bitmap，充当used数组，这样等一会需要做 memoization 优化的时候，就可以轻松地获得 key。

对于这道题而言，游戏的每一步的状态其实包括两点：
1.  剩余可选的数字，也就是used数组或者used bitmap；
2.  remainSum，也就是剩余的和的大小；

仔细观察我们会发现，因为 target-remainSum 一定等于所有已经选择的数字的和，所以我们事实上在做memoization的时候，可以把 used bitmap 作为唯一的key，而不需要考虑 remainSum；

#### Code：
以下Code分别是从最初的 Brute Force 解法，到使用 `used + "," + remainSum` 做key 的memoization，再到最终的使用 used 直接做为 key 的memoization算法。

**Brute Force**
```java
    class Solution {
        public boolean canIWin(int maxChoosableInteger, int desiredTotal) {
            if (maxChoosableInteger >= desiredTotal) return true;
            boolean[] used = new boolean[maxChoosableInteger + 1];
            Arrays.fill(used, false);
            return canWin(maxChoosableInteger, used, desiredTotal);
        }
        // 检查在当前情况下开始的player能否保证必胜
        private boolean canWin(int max, boolean[] used, int remainSum) {
            if (remainSum <= 0) return false;
            for (int i = 1; i <= max; ++i) {
                if (used[i]) continue;
                used[i] = true;
                if (! canWin(max, used, remainSum - i)) {
                    used[i] = false;
                    return true;
                }
                used[i] = false;
            }
            return false;
        }
    }
```
**Memoization, key: used + "," + remainSum**
```java
    class Solution {
        public boolean canIWin(int maxChoosableInteger, int desiredTotal) {
            if (maxChoosableInteger >= desiredTotal) return true;
            if ((1 + maxChoosableInteger) * maxChoosableInteger / 2 < desiredTotal) return false;
            Map<String, Boolean> memo = new HashMap<>();
            int used = 0; // 000000000
            return canWin(maxChoosableInteger, used, desiredTotal, memo);
        }
        // 检查在当前情况下开始的player能否保证必胜
        private boolean canWin(int max, int used, int remainSum, Map<String, Boolean> memo) {
            if (remainSum <= 0) return false;
            String key = used + "," + remainSum;
            if (memo.containsKey(key)) return memo.get(key);
            for (int i = 1; i <= max; ++i) {
                if (((1<<i) & used) > 0) continue;
                used |= 1<<i;
                if (! canWin(max, used, remainSum - i, memo)) {
                    used ^= 1<<i;
                    memo.put(key, true);
                    return true;
                }
                used ^= 1<<i;
            }
            memo.put(key, false);
            return false;
        }
    }
```
**Memoization, key: used**
```java
    class Solution {
        public boolean canIWin(int maxChoosableInteger, int desiredTotal) {
            if (maxChoosableInteger >= desiredTotal) return true;
            if ((1 + maxChoosableInteger) * maxChoosableInteger / 2 < desiredTotal) return false;
            Map<Integer, Boolean> memo = new HashMap<>();
            int used = 0; // 000000000
            return canWin(maxChoosableInteger, used, desiredTotal, memo);
        }
        // 检查在当前情况下开始的player能否保证必胜
        private boolean canWin(int max, int used, int remainSum, Map<Integer, Boolean> memo) {
            if (remainSum <= 0) return false;
            if (memo.containsKey(used)) return memo.get(used);
            for (int i = 1; i <= max; ++i) {
                if (((1<<i) & used) > 0) continue;
                used |= 1<<i;
                if (! canWin(max, used, remainSum - i, memo)) {
                    used ^= 1<<i;
                    memo.put(used, true);
                    return true;
                }
                used ^= 1<<i;
            }
            memo.put(used, false);
            return false;
        }
    }
``` 

**Python Code:**
```python
    class Solution(object):
        def canIWin(self, maxChoosableInteger, desiredTotal):
            """
            :type maxChoosableInteger: int
            :type desiredTotal: int
            :rtype: bool
            """
            def canWin(used, remainSum):
                if remainSum <= 0: return False  #  游戏已经结束，躺输
                if used in memo:
                    return memo[used]
                for i in range(1, maxChoosableInteger + 1):
                    if (1<<i) & used:
                        continue
                    used |= 1<<i
                    if not canWin(used, remainSum - i):
                        used ^= 1<<i
                        memo[used] = True
                        return True
                    used ^= 1<<i
                # 退出循环，说明所有操作都无法取胜
                memo[used] = False
                return False
                
            if maxChoosableInteger > desiredTotal:
                return True
            if (maxChoosableInteger + 1) * maxChoosableInteger // 2 < desiredTotal:
                return False
            memo = {}
            return canWin(0, desiredTotal)
```












