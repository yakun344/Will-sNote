# Next Permutation

_update Jul 23, 2017 16:35_

[LintCode](http://www.lintcode.com/en/problem/next-permutation/)

Given a list of integers, which denote a permutation.

Find the next permutation in ascending order.

**Notice**

The list may contains duplicate integers.

**Example**

```text
For [1,3,2,3], the next permutation is [1,3,3,2]

For [4,3,2,1], the next permutation is [1,2,3,4]
```

## Basic Idea:

要找所有排列中下一个比当前排列更大的排列。仔细想来，当所有数字从左到右将序排列的时候是最大的。

对于一个现有排列，**如何找到**下一个更大的排列呢？我们从右往左扫描，找到第一个比它右边数小的数字，记录index。然后再从右向左扫描，找到第一个比 nums\[index\] 大的数，记录为 biggerIndex，把 index 和 biggerIndex 位置数字换位，这样我们就在最低的高位更新了一个最小的更大的数，index 右边的数字不违背降序，为了让 index 右边的部分最小（因为我们已经更新了高位（index）），我们需要reverse index 右边的部分。这样我们就得到了next permutation。

## Java Code:

```java
    public class Solution {
        /**
         * @param nums: A list of integers
         * @return: A list of integers that's next permuation
         */
        public int[] nextPermutation(int[] nums) {
            // 从右往左，找第一个破坏降序的数
            int index = -1;
            for (int i = nums.length - 1; i > 0; --i) {
                if (nums[i] > nums[i - 1]) {
                    index = i - 1;
                    break;
                }
            }
            if (index == -1) {
                reverse(nums, 0, nums.length - 1);
                return nums;
            }
            // 从右往左找第一个比nums[index]大的数
            int biggerIndex = -1;
            for (int i = nums.length - 1; i > index; --i) {
                if (nums[i] > nums[index]) {
                    biggerIndex = i;
                    break;
                }
            }
            swich(nums, index, biggerIndex);
            reverse(nums, index + 1, nums.length - 1);
            return nums;
        }

        private void reverse(int[] nums, int p, int r) {
            int i = p, j = r;
            while (i < j) {
                swich(nums, i++, j--);
            }
        }

        private void swich(int[] nums, int i, int j) {
            int t = nums[i];
            nums[i] = nums[j];
            nums[j] = t;
        }
    }
```

