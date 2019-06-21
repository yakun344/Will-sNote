# Next Closest Time

_update Sep 27 2018, 16:47_

[LeetCode](https://leetcode.com/problems/next-closest-time/description/)

Given a time represented in the format "HH:MM", form the next closest time by reusing the current digits. There is no limit on how many times a digit can be reused.

You may assume the given input string is always valid. For example, "01:34", "12:09" are all valid. "1:34", "12:9" are all invalid.

**Example 1:**

```text
Input: "19:34"
Output: "19:39"
Explanation: The next closest time choosing from digits 1, 9, 3, 4, is 19:39,
  which occurs 5 minutes later.  It is not 19:33, because this occurs 23
  hours and 59 minutes later.
```

**Example 2:**

```text
Input: "23:59"
Output: "22:22"
Explanation: The next closest time choosing from digits 2, 3, 5, 9, is 22:22.
  It may be assumed that the returned time is next day's time since it is smaller
  than the input time numerically.
```

## Basic Idea:

其实就是将给定time的四位数字提取出来，然后dfs看它们能组成的所有时间中大于距离给定时间最近的时间。如果生成的时间比给定时间小，则加上一整天24小时。

虽然是一道dfs的题目，但是因为只有四个数字，我们可以使用四重for loop来做，使得code更加简洁。

### Java Code:

```java
class Solution {
    public String nextClosestTime(String time) {
        int[] nums = new int[4];
        int i = 0;
        for (char c : time.toCharArray()) {
            if ('0' <= c && '9' >= c) nums[i++] = c - '0';
        }
        int inputMin = 0;
        int nextMin = Integer.MAX_VALUE;
        String ret = "";
        inputMin = (nums[0] * 10 + nums[1]) * 60 + nums[2] * 10 + nums[3];

        if (nums[0] == nums[1] && nums[1] == nums[2] && nums[2] == nums[3]) return time;
        for (int h1 : nums) {
            for (int h2 : nums) {
                for (int m1 : nums) {
                    for (int m2 : nums) {
                        if (! isValid(h1, h2, m1, m2)) continue;
                        int currMin = (h1 * 10 + h2) * 60 + m1 * 10 + m2;
                        if (currMin < inputMin) currMin += 24 * 60;
                        if (currMin < nextMin) {
                            String currTime = h1 + "" + h2 + ":" + m1 + "" + m2;
                            if (currTime.equals(time)) continue;
                            nextMin = currMin;
                            ret = currTime;
                        }
                    }
                }
            }
        }
        return ret;
    }

    private boolean isValid(int h1, int h2, int m1, int m2) {
        if (h1 > 2) return false;
        else if (h1 == 2 && h2 >= 4) return false;
        else if (m1 >= 6) return false;
        else return true;
    }
}
```

