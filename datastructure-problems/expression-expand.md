## Expression Expand
_update Aug 24,2017  17:43_

---
[LintCode](http://www.lintcode.com/en/problem/expression-expand/)

Given an expression s includes numbers, letters and brackets. Number represents the number of repetitions inside the brackets(can be a string or another expression)．Please expand expression to be a string.

**Example**

        s = abc3[a] return abcaaa
        s = 3[abc] return abcabcabc
        s = 4[ac]dy, return acacacacdy
        s = 3[2[ad]3[pf]]xyz, return adadpfpfpfadadpfpfpfadadpfpfpfxyz

**Challenge **
Can you do it without recursion?

#### Basic Idea:
**思路1：recursive dfs**
就是利用recursion，把每一层括号内的内容都直接丢给下级函数去做。但是要注意实现细节。

java:
```java
    // recursive solution
    public class Solution {
        /**
         * @param s  an expression includes numbers, letters and brackets
         * @return a string
         */
        public String expressionExpand(String s) {
            StringBuilder sb = new StringBuilder();
            for (int i = 0; i < s.length(); ++i) {
                // 普通字母
                char c = s.charAt(i);
                if (Character.isLetter(c)) {
                    sb.append(c);
                }
                // 数字
                else if (Character.isDigit(c)) {
                    // 记录数字
                    String fact = c + "";
                    while (Character.isDigit(s.charAt(i + 1))) {
                        fact += s.charAt(i + 1);
                        i++;
                    }
                    int factor = Integer.parseInt(fact);
                    // 读取后续括号内的串, 方法是计数左右括号数量的差，左记为-1，右记为+1，
                    // 当right=0时，表示这次数字后面的串读完
                    i += 2; // 跳过 '['
                    String inner = "";
                    int right = -1;
                    while (true) {
                        if (s.charAt(i) == '[') right -=1;
                        else if (s.charAt(i) == ']') right += 1;
                        inner += s.charAt(i);
                        if (right == 0) break;
                        i++;
                    }
                    // 把inner解析之后的内容重复factor次
                    String inner_expanded = expressionExpand(inner);
                    for (int j = 0; j < factor; ++j) {
                        sb.append(inner_expanded);
                    }
                }
            }   
            return sb.toString();
        }
    }
```

**思路2：using stack**
使用stack可以模拟recursion的过程；






