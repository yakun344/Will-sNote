# Intersection of Two Arrays \(easy\)

## Intersection of Two Arrays \(easy\)

_update Jan 26, 2018 16:20_

[LeetCode](https://leetcode.com/problems/intersection-of-two-arrays/description/)

Given two arrays, write a function to compute their intersection.

**Example:**

```text
Given nums1 = [1, 2, 2, 1], nums2 = [2, 2], return [2].
```

**Note:**

1. Each element in the result must be unique.
2. The result can be in any order.

### Basic Idea:

* **思路1：sorting + 2 pointers**

  先对两个数组排序，然后用两个指针从左到右扫描，谁小移谁，记录相同元素，并同时去重。

  * **Java Code:**

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

* **思路2：Set**

  将其中一个数组的元素加入set，然后扫描另一个数组，如果数字存在于set中，则将其加入res，同时删去set中该元素（去重）；

  * **Java Code：**

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

## Intersection of Two Arrays II

[leetCode](https://leetcode.com/problems/intersection-of-two-arrays-ii/description/)

Given two arrays, write a function to compute their intersection.

**Example:**

```text
Given nums1 = [1, 2, 2, 1], nums2 = [2, 2], return [2, 2].
```

**Note:**

1. Each element in the result should appear as many times as it shows in both arrays.
2. The result can be in any order.

**Follow up:**

1. What if the given array is already sorted? How would you optimize your algorithm?
2. What if nums1's size is small compared to nums2's size? Which algorithm is better?
3. What if elements of nums2 are stored on disk, and the memory is limited such that you cannot load all elements into the memory at once?

### Solution

Basically，只要用一个HashMap记录一个array中每个数字的出现次数，然后扫描另一个array，检查其在map中对应个数，并每次将个数减去1，同时将存在的元素加入res list 即可。

* **Java Code：**

  ```java
  class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int num : nums1) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }
        List<Integer> list = new ArrayList<>();
        for (int num : nums2) {
            int times = map.getOrDefault(num, 0);
            if (times > 0) {
                list.add(num);
                map.put(num, times - 1);
            }
        }
        return list.stream().mapToInt(Integer::intValue).toArray();
    }
  }
  ```

* **Follow up:**
  * **What if the given array is already sorted? How would you optimize your algorithm?**

    如果已经sorted，直接用双指针法比较，每次相同元素加入res，否则谁小移谁；

  * **What if nums1's size is small compared to nums2's size? Which algorithm is better?**

    把 nums1 放入 HashTable，节省space；

  * **What if elements of nums2 are stored on disk, and the memory is limited such that you cannot load all elements into the memory at once?**

    如果 nums1 可以 fit in memory，把 nums1 全部放入 memory 做 hash，然后 block by block 地从磁盘中读出 nums2 进行验证。如果两个array都非常大无法放入内存，可以对他们分别 external sort，然后按顺序合并。

