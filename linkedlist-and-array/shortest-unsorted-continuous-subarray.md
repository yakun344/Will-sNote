## Shortest Unsorted Continuous Subarray
_update Jun 30, 2017 15:24_

---
[leetcode](https://leetcode.com/problems/shortest-unsorted-continuous-subarray/#/description)
Given an integer array, you need to find one **continuous** subarray that if you only sort this subarray in **ascending order**, then the whole array will be sorted in ascending order, too.

You need to find the shortest such subarray and output its length.

    Example 1:
    Input: [2, 6, 4, 8, 10, 9, 15]
    Output: 5
    Explanation: You need to sort [6, 4, 8, 10, 9] in ascending order to make the whole array sorted in ascending order.
**Note:**
Then length of the input array is in range [1, 10,000].
The input array may contain duplicates, so ascending order here means `<=`.

#### 思路：
*  比较简单的想法就是用input array 和 sorted array 进行对比，找出最两端的和原数组不同的元素即为所求 sequence 的首尾。这个方法比较慢，O(NlogN) time O(N)space。
*  `O(N)time O(1)space`的方法。**重点**是要应用 input array 自身的性质： 把input array 视作一个有一些元素互相换位破坏了排序性质的 sorted array。那么左边比较小的元素换到了右边，就一定有右边比较大的元素被换到左边。所以，所求的**unsorted subsequence**的左边界一定比它右边的最小值要大，同理，右边界也一定比它左边的最大值小。我们只要找到这两个**边界**就可以确定所求 sequence 的长度。
    
#### O(nlogn) Java Implementation：
```java
    // 先复制数组，排序，找出最两边与原数组不同的index，中间即为所求的sequence
    public class Solution {
        public int findUnsortedSubarray(int[] nums) {
            int[] aux = nums.clone();
            Arrays.sort(aux);
            int left = -1, right = - 1;
            for (int i = 0; i < aux.length; ++i) {
                if (aux[i] != nums[i]) {
                    if (left == -1) left = i;
                }
                if (aux[nums.length - 1 - i] != nums[nums.length - 1 - i]) {
                    if (right == -1) right = nums.length - 1 - i;
                }
                if (left != -1 && right != -1) return right - left + 1;
            }
            return 0;
        }
    }
```

#### O(N) Python Implementation:
```python
    class Solution(object):
        def findUnsortedSubarray(self, nums):
            """
            :type nums: List[int]
            :rtype: int
            """
            if not nums or len(nums) == 1: return 0
            leftMax = -0x7fffffff
            rightMin = 0x7fffffff
            start = -1
            end = -1
            # 从右往左找最左边的大于右边部分最小值的element的index作为左边界start
            # 同理，从左往右找最后一个小于左边最大值的作为右边界end
            for i in range(len(nums)):
                leftMax = max(nums[i], leftMax) # find max from left
                rightMin = min(nums[len(nums) - 1 - i], rightMin) # find min from right
                if nums[i] < leftMax:
                    end = i
                if nums[len(nums) - 1 - i] > rightMin:
                    start = len(nums) - 1 - i
            if start == -1:
                return 0
            return end - start + 1
```

---
_update Nov 30, 2017 16:35_
#### 更新
时隔数月，再次看到这道easy竟已经想不出 O(n) time 的最优解了。发现最优解的思路的确足够巧妙，以至从前的我以为自己懂了，却并没有真正领会其中的精神。于是把最优解又仔细考虑了一遍，发现画图和举例子对于找思路非常有用：
![](/assets/Shortest Unsorted Continuous Subarray.jpg)

**更新nlog(n)时间solution的java code，逻辑简化了不少：**
```java
    class Solution {
        public int findUnsortedSubarray(int[] nums) {
            if (nums.length == 0) return 0;
            int[] sortedNums = Arrays.copyOf(nums, nums.length);
            Arrays.sort(sortedNums);
            int left = 0, right = nums.length - 1;
            while (left < nums.length && nums[left] == sortedNums[left]) left++;
            while (right > 0 && nums[right] == sortedNums[right]) right--;
            return left < right ? right - left + 1 : 0;
        }
    }
```

**更新O(n)时间最优解 Python Code，优化了逻辑，比之前的容易理解许多**
```python
    class Solution:
        def findUnsortedSubarray(self, nums):
            """
            :type nums: List[int]
            :rtype: int
            """
            # 从左往右找最大值，以及其右边小于该最大值的最右边的数，这个数就是右边界；
            # 左边界同理，从右往左找最小值，最左边大于它的数就是左边界
            # 可以用 1，2，4，5，3 这个数组举例子
            if not nums: return 0
            leftMax = nums[0]
            rightMin = nums[-1]
            leftBd = -1
            rightBd = -1
            # 从左往右找左边最大值，并确定右边界
            for i in range(len(nums)):
                if nums[i] > leftMax:
                    leftMax = nums[i]
                elif nums[i] < leftMax:
                    rightBd = i
            # 确定左边界
            for i in range(len(nums) - 1, -1, -1):
                if nums[i] < rightMin:
                    rightMin = nums[i]
                elif nums[i] > rightMin:
                    leftBd = i
                    
            return rightBd - leftBd + 1 if rightBd > leftBd else 0
```









