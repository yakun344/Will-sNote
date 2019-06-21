# Generate Random Point in a Circle

_update Feb 14 2019, 19:53_

[LeetCode](https://leetcode.com/problems/generate-random-point-in-a-circle/)

Given the radius and x-y positions of the center of a circle, write a function randPoint which generates a uniform random point in the circle.

**Note:**

input and output values are in floating-point. radius and x-y position of the center of the circle is passed into the class constructor. a point on the circumference of the circle is considered to be in the circle. randPoint returns a size 2 array containing x-position and y-position of the random point, in that order.

**Example 1:**

```text
Input: 
["Solution","randPoint","randPoint","randPoint"]
[[1,0,0],[],[],[]]
Output: [null,[-0.72939,-0.65505],[-0.78502,-0.28626],[-0.83119,-0.19803]]
```

**Example 2:**

```text
Input: 
["Solution","randPoint","randPoint","randPoint"]
[[10,5,-7.5],[],[],[]]
Output: [null,[11.52438,-8.33273],[2.46992,-16.21705],[11.13430,-12.42337]]
Explanation of Input Syntax:
```

The input is two lists: the subroutines called and their arguments. Solution's constructor has three arguments, the radius, x-position of the center, and y-position of the center of the circle. randPoint has no arguments. Arguments are always wrapped with a list, even if there aren't any.

## Basic Idea:

没有什么特别的，主要是一些中学数学的简单问题：

```c
    圆方程：(x-a)^2 + (y-b)^2 = r^2
          圆心为 (a, b), 半径为 r
    如果一个点(x, y) 在圆内，则有 (x-a)^2 + (y-b)^2 < r^2
```

所以我们可以有一个解法，就是用一个该圆的外切正方形作为随机生成点的范围，每次判断生成的点是否在圆内，如果不在，就重新生成随机点。

### Java Code:

```java
    class Solution {
        private double r, a, b;

        public Solution(double radius, double x_center, double y_center) {
            r = radius;
            a = x_center;
            b = y_center;
        }

        public double[] randPoint() {
            while (true) {
                double x = Math.random() * (r * 2) + a - r;
                double y = Math.random() * (r * 2) + b - r;
                if (Math.pow(x - a, 2) + Math.pow(y - b, 2) < r * r) {
                    return new double[]{x, y};
                }
            }
        }
    }
```

