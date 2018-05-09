## Heaters
_update May 9,2018  16:28_

---
[LeetCode](https://leetcode.com/problems/heaters/description/)

Winter is coming! Your first job during the contest is to design a standard heater with fixed warm radius to warm all the houses.

Now, you are given positions of houses and heaters on a horizontal line, find out minimum radius of heaters so that all houses could be covered by those heaters.

So, your input will be the positions of houses and heaters seperately, and your expected output will be the minimum radius standard of heaters.

**Note:**
Numbers of houses and heaters you are given are non-negative and will not exceed `25000`.  
Positions of houses and heaters you are given are non-negative and will not exceed `10^9`.  

As long as a house is in the heaters' warm radius range, it can be warmed.
All the heaters follow your radius standard and the warm radius will the same.

**Example 1:**

    Input: [1,2,3],[2]
    Output: 1

* **Explanation:** The only heater was placed in the position 2, and if we use the radius 1 standard, then all the houses can be warmed.  

**Example 2:**

    Input: [1,2,3,4],[1,4]
    Output: 1
    
* **Explanation:** The two heater was placed in the position 1 and 4. We need to use radius 1 standard, then all the houses can be warmed.

<br>

### Basic Idea:
* 最简单的解法是使用二分法，对于每个house，在heater数组中找距离其最近的heater，耗时 `O(#house * log(#heater))`。 当然在这之前需要对两个数组排序，排序耗时 `O(#house * log(#house) + #heater * log(#heater))`  
* 而最优解是 `O(#house + #heater)` 线性时间的（入过不考虑排序消耗时间）。排序，维持两个指针分别从0开始向右扫描，对于每个house，计算距离其最近heater的方法是：取其左边heater和右边heater到它距离的最小值。维持一个全局变量，记录最大距离即可。

* #### C++ Code:
```cpp
class Solution {
public:
    int findRadius(vector<int>& houses, vector<int>& heaters) {
        int i = 0, j = 0;
        int ret = 0;
        sort(houses.begin(), houses.end());
        sort(heaters.begin(), heaters.end());
        while (i < houses.size()) {
            if (j == heaters.size() || heaters[j] > houses[i]) {
                if (j == 0) {
                    ret = max(ret, heaters[j] - houses[i]);
                } else if (j == heaters.size()) {
                    ret = max(ret, abs(heaters[j - 1] - houses[i]));
                } else {
                    ret = max(ret, min(abs(heaters[j - 1] - houses[i]), heaters[j] - houses[i]));
                }
                ++i;
            } else {
                ++j;
            }
        }
        return ret;
    }
};
```