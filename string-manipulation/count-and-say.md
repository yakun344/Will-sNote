## Count and Say
_update Jan 19, 2018  16:46_

---
[LeetCode](https://leetcode.com/problems/count-and-say/description/)

The count-and-say sequence is the sequence of integers with the first five terms as following:
```
        1.     1
        2.     11
        3.     21
        4.     1211
        5.     111221
        1 is read off as "one 1" or 11.
        11 is read off as "two 1s" or 21.
        21 is read off as "one 2, then one 1" or 1211.
```
Given an integer n, generate the nth term of the count-and-say sequence.

**Note:**  
Each term of the sequence of integers will be represented as a string.

**Example 1:**

        Input: 1
        Output: "1"

**Example 2:**

        Input: 4
        Output: "1211"
        
<br>

### Basic Idea:
解法就是按照之前一次的结果，从左到右数相同元素的个数，生成新的结果，再往复继续。具体地，每次的结果存储在一个 StringBuilder 中，例如当前结果为 `1121`，从左到右计算每个char的个数，我们发现有两个1，则将 `21` append 到 newSB 中，再继续往后。
* #### Java Code:
```java
class Solution {
    public String countAndSay(int n) {
        if (n == 1) return "1"; // corner case
        StringBuilder sb = new StringBuilder();
        sb.append("1"); // initialize
        
        // "1" is the 1st one (0), so start from 2nd (1).
        for (int i = 1; i < n; ++i) {
            StringBuilder newSb = new StringBuilder(); // new result stored here
            int curr = 0; // pointer keep going right
            while (curr < sb.length()) {
                int count = 0;
                char c = sb.charAt(curr);
                while (curr < sb.length() && sb.charAt(curr) == c) {
                    count++;
                    curr++;
                }
                newSb.append(String.valueOf(count));
                newSb.append(c);
            }
            sb = newSb;
        }
        return sb.toString();
    }
}
```

<br>

---
_update May 4, 2018  0:58_

### C++ Code:
感觉之前写的Java版本的思路更好理解。注意，c++中将char c转为string可以用 `string(1, c)`;
```cpp
    class Solution {
    public:
        string countAndSay(int n) {
            string last{"1"};
            // 运行 n-1 次
            for (int i = 1; i < n; ++i) {
                string curr;
                char prev = last[0];
                int count = 1;
                // 数之前string每个数字的个数，生成curr string
                for (int j = 1; j < last.size(); ++j) {
                    if (last[j] == prev) {
                        count++;
                    } else {
                        curr.append(to_string(count) + prev);
                        prev = last[j];
                        count = 1;
                    }
                }
                curr.append(to_string(count) + prev);
                last = curr;
            }
            return last;
        }
    };
```











