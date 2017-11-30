## Increasing Triplet Subsequence
_update Aug 14,2017 21:21_

---
[LeetCode](https://leetcode.com/problems/increasing-triplet-subsequence/description/)

Given an unsorted array return whether an increasing subsequence of length 3 exists or not in the array.

Formally the function should:
*  Return true if there exists i, j, k 
*  such that arr[i] < arr[j] < arr[k] given 0 ≤ i < j < k ≤ n-1 else return false.

Your algorithm should run in O(n) time complexity and O(1) space complexity.

**Examples:**
```python
    Given [1, 2, 3, 4, 5],
    return true.
    
    Given [5, 4, 3, 2, 1],
    return false.
```

#### Basic Idea:
要解决这个问题，我们只需要在遍历时想办法跟踪当前位置之前**最小**的和**第二小**的数，准确讲，其实只是需要跟踪**当前位置之前第二小的数**，那么只要发现比当前第二小大的数，就说明三元递增序列了。

#### Java Code:
```java
    public class Solution {
        public boolean increasingTriplet(int[] nums) {
            int first = Integer.MAX_VALUE;
            int second = Integer.MAX_VALUE;
            for (int n : nums) {
                // 发现更小的数，更新当前最小，以便一会更新当前第二小
                if (n <= first) first = n;
                // 这是当前第二小的数，即使first已经被更新过，但不影响第二小，因为
                // 当前第二小之前一定有比它更小的一个数
                else if (n <= second) second = n;
                // 说明当前数比第二小的大，则说明有三元递增序列
                else return true;
            }
            return false;
        }
    }
```
---
_update Nov 30, 2017_
#### 更新
时隔数月重新考虑这道题目的时候，思路是对的，但却没有看出只需要跟踪**当前第二小**这一点。在实现的时候，`<=` 的判断条件很重要；

**更新一个Python的solution：**
```python





