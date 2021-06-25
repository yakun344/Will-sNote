# Matchsticks to Square
_update Jun 24, 2021_

---
![image](https://user-images.githubusercontent.com/24964756/123380044-cf289300-d543-11eb-8f99-76c243496151.png)

## Basic Idea
基本思路就是利用DFS来考虑每种情况，看能否将不同的各个木条放入四个边最终形成正方形。

## Java Code
```java
class Solution {
    int edgeLen;
    
    public boolean makesquare(int[] matchsticks) {
        int sum = 0;
        for (int num : matchsticks) sum += num;
        if (sum % 4 != 0) {
            return false;
        }
        edgeLen = sum / 4;
        return dfs(matchsticks, 0, new int[4]);
    }
    
    private boolean dfs(int[] arr, int pos, int[] edges) {
        if (pos == arr.length) return true;
        Set<Integer> visited = new HashSet<>();
        for (int i = 0; i < 4; ++i) {
            if (edges[i] + arr[pos] <= edgeLen) {
                if (visited.add(edges[i] + arr[pos])) {// 去重
                    edges[i] += arr[pos];
                    if (dfs(arr, pos + 1, edges)) {
                        return true;
                    }
                    edges[i] -= arr[pos];
                }
            }
        }
        return false;
    }
}
```
