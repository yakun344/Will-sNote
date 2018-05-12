# Longest Harmonious Subsequence
_update May 12,2018  1:04_

---
[LeetCode](https://leetcode.com/problems/longest-harmonious-subsequence/description/)


We define a harmonious array is an array where the difference between its maximum value and its minimum value is exactly 1.

Now, given an integer array, you need to find the length of its longest harmonious subsequence among all its possible subsequences.

**Example 1:**

    Input: [1,3,2,2,5,2,3,7]
    Output: 5
    Explanation: The longest harmonious subsequence is [3,2,2,2,3].
    
**Note:** The length of the input array will not exceed 20,000.

<br>

### Basic Idea:
利用HashMap统计每个数字出现的次数，然后遍历hashmap，计算每个数字的个数与其减一的个数的和，最大的和就是解。

* #### C++ Code:
```cpp
    class Solution {
    public:
        int findLHS(vector<int>& nums) {
            unordered_map<int, int> _map;
            for (int num : nums) {
                ++_map[num];
            }
            int res = 0;
            for (auto pair : _map) {
                if (_map.count(pair.first - 1)) {
                    res = max(res, pair.second + _map[pair.first - 1]);
                }
            }
            return res;
        }
    };
```

* #### Java Code:
```java
    class Solution {
        public int findLHS(int[] nums) {
            Map<Integer, Integer> map = new HashMap<>();
            for (int num : nums) map.put(num, map.getOrDefault(num, 0) + 1);
            int res = 0;
            for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
                if (map.containsKey(entry.getKey() - 1)) {
                    res = Math.max(res, entry.getValue() + map.get(entry.getKey() - 1));
                }
            }
            return res;
        }
    }
```