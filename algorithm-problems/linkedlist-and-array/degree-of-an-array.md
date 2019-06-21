# Degree of an Array

_update May 14,2018 19:59_

[LeetCode](https://leetcode.com/problems/degree-of-an-array/description/)

Given a non-empty array of non-negative integers nums, the degree of this array is defined as the maximum frequency of any one of its elements.

Your task is to find the smallest possible length of a \(contiguous\) subarray of nums, that has the same degree as nums.

**Example 1:**

```text
Input: [1, 2, 2, 3, 1]
Output: 2
Explanation: 
The input array has a degree of 2 because both elements 1 and 2 appear twice.
Of the subarrays that have the same degree:
[1, 2, 2, 3, 1], [1, 2, 2, 3], [2, 2, 3, 1], [1, 2, 2], [2, 2, 3], [2, 2]
The shortest length is 2. So return 2.
```

**Example 2:**

```text
Input: [1,2,2,3,1,4,2]
Output: 6
```

**Note:**

* `nums.length` will be between 1 and 50,000.
* `nums[i]` will be an integer between 0 and 49,999.

## Basic Idea:

先统计每个数字出现的次数，然后取出所有出现次数最多的数字，再从中找 first index 和 last index 距离最近的数字，返回 `lastIndex - firstIndex + 1`。

利用map分别统计每个数字出现次数以及首末index。

* **C++ Code:**

  ```cpp
    class Solution {
    public:
        int findShortestSubArray(vector<int>& nums) {
            unordered_map<int, int> _countMap; // 存储数量
            unordered_map<int, int> _firstMap; // 存储第一次出现的index
            unordered_map<int, int> _lastMap;  // 存储最后一次出现的index
            for (int i = 0; i < nums.size(); ++i) {
                if (! _firstMap.count(nums[i])) _firstMap[nums[i]] = i;
            }
            for (int i = nums.size() - 1; i >= 0; --i) {
                if (! _lastMap.count(nums[i])) _lastMap[nums[i]] = i;
            }

            for (int num : nums) ++_countMap[num];
            vector<int> maxNums;
            int maxCount = 0;
            for (auto pair : _countMap) {
                if (pair.second > maxCount) {
                    maxCount = pair.second;
                    maxNums.clear();
                    maxNums.push_back(pair.first);
                } else if (pair.second == maxCount) {
                    maxNums.push_back(pair.first);
                }
            }
            int minLen = nums.size();
            for (int num : maxNums) {
                minLen = min(minLen, _lastMap[num] - _firstMap[num] + 1);
            }
            return minLen;
        }
    };
  ```

