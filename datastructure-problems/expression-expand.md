## Expression Expand （Decode String）
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

python
```python
    # recursive solution
    class Solution:
        # @param {string} s  an expression includes numbers, letters and brackets
        # @return {string} a string
        def expressionExpand(self, s):
            res = []
            i = 0
            while i < len(s):
                # 普通字符
                if s[i].isalpha():
                    res.append(s[i])
                    i += 1
                # 数字
                elif s[i].isdigit():
                    dig = s[i]
                    i += 1
                    while s[i].isdigit():
                        dig += s[i]
                        i += 1
                    factor = int(dig) # 系数得到了

                    # 接下来获取之后括号内的内容
                    i += 1  # 跳过'['
                    right = -1
                    inner = []
                    while right != 0:
                        if s[i] == '[':
                            right -= 1
                        elif s[i] == ']':
                            right += 1
                        if right == 0:
                            break
                        inner.append(s[i])
                        i += 1

                    #  处理inner
                    inner_expanded = self.expressionExpand(inner)
                    res += list(inner_expanded * factor)
                else:
                    i += 1
            return ''.join(res)
```

**思路2：using stack**
使用stack可以模拟recursion的过程，但是要特别注意实现细节，跟踪好每一步之后指针 i 的位置。

Java:
```java
    // non-recusive solution
    public class Solution {
        /**
         * @param s  an expression includes numbers, letters and brackets
         * @return a string
         */
        public String expressionExpand(String s) {
            Deque<Object> stack = new LinkedList<>();
            StringBuilder sb = new StringBuilder();

            for (int i = 0; i < s.length(); ++i) {
                Character c = s.charAt(i);
                // 如果是数字，则找到完整数字，并压栈
                if (Character.isDigit(c)) {
                    Integer factor = 0;
                    String dig = c + "";
                    i++;
                    while (Character.isDigit(s.charAt(i))) {
                        dig += s.charAt(i++);
                    } // 此时i指向数字之后的'['
                    factor = Integer.valueOf(dig);
                    stack.addLast(factor);
                }
                else if (Character.isLetter(c)) {
                    stack.addLast(c);
                }
                if (c == ']' || i == s.length() - 1) {
                    // 在stack中拿出最近的直到数字的字符串，处理之后放回stack中
                    // 便于后面的处理
                    int factor = 1;
                    StringBuilder inner_sb = new StringBuilder();
                    while (! stack.isEmpty()) {
                        if (stack.peekLast().getClass() == Integer.class) {
                            factor = (Integer)(stack.removeLast());
                            break;
                        }
                        inner_sb.append((Character)(stack.removeLast()));
                    }
                    String inner = inner_sb.reverse().toString();
                    for (int j = 0; j < factor; ++j) {
                        for (char ch : inner.toCharArray()) {
                            stack.addLast(ch);
                        }
                    }
                }
            }
            // 把stack中的内容放入stringbuilder，因为这里使用的stack是Deque，可以从
            // 两端pop，很方便
            while (! stack.isEmpty()) {
                sb.append(stack.removeFirst());
            }
            return sb.toString();
        }
    }
```

---
_update 2018-07-19 22:20:02_

#### Update: recursion 模块化解法 java
注意模块化的思路，很多逻辑看似复杂，可以交给一个helper function 稍后实现，这样可以专注核心逻辑。
```java
public class Solution {
    /**
     * @param s: an expression includes numbers, letters and brackets
     * @return: a string
     */
    public String expressionExpand(String s) {
        char[] arr = s.toCharArray();
        return expand(arr, 0, arr.length - 1);
    }

    private int getNumber(char[] arr, int start) {
        int ret = 0;
        int i = start;
        while (arr[i] >= '0' && arr[i] <= '9') {
            ret = ret * 10 + arr[i++] - '0';
        }
        return ret;
    }

    // 从当前‘【’出发，找到与之匹配的‘】’
    private int getNextPos(char[] arr, int start) {
        int left = 0, right = 0;
        for (int i = start; i < arr.length; ++i) {
            if (arr[i] == '[') left++;
            else if (arr[i] == ']') right++;
            if (left == right) return i;
        }
        return -1;
    }

    private String expand(char[] arr, int start, int end) {
        StringBuilder sb = new StringBuilder();
        int i = start;
        while (i <= end) {
            if (arr[i] >= 'a' && arr[i] <= 'z' ||
                arr[i] >= 'A' && arr[i] <= 'Z') {
                sb.append(arr[i++]);
            } else if (arr[i] >= '0' && arr[i] <= '9') {
                int num = getNumber(arr, i);
                // 跳过数字
                while (arr[i] != '[') i++;
                // 得到对应‘]’ 的index
                int nextPos = getNextPos(arr, i);
                String str = expand(arr, i + 1, nextPos - 1);
                for (int k = 0; k < num; ++k) sb.append(str);
                i = nextPos + 1;
            } else {
                return "error,";
            }
        }
        return sb.toString();
    }
}
```
