# Queue Reconstruction by Height （Medium）

_update Jan 26,2018 9:47_

[LeetCode](https://leetcode.com/problems/queue-reconstruction-by-height/description/)

Suppose you have a random list of people standing in a queue. Each person is described by a pair of integers `(h, k)`, where h is the height of the person and k is the number of people in front of this person who have a height greater than or equal to h. Write an algorithm to reconstruct the queue.

**Note:** The number of people is less than 1,100.

**Example**

```text
Input:
[[7,0], [4,4], [7,1], [5,0], [6,1], [5,2]]

Output:
[[5,0], [7,0], [5,2], [6,1], [4,4], [7,1]]
```

## Basic Idea:

以身高作为主key，反向排序，以在其之前不低于其高度的人数为副key，正向排序，上面的例子排序之后如下图所示：

```c
    input:  [[7,0], [4,4], [7,1], [5,0], [6,1], [5,2]]
    sorted: [[7,0], [7,1], [6,1], [5,0], [5,0], [4,4]]

    我们注意到排序之后，从左向右看，如果我们按照其副key作为其index，将它们依次插入 list，就可得到 result；
    之所以这样做是对的，是因为这样恰好满足了副key存在的意义，即在e之前高度不小于e的数量。

    每次插入操作之后 list 的变化如下：
    [7, 0] 
    [7, 0] , [7, 1]  
    [7, 0] , [6, 1] , [7, 1]  
    [5, 0] , [7, 0] , [6, 1] , [7, 1]  
    [5, 0] , [7, 0] , [5, 2] , [6, 1] , [7, 1]  
    [5, 0] , [7, 0] , [5, 2] , [6, 1] , [4, 4] , [7, 1]
```

由于我们选择使用LinkedList，每次插入操作的时间复杂度是 `O(当前插入的index)` ，所以 worst case is O\(n^2\), 空间复杂度 `O(n)`;

* Java Code:

  ```java
  class Solution {
    public int[][] reconstructQueue(int[][] people) {
        Arrays.sort(people, (p1, p2)->{
            if (p1[0] == p2[0]) return Integer.compare(p1[1], p2[1]);
            else                return Integer.compare(p2[0], p1[0]);
        });
        List<int[]> res = new LinkedList<>();
        for (int[] p : people) {
            res.add(p[1], p);
        }
        return res.toArray(new int[people.length][]);
    }
  }
  ```

