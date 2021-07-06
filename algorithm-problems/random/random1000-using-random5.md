# Random1000 Using Random5

_update Mar 16,2018 23:45_

Given a random generator `random5()`, the return value of `random5()` is `0 - 4` with equal probability. Use `random5()` to implement `random1000()`;

## Basic Idea:

和之前的题目 [Random7 Using Random5](https://app.gitbook.com/@willguo/s/willguo-algo/~/drafts/-MdtyW6bL0PeSOMyazhB/algorithm-problems/random/random7-using-random5) 类似，只是这道题我们需要用循环逻辑来生成这个五进制数的每一位，直到其可以大于1000，这个数字是3124，则我们可以先生成 `random3125()`, 然后取前 3000 个数 `[0,1,2...,2999]`, 然后 mod 1000 即可。

* **Java Code:**

  ```java
  public class Solution {
    public int random1000() {
      // Write your solution here.
      // you can use RandomFive.random5() for generating
      // 0 - 4 with equal probability.
      while (true) {
        int rand = 0;
        for (int digit = 4; digit >= 0; --digit) {
          rand += RandomFive.random5() * Math.pow(5, digit);
        }
        if (rand < 3000) return rand % 1000;
      }
    }
  }
  ```

