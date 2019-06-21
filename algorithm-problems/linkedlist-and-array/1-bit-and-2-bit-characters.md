# 1-bit and 2-bit Characters

_update May 14,2018 20:19_

[LeetCode](https://leetcode.com/problems/1-bit-and-2-bit-characters/description/)

We have two special characters. The first character can be represented by one bit 0. The second character can be represented by two bits \(10 or 11\).

Now given a string represented by several bits. Return whether the last character must be a one-bit character or not. The given string will always end with a zero.

**Example 1:**

```text
Input: 
bits = [1, 0, 0]
Output: True
Explanation: 
The only way to decode it is two-bit character and one-bit character. So the last character is one-bit character.
```

**Example 2:**

```text
Input: 
bits = [1, 1, 1, 0]
Output: False
Explanation: 
The only way to decode it is two-bit character and two-bit character. So the last character is NOT one-bit character.
```

**Note:**

* `1 <= len(bits) <= 1000`.
* `bits[i]` is always 0 or 1.

## Basic Idea:

可以先随便写几个序列进行观察，发现我们可以直接从左向右推算。用一个指针 i 跟踪当前需要开始检测的 element，保证 i 之前已经完成匹配，则如果 i 指向 0，那么就是 0，`i+=1`; 如果是 1， 则若不是11 就是 10，这两种情况，都可以令 `i += 2`，然后继续匹配。出口就是如果 i 停在 `nums.size() - 1`，则一定可以匹配最后一个 `0`。

* **C++ Code:**

  ```cpp
    class Solution {
    public:
        bool isOneBitCharacter(vector<int>& bits) {
            if (bits.size() == 1) return true;
            int i = 0;
            while (i < bits.size()) {
                if (bits[i] == 0) ++i;
                else i += 2;
                if (i == bits.size() - 1) return true;
            }
            return false;
        }
    };
  ```

