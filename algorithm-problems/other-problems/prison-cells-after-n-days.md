# Prison Cells After N Days

_update Dec 16,2018_

[LeetCode](https://leetcode.com/problems/prison-cells-after-n-days/)

There are 8 prison cells in a row, and each cell is either occupied or vacant.

Each day, whether the cell is occupied or vacant changes according to the following rules:

If a cell has two adjacent neighbors that are both occupied or both vacant, then the cell becomes occupied. Otherwise, it becomes vacant. \(Note that because the prison is a row, the first and the last cells in the row can't have two adjacent neighbors.\)

We describe the current state of the prison in the following way: `cells[i] == 1` if the i-th cell is occupied, else `cells[i] == 0`.

Given the initial state of the prison, return the state of the prison after N days \(and N such changes described above.\)

**Example 1:**

```text
Input: cells = [0,1,0,1,1,0,0,1], N = 7
Output: [0,0,1,1,0,0,0,0]
Explanation: 
The following table summarizes the state of the prison on each day:
Day 0: [0, 1, 0, 1, 1, 0, 0, 1]
Day 1: [0, 1, 1, 0, 0, 0, 0, 0]
Day 2: [0, 0, 0, 0, 1, 1, 1, 0]
Day 3: [0, 1, 1, 0, 0, 1, 0, 0]
Day 4: [0, 0, 0, 0, 0, 1, 0, 0]
Day 5: [0, 1, 1, 1, 0, 1, 0, 0]
Day 6: [0, 0, 1, 0, 1, 1, 0, 0]
Day 7: [0, 0, 1, 1, 0, 0, 0, 0]
```

**Example 2:**

```text
Input: cells = [1,0,0,1,0,0,1,0], N = 1000000000
Output: [0,0,1,1,1,1,1,0]
```

**Note:**

1. `cells.length == 8`
2. `cells[i]` is in `{0, 1}`
3. `1 <= N <= 10^9`

## Basic Idea:

首先我们注意到长度为8的boolean array，第一天之后左右两端都是0，所以共有6个位置可以变化，总状态数为 `2^6=64`, 所以一定会出现循环。所以最终的思路就是找出循环，将N变小。

需要注意的主要有两点：

1. 每次将 N - 1， 这样 `N %= period` 的时候就不会出现诸如 `i > N` 的情况。
2. 特别注意边界条件，加一减一不是很好把握。

### Java Code:

```java
class Solution {
    public int[] prisonAfterNDays(int[] cells, int N) {
        int[] first = null;
        int[] prev = cells;
        int count = 0;
        int period = 0;
        while (N > 0) {
            if (count == 1) first = prev;
            int[] next = getNextDay(prev);
            N--;
            if (Arrays.equals(first, next)) {
                period = count;
                N %= period;
            }
            prev = next;
            count++;
        }
        return prev;
    }

    private int[] getNextDay(int[] nums) {
        int[] ret = new int[nums.length];
        for (int i = 1; i < nums.length - 1; ++i) {
            if (nums[i - 1] == nums[i + 1]) ret[i] = 1;
        }
        return ret;
    }
}
```

