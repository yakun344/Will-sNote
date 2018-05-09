## Find All Numbers Disappeared in an Array
_update 2018-05-09 00:46:58_

---
[LeetCode](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/description/)

Given an array of integers where `1 ≤ a[i] ≤ n (n = size of array)`, some elements appear twice and others appear once.

Find all the elements of `[1, n]` inclusive that do not appear in this array.

Could you do it without extra space and in `O(n)` runtime? You may assume the returned list does not count as extra space.

**Example:**

    Input:
    [4,3,2,7,8,2,3,1]
    
    Output:
    [5,6]
    
### Basic Idea:
如果不考虑 O(1) 空间的要求是很简单的，但是如果要考虑的话，就没有那么容易了。

考虑到数字都来自 `1-n` 之间，都是正数，且可以和 input array 的 indice 一一对应，我们可以将 indice 标记为负数表示一个数字 （index+1） 已经出现过。全部标记完成后，仍然是正数的index对应的数字 (index+1) 就是缺失的数字了。

* #### C++ Code:
```cpp
    class Solution {
    public:
        vector<int> findDisappearedNumbers(vector<int>& nums) {
            vector<int> res;
            for (int i = 0; i < nums.size(); ++i) {
                int idx = abs(nums[i]) - 1;
                if (nums[idx] > 0) nums[idx] *= -1;
            }
            for (int i = 0; i < nums.size(); ++i) {
                if (nums[i] > 0) res.push_back(i + 1);
            }
            return res;
        }
    };
```