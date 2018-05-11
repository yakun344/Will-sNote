# Relative Ranks
_update May 11,2018  0:08_

---
[LeetCode](https://leetcode.com/problems/relative-ranks/description/)

Given scores of N athletes, find their relative ranks and the people with the top three highest scores, who will be awarded medals: "Gold Medal", "Silver Medal" and "Bronze Medal".

**Example 1:**

    Input: [5, 4, 3, 2, 1]
    Output: ["Gold Medal", "Silver Medal", "Bronze Medal", "4", "5"]
    
**Explanation:** The first three athletes got the top three highest scores, so they got "Gold Medal", "Silver Medal" and "Bronze Medal".   
For the left two athletes, you just need to output their relative ranks according to their scores.

**Note:**
* N is a positive integer and won't exceed 10,000.
* All the scores of athletes are guaranteed to be unique.

<br>

### Basic Idea:
新建一个 `n*2` 的 array，存储分数和在 input array 中的 index，然后以分数为key对该array进行排序。之后扫描排序好的 2D array，就可以按照每个分数所记录的在 input array 中的 index将其排名放到相应位置。

* #### C++ Code:
注意 `std::sort()` 函数的使用;
```cpp
class Solution {
public:
    vector<string> findRelativeRanks(vector<int>& nums) {
        vector<vector<int>> arr;
        for (int i = 0; i < nums.size(); ++i) {
            arr.push_back({nums[i], i});
        }
        auto comp = [](vector<int>& a, vector<int>& b)->bool{ return a[0] > b[0]; };
        sort(arr.begin(), arr.end(), comp);
        
        vector<string> res(nums.size());
        for (int i = 0; i < arr.size(); ++i) {
            string s;
            if (i == 0) s = "Gold Medal";
            else if (i == 1) s = "Silver Medal";
            else if (i == 2) s = "Bronze Medal";
            else s = to_string(i + 1);
            res[arr[i][1]] = s;
        }
        return res;
    }
};
```










