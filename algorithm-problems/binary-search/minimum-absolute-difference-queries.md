# Minimum Absolute Difference Queries

_update Jun 23, 2021_

__[_Leetcode_](https://leetcode.com/problems/minimum-absolute-difference-queries/)__

![image](https://user-images.githubusercontent.com/24964756/123195824-acbc4a00-d45d-11eb-88e5-673b7331c20b.png)

## Basic Idea

我们注意到虽然数组nums和queries的长度都很大，但是nums数据的范围很小。如果我们可以用一个长度为 100 的数组存储每个数字对应在nums中的index，然后在query的时候我们只需要从左往右扫描从1到100每个数字，然后更新相邻两个有query范围内的index出现的数字之间的最小绝对之差即可。

其中一个细节是如何快速知道某个数字对应的index中是否包含query所标记的范围，我们可以使用二分法，也可以用BST。两者时间复杂度是类似的，但是TreeSet的overhead会大一些。

## JavaCode

### 1. TreeSet

```java
class Solution {
    public int[] minDifference(int[] nums, int[][] queries) {
        TreeSet[] arr = new TreeSet[101];
        for (int i = 1; i <= 100; ++i) {
            arr[i] = new TreeSet<Integer>();
        }
        for (int i = 0; i < nums.length; ++i) {
            arr[nums[i]].add(i);
        }
        int[] res = new int[queries.length];
        for (int k = 0; k < queries.length; ++k) {
            int[] query = queries[k];
            int l = query[0], r = query[1];
            int minDiff = 9999;
            int last = -1;
            for (int i = 1; i <= 100; ++i) {
                Integer ceiling = (Integer) arr[i].ceiling(l);
                Integer floor = (Integer) arr[i].floor(l);
                if (ceiling != null && ceiling <= r 
                    || floor != null &&  floor >= l
                   ) {
                    if (last != -1) {
                        minDiff = Math.min(Math.abs(i - last), minDiff);
                        // 优化，因为最小的diff就是1
                        if (minDiff == 1) break;
                    }
                    last = i;
                }
            }
            if (minDiff == 9999) {
                res[k] = -1;
            } else {
                res[k] = minDiff;
            }
        }
        return res;
    }
}
```

### 2. Binary Search

```java
class Solution {
    public int[] minDifference(int[] nums, int[][] queries) {
        ArrayList<Integer>[] mapping = new ArrayList[101];
        for (int i = 0; i < mapping.length; ++i) {
            mapping[i] = new ArrayList<>();
        }
        for (int i = 0; i < nums.length; ++i) {
            mapping[nums[i]].add(i);
        }

        int[] res = new int[queries.length];
        Arrays.fill(res, Integer.MAX_VALUE);
        for (int k = 0; k < queries.length; ++k) {
            int[] query = queries[k];
            int last = Integer.MAX_VALUE;
            for (int i = 1; i <= 100; ++i) {
                if (contains(mapping[i], query[0], query[1])) {
                    if (last != Integer.MAX_VALUE) {
                        res[k] = Math.min(res[k], Math.abs(i - last));
                    }
                    last = i;
                }
            }
            if (res[k] == Integer.MAX_VALUE) {
                res[k] = -1;
            }
        }
        return res;
    }

    // 检查list中是否有[lower, upper]范围内元素
    // 因list是某数字出现的index，所以没有重复
    private boolean contains(ArrayList<Integer> list, int lower, int upper) {
        if (list.isEmpty()) {
            return false;
        }
        int left = 0, right = list.size() - 1;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (list.get(mid) < lower) {
                left = mid;
            } else {
                right = mid;
            }
        }
        if (inRange(list.get(left), lower, upper) || inRange(list.get(right), lower, upper)) {
            return true;
        }
        return false;
    }

    private boolean inRange(int k, int lower, int upper) {
        return k >= lower && k <= upper;
    }
}
```
