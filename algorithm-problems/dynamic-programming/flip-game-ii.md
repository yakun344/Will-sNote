# Flip Game II

_update Sep 10, 2017 17:00_

[LeetCode](https://leetcode.com/problems/flip-game-ii/description/)

You are playing the following Flip Game with your friend: Given a string that contains only these two characters: + and -, you and your friend take turns to flip two consecutive `"++"` into `"--"`. The game ends when a person can no longer make a move and therefore the other person will be the winner.

Write a function to determine if the starting player can guarantee a win.

For example, given `s = "++++"`, return true. The starting player can guarantee a win by flipping the middle `"++"` to become `"+--+"`.

**Follow up:** Derive your algorithm's runtime complexity.

## Basic Idea:

这道题目所描述的是一个典型有最优选择策略的零和游戏，其特点是当 canWin==True 的时候，我们必胜，当 canWin==False 的时候，不是不知道我们能否获胜，而是当对方也采取最优策略的时候，我们必败。

由上面的分析，我们就可以得到一种思路：

* 对一个 input string s，我们可以有 k 种 flip 一步的方案；
* 对每一个方案，会有一个 next string，这个next就是丢给下家的；
* 我们只要判断 `if (! canWin(next))`, 就说明下家一定赢不了，也说明我们必胜；
* 此时我们就选择这条路，因为如此必胜，所以可以直接返回 True;

接下来，我们注意到其中可能会有重复，而且是否 canWin 是和每个input string s 一一对应的，我们可以由此用 Memoized Dynamic Programming 的思路进行优化，维持一个 `Map<String, Boolean>`, 为每个遇见的 s 记录其是否 canWin；

**Time Complexity：**  
我们实际上在穷举所有可能的情况，对于 input like ： “+++++++++++”，我们有大约 P\(n, n\) 种不同的操作方法，是 O\(n!\) 的，但是经过优化之后，显然已经有了很大的进步, 最终应该是 O\(2^n\)。

## Java Code:

```java
    class Solution {
        public boolean canWin(String s) {
            return canWin(s, new HashMap<String, Boolean>());
        }
        private boolean canWin(String s, Map<String, Boolean> winTable) {
            if (winTable.containsKey(s)) return winTable.get(s);
            // 进行这一步的操作，考虑每种可能
            for (int i = 0; i < s.length() - 1; ++i) {
                if (s.charAt(i) == '+' && s.charAt(i + 1) == '+') {
                    String next = s.substring(0, i) + "--" + s.substring(i + 2, s.length());
                    // ! canWin 表示必败，当下家必败的时候，说明我们必胜
                    if (! canWin(next, winTable)) {
                        winTable.put(s, true);
                        return true;
                    }
                }
            }
            winTable.put(s, false);
            return false;
        }
    }
```

