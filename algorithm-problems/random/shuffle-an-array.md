# Shuffle an Array

_update Jan 25,2018 22:21_

[LeetCode](https://leetcode.com/problems/shuffle-an-array/description/)

Shuffle a set of numbers without duplicates.

**Example:**

```text
// Init an array with set 1, 2, and 3.
int[] nums = {1,2,3};
Solution solution = new Solution(nums);

// Shuffle the array [1,2,3] and return its result. Any permutation of [1,2,3] must equally likely to be returned.
solution.shuffle();

// Resets the array back to its original configuration [1,2,3].
solution.reset();

// Returns the random shuffling of array [1,2,3].
solution.shuffle();
```

## Basic Idea:

基本思路就是维持一个挡板，从右侧开始，选择每个数字，每次从挡板左边的数字中随机选择，然后swap，挡板左移。从右往左是为了生成随机数的时候方便（从0开始，而不是从k开始）。

* **Java Code:**

  ```java
    class Solution {
        private int[] original;
        public Solution(int[] nums) {
            original = nums;
        }

        /** Resets the array to its original configuration and return it. */
        public int[] reset() {
            return original;
        }

        /** Returns a random shuffling of the array. */
        public int[] shuffle() {
            int spliter = original.length - 1;
            int[] ret = Arrays.copyOf(original, original.length);
            while (spliter > 0) {
                // 隔板左边数字个数为 spliter + 1, 生成 (0-, spliter) 的随机数
                int indexToSwap = (int)(Math.random() * (spliter + 1));
                swap(ret, indexToSwap, spliter);
                spliter--;
            }
            return ret;
        }

        private void swap(int[] arr, int i, int j) {
            int t = arr[i];
            arr[i] = arr[j];
            arr[j] = t;
        }
    }
  ```

