Best Team With No Conflicts

----
_update Oct 18th, 2020_

You are the manager of a basketball team. For the upcoming tournament, you want to choose the team with the highest overall score. The score of the team is the sum of scores of all the players in the team.

However, the basketball team is not allowed to have conflicts. A conflict exists if a younger player has a strictly higher score than an older player. A conflict does not occur between players of the same age.

Given two lists, scores and ages, where each `scores[i]` and `ages[i]` represents the score and age of the ith player, respectively, return the highest overall score of all possible basketball teams.

**Example 1:**
```c
Input: scores = [1,3,5,10,15], ages = [1,2,3,4,5]
Output: 34
Explanation: You can choose all the players.
```

**Example 2:**
```c
Input: scores = [4,5,6,5], ages = [2,1,2,1]
Output: 16
Explanation: It is best to choose the last 3 players. Notice that you are allowed to choose multiple people of the same age.
```

**Example 3:**
```c
Input: scores = [1,2,3,5], ages = [8,9,10,1]
Output: 6
Explanation: It is best to choose the first 3 players.
```


**Constraints:**
```c
1. 1 <= scores.length, ages.length <= 1000
2. scores.length == ages.length
3. 1 <= scores[i] <= 106
4. 1 <= ages[i] <= 1000
```
<br>

## Basic Idea
比较容易想到的第一步是按照年龄排序，进而可以想到如果年龄相同需要按照分数排序。考虑到需要保证选择的人之中不能出现分数更高但是更年轻的选手，而选择或者不选择某个选手会对之后的选择产生影响，整个思路倾向于DP，考虑和LIS(Longest Increasing Subsequence)类似的O(N^2)解法。我们定义如下DP规则：
```c
    先保证输入数据按照age，score主次键排序

    定义：dp[i]: 到i为止，并且选择i能获得的最大分数

    则有如下递推规则：
    dp[i + 1] := scores[i+1]
    dp[i + 1] = max{scores[i+1] + dp[j] for j from 0 to i if (ages[j] == ages[i+1] or scores[j] <= scores[i+1])}

    最终的解就是 max{dp[:]}
```

## Java Code:
```java
class Solution {
    public int bestTeamScore(int[] scores, int[] ages) {
        int[][] arr = new int[scores.length][2];
        for (int i = 0; i < scores.length; ++i) {
            arr[i][0] = ages[i];
            arr[i][1] = scores[i];
        }
        // 按age,score主次排序
        Arrays.sort(arr, (a, b) -> {
            if (a[0] == b[0]) return Integer.compare(a[1], b[1]);
            return Integer.compare(a[0], b[0]);
        });

        // dp[i] 表示选择到第i个人位置，选择第i人的情况下能获得的最大分数，
        // 因为已经按照age，score排序，我们一定先见到更年轻分数更低的选手，
        // 所以在dp的过程中，我们可以保证如果要选择i，那么我们只需要考虑i之前
        // 所有年龄等于它或者分数小于等于它的情况
        int dp[] = new int[scores.length];
        for (int i = 0; i < scores.length; ++i) {
            int score = arr[i][1];
            int age = arr[i][0];
            dp[i] = score;
            for (int j = i - 1; j >= 0; --j) {
                if (arr[j][0] < age && arr[j][1] > score) {
                    continue;
                }
                dp[i] = Math.max(dp[i], dp[j] + score);
            }
        }

        int ret = 0;        
        for (int i = 0; i < dp.length; ++i) {
            ret = Math.max(ret, dp[i]);
        }
        return ret;
    }
}
```
