# Maximum XOR of Two Numbers in an Array

_update Jul,30 2017 16:25_

[leetcode](https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/description/)

Given a non-empty array of numbers, a0, a1, a2, … , an-1, where 0 ≤ ai < 231.

Find the maximum result of ai XOR aj, where 0 ≤ i, j < n.

Could you do this in** O(n) runtime**?

**Example:**

```
Input: [3, 10, 5, 25, 2, 8]

Output: 28

Explanation: The maximum result is 5 ^ 25 = 28.
```

## Basic Idea:

这道题用两层for循环可以轻易解决，但是需要O(n^2)的时间复杂度，因为对于每个数，我们需要对比 n 次xor 和 maxXor。于是我们想到，如果可以将求每个数与其他数之间的最大 xor 的过程优化到O(const)，我们就可以得到`O(const * n)`的最终复杂度。因为每个数的长度都相等 (32bit integer)，我们就想到可以使用 Trie 来解决。Trie 的高度是32，每层最多有 2 个 node (0 and 1)。则对每个数求其于其他数最大xor的时间是 O(32)，共有 n 个数，加上构造 trie 的 O(32n)，总的时间为`O(2 * 32 n)`。

## Java Code：

```java
    // O(32n) solution using Trie
    public class Solution {
        // define Trie node
        private class TrieNode {
            TrieNode[] child;
            public TrieNode() {
                this.child = new TrieNode[2];
            }
        }

        public int findMaximumXOR(int[] nums) {
            // init trie
            TrieNode root = initTrie(nums);

            // 对每一个数，在trie中找它对所有数xor的最大值，更新全局最大值，就是解
            int globalMax = Integer.MIN_VALUE;
            for (int num : nums) {
                int max = 0; // 可以 O(32) 的时间找到当前数的 maxXor
                TrieNode currNode = root; // 从最高位开始向下查看
                for (int i = 31; i >= 0; --i) {
                    // 检查 currNode.child，如果有和num该位不同的child，则进入，继续寻找不同，
                    // 若没有，则往下继续寻找不同
                    int digit = (num >>> i) & 1;
                    if (currNode.child[digit ^ 1] != null) { // 取反，只对第一位
                        max |= 1 << i; // equlvalent to : max += 1 << i, 因为 i 不会重复
                        currNode = currNode.child[digit ^ 1];
                    } else {
                        currNode = currNode.child[digit];
                    }
                }
                globalMax = Math.max(globalMax, max);
            }
            return globalMax;
        }

        // insert all numbers in trie as 32bit binary sequence
        // 从高位到低位
        private TrieNode initTrie(int[] nums) {
            TrieNode root = new TrieNode();
            for (int num : nums) {
                // 从高到低位插入
                TrieNode currNode = root;
                for (int i = 31; i >= 0; --i) {
                    int digit = (num >>> i) & 1; // 0 or 1
                    if (currNode.child[digit] == null) {
                        currNode.child[digit] = new TrieNode();
                    }
                    currNode = currNode.child[digit];
                }
            }
            return root;
        }
    }
```
