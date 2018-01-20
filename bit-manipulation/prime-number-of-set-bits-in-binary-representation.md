## Prime Number of Set Bits in Binary Representation
_update Jan 20, 2018  9:53_

---
[LeetCode](https://leetcode.com/problems/prime-number-of-set-bits-in-binary-representation/description/)

Given two integers L and R, find the count of numbers in the range `[L, R]` (inclusive) having a prime number of set bits in their binary representation.

(Recall that the number of set bits an integer has is the number of 1s present when written in binary. For example, 21 written in binary is 10101 which has 3 set bits. Also, 1 is not a prime.)

**Example 1:**
    
    Input: L = 6, R = 10
    Output: 4
    
    Explanation:
    6 -> 110 (2 set bits, 2 is prime)
    7 -> 111 (3 set bits, 3 is prime)
    9 -> 1001 (2 set bits , 2 is prime)
    10->1010 (2 set bits , 2 is prime)

**Example 2:**
    
    Input: L = 10, R = 15
    Output: 5
    
    Explanation:
    10 -> 1010 (2 set bits, 2 is prime)
    11 -> 1011 (3 set bits, 3 is prime)
    12 -> 1100 (2 set bits, 2 is prime)
    13 -> 1101 (3 set bits, 3 is prime)
    14 -> 1110 (3 set bits, 3 is prime)
    15 -> 1111 (4 set bits, 4 is not prime)

**Note:**

L, R will be integers L <= R in the range [1, 10^6].
R - L will be at most 10000.

<br>

#### Basic Idea:
这道题考察基本的**位操作**能力和对于**int 性质**的理解。这道题中要求计数每个数字中 1 的个数是否是质数，因为这里提到的数字都是 int，我们只需要穷举 `[0-32]` 内的所有质数存入set，然后用每个数字中 1 的个数去 set 中查询。

#### Java Code:
```java
class Solution {
    public int countPrimeSetBits(int L, int R) {
        Integer[] primeArray = new Integer[] {2,3,5,7,11,13,17,19,23,29,31};
        Set<Integer> primeSet = new HashSet<>(Arrays.asList(primeArray));
        int ret = 0;
        for (int i = L; i <= R; ++i) {
            if (primeSet.contains(countBits(i))) ret++;
        }
        return ret;
    }
    
    private int countBits(int n) {
        int ret = 0;
        while (n != 0) {
            ret += n & 1;
            n >>>= 1;
        }
        return ret;
    }
}
```












