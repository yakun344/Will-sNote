## Total Hamming Distance
_update Jan 13, 2018  17:05_

---
[LeetCode](https://leetcode.com/problems/total-hamming-distance/description/)

The Hamming distance between two integers is the number of positions at which the corresponding bits are different.

Now your job is to find the total Hamming distance between all pairs of the given numbers.

**Example:**

    Input: 4, 14, 2

    Output: 6

**Explanation:**   
In binary representation, the 4 is 0100, 14 is 1110, and 2 is 0010 (just
showing the four bits relevant in this case). So the answer will be:  
`HammingDistance(4, 14) + HammingDistance(4, 2) + HammingDistance(14, 2) = 2 + 2 + 2 = 6`;

**Note:**

1. Elements of the given array are in the range of 0 to 10^9; 
2. Length of the array will not exceed 10^4.

#### Basic Idea:
&emsp; 首先，用两层 for loop 对每一对 Integer, 计算他们的 Hamming Distance 然后加起来，但是这样做的时间复杂度会是 `O(31 * n^2)`.    
&emsp; 经过观察，我们可以进行优化：对每一位，我们检查所有Integer在该位是 0 还是 1，统计 1 和 0 的个数，然后对于这一位，产生的 Hamming Distance 就是 `0 的个数 * 1 的个数`，这样，总的时间复杂度就成为了 `O(31 * n)`;