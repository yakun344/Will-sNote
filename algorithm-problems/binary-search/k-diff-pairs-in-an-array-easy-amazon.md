# K-diff Pairs in an Array \(Easy Amazon\)

_update May 11,2018 15:17_

[LeetCode](https://leetcode.com/problems/k-diff-pairs-in-an-array/description/)

Given an array of integers and an integer k, you need to find the number of unique k-diff pairs in the array. Here a k-diff pair is defined as an integer pair \(i, j\), where i and j are both numbers in the array and their absolute difference is k.

**Example 1:**

```text
Input: [3, 1, 4, 1, 5], k = 2
Output: 2
Explanation: There are two 2-diff pairs in the array, (1, 3) and (3, 5).
Although we have two 1s in the input, we should only return the number of unique pairs.
```

**Example 2:**

```text
Input:[1, 2, 3, 4, 5], k = 1
Output: 4
Explanation: There are four 1-diff pairs in the array, (1, 2), (2, 3), (3, 4) and (4, 5).
```

**Example 3:**

```text
Input: [1, 3, 1, 5, 4], k = 0
Output: 1
Explanation: There is one 0-diff pair in the array, (1, 1).
```

**Note:**

* The pairs `(i, j)` and `(j, i)` count as the same pair.
* The length of the array won't exceed 10,000.
* All the integers in the given input belong to the range: `[-1e7, 1e7]`.

## Basic Idea:

求绝对值相差 k 的 unique pair 的个数。先对 input array 排序，然后对每个元素，二分查找其后方有无满足条件的元素与其构成pair。排序耗时 `O(nlogn)`，二分查找共耗时 `O(n*logn)`，总计 `O(nlogn)`;

* **C++ Code**

  ```cpp
    class Solution {
        bool helper(const vector<int>& nums, int left, int right, int target) {
            if (left > right) return false;
            while (left + 1 < right) {
                int mid = left + (right - left) / 2;
                if (nums[mid] <= target) {
                    left = mid;
                } else {
                    right = mid;
                }
            }
            if (nums[left] == target || nums[right] == target) {
                return true;
            }
            else return false;
        }
    public:
        int findPairs(vector<int>& nums, int k) {
            sort(nums.begin(), nums.end());
            int ret = 0;
            for (int i = 0; i < nums.size(); ++i) {
                if (i > 0 && nums[i - 1] == nums[i]) continue;
                if (helper(nums, i + 1, nums.size() - 1, nums[i] + k)) {
                    ++ret;
                }
            }
            return ret;
        }
    };
  ```

_update 2018-07-15 10:17:42_

### Update: Two pointers

更快的解法是使用双指针法。假定当前nums已经排序，只要维持 i，j 两个指针从左向右扫描，遇到 `nums[j] - nums[i] == k` 时就令 `ret++`；具体地：

* 对每个i，找到相应j的位置，那么对于下一个i，j的起始位置就是 `max(i+1, j)`，因为j之前的数字一定比 `nums[i-1]` 小，所以一定比 `nums[i]` 小。
* 每次检查完一个i之后，继续判断，跳过所有重复的i，即保证每个i都只被检查一次。

#### Java Code

```java
class Solution {
    public int findPairs(int[] nums, int k) {
        int i = 0, j = 1, ret = 0;
        if (nums == null || nums.length == 0) return ret;
        Arrays.sort(nums);
        while (j < nums.length && i < nums.length - 1) {
            j = j > i ? j : i + 1; // 避免 i 和 j 相等
            if (nums[j] - nums[i] == k) {
                ret++;
                i++;
                j++;
                // 去重
                while (i < nums.length && nums[i] == nums[i - 1]) i++;
                while (j < nums.length && nums[j] == nums[j - 1]) j++;
            } else if (nums[j] - nums[i] > k) {
                i++;
            } else {
                j++;
            }
        }
        return ret;
    }
}
```

_update 2018-07-29 21:03:17_

### Update, HashMap Solution

可以利用HashMap来解决，先排序，然后每次检查num-k是否在之前出现过，另外要跳过重复数字。还有就是需要注意，当k==0的时候，要特殊处理。

```java
class Solution {
    public int findPairs(int[] nums, int k) {
        Arrays.sort(nums);
        Map<Integer, Integer> map = new HashMap<>();
        int ret = 0;
        int last = Integer.MAX_VALUE;
        for (int num : nums) {
            if (num == last) {
                map.put(num, 2);
                continue;
            }
            last = num;
            if (map.containsKey(num - k)) ret ++;
            map.put(num, 1);
        }
        if (k == 0) {
            for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
                if (entry.getValue() > 1) ret++;
            }
        }
        return ret;
    }
}
```

