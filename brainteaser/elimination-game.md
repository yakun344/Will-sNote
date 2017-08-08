## Elimination Game
_Update Aug 8,2017  15:50_

---
[LeetCode](https://leetcode.com/problems/elimination-game/description/)

There is a list of sorted integers from 1 to n. Starting from left to right, remove the first number and every other number afterward until you reach the end of the list.

Repeat the previous step again, but this time from right to left, remove the right most number and every other number from the remaining numbers.

We keep repeating the steps again, alternating left to right and right to left, until a single number remains.

Find the last number that remains starting with a list of length n.

**Example:**

    Input:
    n = 9,
    1 2 3 4 5 6 7 8 9
    2 4 6 8
    2 6
    6
    
    Output:
    6
    
#### Basic Idea:
[这里](https://discuss.leetcode.com/topic/59293/java-easiest-solution-o-logn-with-explanation) 是一个很好的解读。

基本思路就是跟踪当前序列的最左边元素，称为head。当我们从左边开始的时候，这个head一定会右移，当我们从右开始的时候，只有当前序列长度为奇数的时候，head会右移。右移的步数初始为1，每次乘二。

#### Java Code:
```java
    public class Solution {
        public int lastRemaining(int n) {
            int step = 1;
            int head = 1;
            boolean left = true;
            while (n > 1) {
                if (left || n % 2 == 1) {
                    // 需要把head前移
                    head += step;
                }
                left = ! left;
                n /= 2;
                step *= 2;
            }
            return head;
        }
    }
```