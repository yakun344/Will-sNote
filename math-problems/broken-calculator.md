# Broken Calculator
_update Feb 11 2019, 21:53_

---
[LeetCode](https://leetcode.com/problems/broken-calculator/)

On a broken calculator that has a number showing on its display, we can perform two operations:

Double: Multiply the number on the display by 2, or;
Decrement: Subtract 1 from the number on the display.
Initially, the calculator is displaying the number X.

Return the minimum number of operations needed to display the number Y.

**Example 1:**

    Input: X = 2, Y = 3
    Output: 2
    Explanation: Use double operation and then decrement operation {2 -> 4 -> 3}.

**Example 2:**

    Input: X = 5, Y = 8
    Output: 2
    Explanation: Use decrement and then double {5 -> 4 -> 8}.

**Example 3:**

    Input: X = 3, Y = 10
    Output: 3
    Explanation:  Use double, decrement and double {3 -> 6 -> 5 -> 10}.

**Example 4:**

    Input: X = 1024, Y = 1
    Output: 1023
    Explanation: Use decrement operations 1023 times.
 

**Note:**

1. `1 <= X <= 10^9`
2. `1 <= Y <= 10^9`

<br/>

### Basic Idea:
如果X比Y大，我们就每次让`X--`，这种情况下直接返回 `X-Y`;

如果X比Y小，如果从X转换为Y的方向考虑，每次有两种选择，及 `X=X*2` 或者 `X=X-1`，所以我们可能需要做一个BFS，但是这样的话时间空间复杂度将是指数级的。

但如果我们从Y出发，考虑它每次是如何被X推出，则只有一条路可以选，就会简单许多：

* 当Y为奇数时，一定是由 `X * 2 - 1` 形成；
* 当Y为偶数时，一定是由 `X * 2` 形成；
* 当Y小于X时，一定是 `while (X>Y) X--;` 形成；

于是我们就可以用这种方法反推来计算操作次数。这种思路非常巧妙，以后也要注意可以**从反方向考虑问题**。

#### Java Code:
```java
    class Solution {
        public int brokenCalc(int X, int Y) {
            if (X > Y) return X - Y;
            int ret = 0;
            while (Y > X) {
                if (Y % 2 == 0) {
                    Y /= 2;
                    ret++;
                } else {
                    Y = (Y + 1) / 2;
                    ret += 2;
                }
            }
            return ret + X - Y;
        }
    }
```
