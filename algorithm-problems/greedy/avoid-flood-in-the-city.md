# Avoid Flood in The City

_update Jun 23, 2020_

[Leetcode](https://leetcode.com/problems/avoid-flood-in-the-city/)

Your country has an infinite number of lakes. Initially, all the lakes are empty, but when it rains over the nth lake, the nth lake becomes full of water. If it rains over a lake which is full of water, there will be a flood. Your goal is to avoid the flood in any lake.

Given an integer array rains where:

`rains[i] > 0` means there will be rains over the `rains[i]` lake.

`rains[i] == 0` means there are no rains this day and you can choose one lake this day and dry it.

Return an array ans where:

1. `ans.length == rains.length`
2. `ans[i] == -1 if rains[i] > 0`.
3. `ans[i] is the lake you choose to dry in the ith day if rains[i] == 0`.
4. If there are multiple valid answers return any of them. If it is impossible to avoid flood return an empty array.

**Notice** that if you chose to dry a full lake, it becomes empty, but if you chose to dry an empty lake, nothing changes. \(see example 4\)

**Example 1:**

```text
Input: rains = [1,2,3,4]
Output: [-1,-1,-1,-1]
Explanation: After the first day full lakes are [1]
After the second day full lakes are [1,2]
After the third day full lakes are [1,2,3]
After the fourth day full lakes are [1,2,3,4]
There's no day to dry any lake and there is no flood in any lake.
```

**Example 2:**

```text
Input: rains = [1,2,0,0,2,1]
Output: [-1,-1,2,1,-1,-1]
Explanation: After the first day full lakes are [1]
After the second day full lakes are [1,2]
After the third day, we dry lake 2. Full lakes are [1]
After the fourth day, we dry lake 1. There is no full lakes.
After the fifth day, full lakes are [2].
After the sixth day, full lakes are [1,2].
It is easy that this scenario is flood-free. [-1,-1,1,2,-1,-1] is another acceptable scenario.
```

**Example 3:**

```text
Input: rains = [1,2,0,1,2]
Output: []
Explanation: After the second day, full lakes are  [1,2]. We have to dry one lake in the third day.
After that, it will rain over lakes [1,2]. It's easy to prove that no matter which lake you choose to dry in the 3rd day, the other one will flood.
```

**Example 4:**

```text
Input: rains = [69,0,0,0,69]
Output: [-1,69,1,1,-1]
Explanation: Any solution on one of the forms [-1,69,x,y,-1], [-1,x,69,y,-1] or [-1,x,y,69,-1] is acceptable where 1 <= x,y <= 10^9
```

**Example 5:**

```text
Input: rains = [10,20,20]
Output: []
Explanation: It will rain over lake 20 two consecutive days. There is no chance to dry any lake.
```

**Constraints:**

1. `1 <= rains.length <= 10^5`
2. `0 <= rains[i] <= 10^9`

## Basic Idea

??????????????????????????????????????????10e5????????????????????????N^2??????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????

?????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????PriorityQueue??????????????????????????????????????????????????????????????????????????????????????????????????????HashMap??????????????????????????????????????????????????????next???????????????????????????????????????????????????????????????

## Java Code???

```java
class Solution {
    public int[] avoidFlood(int[] rains) {
        Map<Integer, Integer> map = new HashMap<>();
        int[] next = new int[rains.length + 1];
        for (int i = rains.length - 1; i >= 0; --i) {
            if (rains[i] != 0) {
                if (map.containsKey(rains[i])) {
                    next[i] = map.get(rains[i]);
                }
                map.put(rains[i], i);
            }
        }
        int[] ret = new int[rains.length];
        PriorityQueue<Integer> nextRainDay = new PriorityQueue<>();
        Set<Integer> fullLakes = new HashSet<>();
        for (int i = 0; i < rains.length; ++i) {
            if (rains[i] != 0) {
                if (fullLakes.contains(rains[i])) {
                    return new int[]{};
                }
                ret[i] = -1;
                if (next[i] != 0) {
                    nextRainDay.offer(next[i]);
                }
                fullLakes.add(rains[i]);
            } else {
                if (!nextRainDay.isEmpty()) {
                    ret[i] = rains[nextRainDay.poll()];
                    fullLakes.remove(ret[i]);
                } else {
                    ret[i] = 1;
                }
            }
        }
        return ret;
    }
}
```

