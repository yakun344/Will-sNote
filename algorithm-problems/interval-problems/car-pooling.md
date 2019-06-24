# Car Pooling
_update Jun 23, 2019_

---
[LeetCode](https://leetcode.com/problems/car-pooling/)

You are driving a vehicle that has capacity empty seats initially available for passengers.  The vehicle only drives east (ie. it cannot turn around and drive west.)

Given a list of trips, `trip[i] = [num_passengers, start_location, end_location]` contains information about the i-th trip: the number of passengers that must be picked up, and the locations to pick them up and drop them off.  The locations are given as the number of kilometers due east from your vehicle's initial location.

Return true if and only if it is possible to pick up and drop off all passengers for all the given trips. 


**Example 1:**

    Input: trips = [[2,1,5],[3,3,7]], capacity = 4
    Output: false

**Example 2:**

    Input: trips = [[2,1,5],[3,3,7]], capacity = 5
    Output: true

**Example 3:**

    Input: trips = [[2,1,5],[3,5,7]], capacity = 3
    Output: true

**Example 4:**

    Input: trips = [[3,2,7],[3,7,9],[8,3,9]], capacity = 11
    Output: true
 

**Constraints:**

1. `trips.length <= 1000`
2. `trips[i].length == 3`
3. `1 <= trips[i][0] <= 100`
4. `0 <= trips[i][1] < trips[i][2] <= 1000`
5. `1 <= capacity <= 100000`

### Basic Idea
这道题目就是输入一些 `[人数，起点，终点]` 的数据，以及一个 capacity，问能一次从左到右能否接送所有的人。

利用扫描线的原理，将输入数据转换为`[位置坐标，人数delta]`，然后按照坐标从左到右以及人数先下后上的顺序排序，之后扫描一次，检查load是否会超过capacity。

### C++ Code
```cpp
class Solution {
public:
    bool carPooling(vector<vector<int>>& trips, int capacity) {
        vector<pair<int, int>> events;
        for (const auto& trip : trips) {
            events.push_back({trip[1], trip[0]});
            events.push_back({trip[2], -trip[0]});
        }
        sort(events.begin(), events.end(), [](pair<int, int> a, pair<int, int> b){
            if (a.first != b.first) return a.first < b.first;
            else return a.second < b.second;
        });
        int load = 0;
        for (const auto& event : events) {
            load += event.second;
            if (load > capacity) return false;
        }
        return true;
    }
};
```
