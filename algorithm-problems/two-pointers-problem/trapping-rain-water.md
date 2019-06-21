# Trapping Rain Water

_update sep 6 2018, 11:32_

[LeetCode](https://leetcode.com/problems/trapping-rain-water/description/)

Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

![](http://www.leetcode.com/static/images/problemset/rainwatertrap.png)

The above elevation map is represented by array `[0,1,0,2,1,0,1,3,2,1,2,1]`. In this case, 6 units of rain water \(blue section\) are being trapped. Thanks Marcos for contributing this image!

**Example:**

```text
Input: [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
```

## Basic Idea

### Solution 1： Two Pointers

对于一对左右边界来说，如果不考虑之前可能有更高的边界，这两个边界中间水位高度取决于较低的边界。于是，我们可以用两个指针分别从两边出发向中间逼近，每次更新leftMax，rightMax，waterHeight 和 water。具体方法是，如果 `leftMax > rightMax`, 则 right 向左逼近，否则 left 向右逼近。

* **Java Code:**

  ```java
  class Solution {
   public int trap(int[] height) {
       int left = -1, right = height.length, leftMax = 0, rightMax = 0;
       int water = 0, waterHeight = 0;
       while (left + 1 < right) {
           // 根据leftmax和rightmax更新waterHeight和water
           int currHeight = Math.min(leftMax, rightMax);
           if (currHeight > waterHeight) {
               water += (right - left - 1) * (currHeight - waterHeight);
               waterHeight = currHeight;
           }
           // 之后移动left和right，更新leftmax和rightmax，并减去柱子占据的空间
           if (leftMax > rightMax) {
               right--;
               rightMax = Math.max(rightMax, height[right]);
               if (waterHeight > 0) {
                   water -= Math.min(waterHeight, height[right]);
               }
           } else {
               left++;
               leftMax = Math.max(leftMax, height[left]);
               if (waterHeight > 0) {
                   water -= Math.min(waterHeight, height[left]);
               }
           }
       }
       return water;
   }
  }
  ```

### Solution 2: DP

对于每个横坐标，我们可以通过检查其左边的最高墙 leftMax 和其右边的最高墙 rightMax 求得该位置水的高度，我们只要扫描一遍横坐标，将每个位置上水位高度加起来就可以了。

那么如何用 O\(n\) 时间求得每个点左边的最大值 leftMax 和右边最大值 rightMax 呢，我们可以利用dp的思路先生成这两个数组，总时间复杂度为 O\(3n\);

## Java Code:

```java
class Solution {
    public int trap(int[] height) {
        if (height.length == 0) return 0;
        int[] leftMaxs = new int[height.length];
        int[] rightMaxs = new int[height.length];
        // 生成leftMax数组
        leftMaxs[0] = height[0];
        for (int i = 1; i < height.length; ++i) {
            if (leftMaxs[i - 1] < height[i]) {
                leftMaxs[i] = height[i];
            } else {
                leftMaxs[i] = leftMaxs[i - 1];
            }
        }
        // 生成rightMax数组
        rightMaxs[rightMaxs.length - 1] = height[height.length - 1];
        for (int i = height.length - 2; i >= 0; --i) {
            if (rightMaxs[i + 1] < height[i]) {
                rightMaxs[i] = height[i];
            } else {
                rightMaxs[i] = rightMaxs[i + 1];
            }
        }
        // 扫描height数组，累加每一条water
        int water = 0;
        for (int i = 0; i < height.length; ++i) {
            int waterHeight = Math.min(leftMaxs[i], rightMaxs[i]);
            if (waterHeight > height[i]) {
                water += waterHeight - height[i];
            }
        }
        return water;
    }
}
```

