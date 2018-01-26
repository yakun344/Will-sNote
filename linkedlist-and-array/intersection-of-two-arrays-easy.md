# Intersection of Two Arrays (easy)
_update Jan 26, 2018  16:20_

---
[LeetCode](https://leetcode.com/problems/intersection-of-two-arrays/description/)

Given two arrays, write a function to compute their intersection.

**Example:**

    Given nums1 = [1, 2, 2, 1], nums2 = [2, 2], return [2].

**Note:**

1. Each element in the result must be unique.
2. The result can be in any order.

<br>

## Basic Idea:
* ### 思路1：sorting + 2 pointers
先对两个数组排序，然后用两个指针从左到右扫描，谁小移谁，记录相同元素，并同时去重。
  * #### Java Code:
  ```java
    class Solution {
        public int[] intersection(int[] nums1, int[] nums2) {
            if (nums1.length == 0 || nums2.length == 0) return new int[]{};
            Arrays.sort(nums1);
            Arrays.sort(nums2);
            int i = 0, j = 0;
            List<Integer> res = new ArrayList<>();
            while (i < nums1.length && j < nums2.length) {
                if (nums1[i] == nums2[j]) {
                    if (res.isEmpty() || res.get(res.size() - 1) != nums1[i]) {
                        res.add(nums1[i]);
                    }
                    i++;
                    j++;
                } else if (nums1[i] < nums2[j]) {
                    i++;
                } else {
                    j++;
                }
            }
            return res.stream().mapToInt(Integer::intValue).toArray();
        }
    }
  ```
  
* ### 思路2：Set
将其中一个数组的元素加入set，然后扫描另一个数组，如果数字存在于set中，则将其加入res，同时删去set中该元素（去重）；
  * #### Java Code：
  注意 Java8 stream 的运用
  ```java
    class Solution {
        public int[] intersection(int[] nums1, int[] nums2) {
            if (nums1.length == 0 || nums2.length == 0) return new int[0];
            Set<Integer> set = Arrays.stream(nums1).boxed().collect(Collectors.toCollection(HashSet::new));
            List<Integer> list = new ArrayList<>();
            for (int num : nums2) {
                if (set.contains(num)) {
                    list.add(num);
                    set.remove(num);
                }
            }
            return list.stream().mapToInt(Integer::intValue).toArray();
        }
    }
  ```










  