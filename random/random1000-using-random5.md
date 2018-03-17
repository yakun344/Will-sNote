# Random1000 Using Random5
_update Mar 16,2018 23:45_

---
Given a random generator `random5()`, the return value of `random5()` is `0 - 4` with equal probability. Use `random5()` to implement `random1000()`;

## Basic Idea:
和之前的题目 [Random7 Using Random5]() 类似，只是这道题我们需要用循环逻辑来生成这个五进制数的每一位，直到其大于 1000 （这个数是3905，即我们会生成 `random3905()`），然后我们取其生成的 `[0,1,2...,2999]` 的前3000个数的部分，将其 mod3 即可；

* ### Java Code:
```java
