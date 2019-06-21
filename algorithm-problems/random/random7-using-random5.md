# Random7 Using Random5

_update Mar 16, 2018 23:10_

Given a random generator `random5()`, the return value of `random5()` is `0 - 4` with equal probability. Use `random5()` to implement `random7()`.

## Basic Idea:

首先思考，如果给定 random7，要求实现 random5 其实很简单，只要将生成的 5,6 直接抛弃，直到生成的随机数在 `[0,1,2,3,4]` 之间就返回即可。可以很简单地证明这里生成的 `[0,1,2,3,4]` 是等概率的。

但是如果给定 random5, 要求 random7, 我们就要先想办法生成一个比 rand7 更大的随机函数。这里我们可以用 `rand5 * 5 + rand5 = rand25` 来生成 random25。之所以这样做是对的，是因为我们可以把数字想象成 5 进制，则第一个 rand5 生成了十位数字，第二个就作为个位数字，这样每个数字的出现概率都相同。然后我们可以得到 `[0,1,2...,24]` 之间的随机数，只去前21个，即 `[0,1,2...,20]`，将其 mod 7 即可得到 random7。

* **Java Code:**

  ```java
  public class Solution {
    public int random7() {
      // write your solution here
      // you can use RandomFive.random5() for generating
      // 0 - 4 with equal probability.
      while (true) {
        int rand = (int)(RandomFive.random5() * 5 + RandomFive.random5());
        if (rand < 21) return rand % 7;
      }
    }
  }
  ```

