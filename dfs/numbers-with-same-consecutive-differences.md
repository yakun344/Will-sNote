# Numbers With Same Consecutive Differences
_update Jan 3 19:19, 2019_

---
[LeetCode](https://leetcode.com/problems/numbers-with-same-consecutive-differences/)

Return all non-negative integers of length N such that the absolute difference between every two consecutive digits is K.

Note that every number in the answer must not have leading zeros except for the number 0 itself. For example, 01 has one leading zero and is invalid, but 0 is valid.

You may return the answer in any order.

 

**Example 1:**

    Input: N = 3, K = 7
    Output: [181,292,707,818,929]
    Explanation: Note that 070 is not a valid number, because it has leading zeroes.

**Example 2:**

    Input: N = 2, K = 1
    Output: [10,12,21,23,32,34,43,45,54,56,65,67,76,78,87,89,98]
 

**Note:**

    1 <= N <= 9
    0 <= K <= 9

<br/>

### Basic Idea:
基本思路就是从左到右以此生成下一位数字，有DFS和BFS两种思路。这里着重说一下BFS的解法，一开始没有想到。我们可以从一个list开始，第一层操作后里面只有`1,2,3,4,5,6,7,8,9`，第二次操作的时候对每一个我们考虑加减k的情况，如果可以，就将加上第二位的结果放入新的list。

#### DFS Java Code:
```java
class Solution {
    public int[] numsSameConsecDiff(int N, int K) {
        if (N == 1) return new int[]{0,1,2,3,4,5,6,7,8,9};
        List<Integer> res = new ArrayList<>();
        for (int start = 1; start <= 9; ++start) {
            dfs(start, N - 1, K, res);
        }
        return res.stream().mapToInt(i->i).toArray();
    }
    
    private void dfs(int curr, int N, int K, List<Integer> res) {
        if (N == 0) {
            res.add(curr);
            return;
        }
        int digit = curr % 10;
        if (digit + K < 10) dfs(curr * 10 + digit + K, N - 1, K, res);
        if (K != 0 && digit - K >= 0) dfs(curr * 10 + digit - K, N - 1, K, res);
    }
}
```

#### BFS Java Code:
```java
class Solution {
    public int[] numsSameConsecDiff(int N, int K) {
        if (N == 1) return new int[]{0,1,2,3,4,5,6,7,8,9};
        List<Integer> list = new ArrayList<>();
        for (int i = 1; i <= 9; ++i) list.add(i);
        for (int i = 1; i < N; ++i) {
            List<Integer> next = new ArrayList<>();
            for (int num : list) {
                int digit = num % 10;
                if (digit - K >= 0) next.add(num * 10 + digit - K);
                if (K != 0 && digit + K <= 9) next.add(num * 10 + digit + K);
            }
            list = next;
        }
        return list.stream().mapToInt(i->i).toArray();
    }
}
```
