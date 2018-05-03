## Two Sum - Unique pairs
_update Aug 19,2017  17:25_

---
[LintCode](http://www.lintcode.com/en/problem/two-sum-unique-pairs/)  

Given an array of integers, find how many unique pairs in the array such that their sum is equal to a specific target number. Please return the number of pairs.

**Example**

    Given nums = [1,1,2,45,46,46], target = 47
    return 2
    
    1 + 46 = 47
    2 + 45 = 47
    
#### Basic Idea:
使用 2 pointers algorithm：

1.  先排序；
2.  left 和 right 两个指针从两端向中间逼近，沿途记录信息；
3.  注意去重的手法；

#### Java Code:
```java
    public class Solution {
        /**
         * @param nums an array of integer
         * @param target an integer
         * @return an integer
         */
        public int twoSum6(int[] nums, int target) {
            Arrays.sort(nums);
            int left = 0, right = nums.length - 1;
            int ret = 0;
            while (left < right) {
                int sum = nums[left] + nums[right];
                if (sum == target) {
                    ret++;
                    left++;
                    right--;
            // ！！！！！！！！ 去重 ！！！！！！！！
                    while (left < right && nums[left] == nums[left - 1]) left++;
                    while (left < right && nums[right] == nums[right + 1]) right--;
                }
                else if (sum < target) left++;
                else right--;
            }
            return ret;
        }
    }
```

#### C++ Code（这段code对应 LeetCode 版本）:
[leetcode](https://leetcode.com/problems/two-sum/description/)

Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

**Example:**

`Given nums = [2, 7, 11, 15], target = 9,`

`Because nums[0] + nums[1] = 2 + 7 = 9,`
`return [0, 1].`


```cpp
    // two pointers
    class Solution {
    public:
      vector<int> twoSum(vector<int>& nums, int target) {
        vector< vector<int> > arr;
        for (int i = 0; i < nums.size(); ++i) {
          arr.emplace_back(vector<int>{nums[i], i});
        }
        // using lambda to provide a comparator
        sort(arr.begin(), arr.end(), [](const vector<int>& v1, const vector<int>& v2) {return v1[0] < v2[0]; });
        
        // two pointers
        int i = 0, j = arr.size() - 1;
        while (i < j && arr[i][0] + arr[j][0] != target) {
          if (arr[i][0] + arr[j][0] < target) {
            i++;
          } else {
            j--;
          }
        }
        return vector<int>{arr[i][1], arr[j][1]};
      }
    };
```
    
    