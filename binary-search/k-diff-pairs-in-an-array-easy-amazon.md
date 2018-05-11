## K-diff Pairs in an Array
_update May 11,2018 15:17_

---
[LeetCode](https://leetcode.com/problems/k-diff-pairs-in-an-array/description/)

Given an array of integers and an integer k, you need to find the number of unique k-diff pairs in the array. Here a k-diff pair is defined as an integer pair (i, j), where i and j are both numbers in the array and their absolute difference is k.

**Example 1:**
    
    Input: [3, 1, 4, 1, 5], k = 2
    Output: 2
    Explanation: There are two 2-diff pairs in the array, (1, 3) and (3, 5).
    Although we have two 1s in the input, we should only return the number of unique pairs.

**Example 2:**

    Input:[1, 2, 3, 4, 5], k = 1
    Output: 4
    Explanation: There are four 1-diff pairs in the array, (1, 2), (2, 3), (3, 4) and (4, 5).

**Example 3:**

    Input: [1, 3, 1, 5, 4], k = 0
    Output: 1
    Explanation: There is one 0-diff pair in the array, (1, 1).

**Note:**

* The pairs `(i, j)` and `(j, i)` count as the same pair.
* The length of the array won't exceed 10,000.
* All the integers in the given input belong to the range: `[-1e7, 1e7]`.

<br>

### Basic Idea:
求绝对值相差 k 的 unique pair 的个数。先对 input array 排序，然后对每个元素，二分查找其后方有无满足条件的元素与其构成pair。排序耗时 `O(nlogn)`，二分查找共耗时 `O(n*logn)`，总计 `O(nlogn)`;

* #### C++ Code
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