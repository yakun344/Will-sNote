# Maximum Number of Removable Characters
_update Jun 13, 2021_

![image](https://user-images.githubusercontent.com/24964756/121821673-3cd4e500-cc4f-11eb-8aa5-6ba2afc30e86.png)

## Basic Idea:
这道题目数据量比较大，如果是N^2的话肯定TLE，但是如果可以做到NlogN就可以。由此可以想到二分法。如果直接做的话，从左到右尝试每个长度的removable中的index，然后验证移除这些index之后P是否仍是S的subsequence，每次验证需要O(N), 总时间O(N^2)。利用二分法，我们可以对验证次数进行优化，最终做到O(NlogN)。

需要注意的是，每次验证时将 `removable[0 ~ i]` 的index在S中移除的操作可能会比较耗时，如果采用 HashSet 标记每个index，则这一步就需要 O(i) 。在推荐答案中我看到一个优化的做法，利用一个数组orders存储S中index在removable数组中的index，例如对于`s长度为4，removable=[3,1,0]`，则`orders=[2,1,4,0]`，即index 0 对应removable中index=2的位置，index 2因为不在removable中，让它等于 4 即大于s中的最大index 3 （也表示index2不会被删除）. 这样在之后验证的时候，如果我们要验证 removable 中的前 k 个 index 被删掉之后是否仍满足条件，我们只需要保证在比较 s 和 p 的时候跳过 s 中 index 在 removable 中 order 小于等于 k 的就可以了。

## Java Code after Optimization
```java
class Solution {
    public int maximumRemovals(String s, String p, int[] removable) {
        int[] removeOrder = new int[s.length()];
        Arrays.fill(removeOrder, s.length());
        for (int i = 0; i < removable.length; ++i) {
            removeOrder[removable[i]] = i; // 记录每个index在removable数组中的index
        }
        
        int left = 0, right = removable.length;
        while (left + 1 < right) {
            int mid = (left + right) / 2;
            if (check(s, p, removeOrder, mid)) {
                left = mid;
            } else {
                right = mid;
            }
        }
        if (check(s, p, removeOrder, right)) {
            return right;
        } else {
            return left;
        }
    }
    
    // 检查 removable 数组中前 len 个index 能否被remove
    private boolean check(String s, String p, int[] removeOrder, int len) {
        int j = 0;
        for (int i = 0; i < s.length() && j < p.length(); ++i) {
            if (removeOrder[i] < len) { 
                // 表示当前s中的index i位于removable中前len个位置，需要删掉，所以不和p比较直接跳过
                continue;
            }
            if (s.charAt(i) == p.charAt(j)) {
                j++;
            }
        }
        if (j == p.length()) {
            return true;
        }
        return false;
    }
}
```
