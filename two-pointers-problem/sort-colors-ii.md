## Sort Colors II
_update Aug 19,2017  23:59_

---
[LintCode](http://www.lintcode.com/en/problem/sort-colors-ii/)

Given an array of n objects with k different colors (numbered from 1 to k), sort them so that objects of the same color are adjacent, with the colors in the order 1, 2, ... k.

**Notice**

1.  You are not suppose to use the library's sort function for this problem.
2.  `k <= n`

**Example**

    Given colors=[3, 2, 2, 1, 4], k=4, your code should sort colors in-place to [1, 2, 2, 3, 4].

**Challenge **
A rather straight forward solution is a two-pass algorithm using counting sort. That will cost O(k) extra memory. Can you do it without using extra memory?

#### Basic Idea:
**思路1：**
用双指针k次，每次解决一个颜色，耗时O(kn), worst case下为O(n^2), 不好；

**思路2：**
因为 `k<=n` ，我们可以利用原本空间进行修改版本的 counting sort:

以下内容摘自 [这里](http://wdxtub.com/interview/14520606003957.html)：

我们可以使用类似桶排序的思想，对所有的数进行计数。

1. 从左扫描到右边，遇到一个数字，先找到对应的bucket.比如 3 2 2 1 4 第一个3对应的bucket是index = 2 (bucket从0开始计算）

2. Bucket 如果有数字，则把这个数字移动到i的position(就是存放起来），然后把bucket记为-1(表示该位置是一个计数器，计1）。

3. Bucket 存的是负数，表示这个bucket已经是计数器，直接减1. 并把color[i] 设置为0 （表示此处已经计算过）

4. Bucket 存的是0，与3一样处理，将bucket设置为-1， 并把color[i] 设置为0 （表示此处已经计算过）

5. 回到position i，再判断此处是否为0（只要不是为0，就一直重复2-4的步骤）。 

6. 完成1-5的步骤后，从尾部到头部将数组置结果。（从尾至头是为了避免开头的计数器被覆盖）
例子(按以上步骤运算)：

        3 2 2 1 4
        2 2 -1 1 4
        2 -1 -1 1 4
        0 -2 -1 1 4
        -1 -2 -1 0 4
        -1 -2 -1 -1 0

#### Java Code:
```java
    class Solution {
        /**
         * @param colors: A list of integer
         * @param k: An integer
         * @return: nothing
         */
        public void sortColors2(int[] colors, int k) {
            int i = 0;
            while (i < colors.length) {
                if (colors[i] > 0) {
                    // 说明需要处理colors[i]存放的颜色
                    int color = colors[i];
                    if (colors[color - 1] > 0) {
                        // 当color对应index存有其他颜色时
                        colors[i] = colors[color - 1]; // 把color对应index的颜色存入i
                        colors[color - 1] = -1; // 把对应index存放其index颜色的数量的相反数 
                    } else {
                        // 当对应index已经是计数器时
                        colors[i] = 0; // 表示原来这里的颜色已经存储过
                        colors[color - 1]--;
                        i++; // 考虑右边的元素
                    }
                } else {
                    // 当前i位置已经处理过
                    i++;
                }
            }
            // 从右往左，把排序后的数组复原在原来位置
            int j = colors.length - 1;
            for (i = colors.length - 1; i >= 0; i--) {
                int count = -colors[i];
                if (count == 0) continue;
                while (count > 0) {
                    colors[j--] = i + 1;
                    count--;
                }
            }
        }
    }
```

