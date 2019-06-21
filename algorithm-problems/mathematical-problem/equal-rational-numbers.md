# Equal Rational Numbers

_update Jan 9 2019, 20:26_

[LeetCode](https://leetcode.com/problems/equal-rational-numbers/)

Given two strings S and T, each of which represents a non-negative rational number, return True if and only if they represent the same number. The strings may use parentheses to denote the repeating part of the rational number.

In general a rational number can be represented using up to three parts: an integer part, a non-repeating part, and a repeating part. The number will be represented in one of the following three ways:

```text
<IntegerPart> (e.g. 0, 12, 123)
<IntegerPart><.><NonRepeatingPart>  (e.g. 0.5, 1., 2.12, 2.0001)
<IntegerPart><.><NonRepeatingPart><(><RepeatingPart><)> (e.g. 0.1(6), 0.9(9), 0.00(1212))
```

The repeating portion of a decimal expansion is conventionally denoted within a pair of round brackets. For example:

`1 / 6 = 0.16666666... = 0.1(6) = 0.1666(6) = 0.166(66)`

Both `0.1(6)` or `0.1666(6)` or `0.166(66)` are correct representations of `1 / 6`.

**Example 1:**

```text
Input: S = "0.(52)", T = "0.5(25)"
Output: true
Explanation:
Because "0.(52)" represents 0.52525252..., and "0.5(25)" represents 0.52525252525..... , 
    the strings represent the same number.
```

**Example 2:**

```text
Input: S = "0.1666(6)", T = "0.166(66)"
Output: true
```

**Example 3:**

```text
Input: S = "0.9(9)", T = "1."
Output: true
Explanation: 
"0.9(9)" represents 0.999999999... repeated forever, which equals 1. 
"1." represents the number 1, which is formed correctly: (IntegerPart) = "1" and 
    (NonRepeatingPart) = "".
```

**Note:**

1. Each part consists only of digits.
2. The `<IntegerPart>` will not begin with 2 or more zeros.  \(There is no other restriction on the digits of each part.\)
3. `1 <= <IntegerPart>.length <= 4`
4. `0 <= <NonRepeatingPart>.length <= 4`
5. `1 <= <RepeatingPart>.length <= 4`

## Basic Idea:

* **思路1: 转换为double**

  因为题目规定所给的数字都比较短，包括循环部分在内都不会超过12位，所以我们可以通过对输入的String进行处理，是他们到达一定长度，然后用 `Double.parseDouble(String)` 将其转换为double，然后比较是否相等。

  例如对于输入 "123.2" 和 "123.2\(22\)"，我们先将其处理成为 "123.2" 和 "123.22222222222222222"，然后转换为double比较大小。

  这个解法比较取巧，如果输入string的长度限制取消，就不work了。

  **Java Code:**

  ```java
    class Solution {
        public boolean isRationalEqual(String S, String T) {
            double n1 = getDouble(S);
            double n2 = getDouble(T);
            return n1 == n2;
        }

        private double getDouble(String s) {
            int repeatStart = s.indexOf('(') + 1;
            String repeat = repeatStart == 0 ? "" : s.substring(repeatStart, s.length() - 1);
            String nonRepeat = repeatStart == 0 ? s : s.substring(0, repeatStart - 1);
            s = nonRepeat;
            for (int i = 0; i < 20; ++i) {
                s += repeat;
            }
            return Double.parseDouble(s);
        }
    }
  ```

* **思路2: 展开，逐位比较**

  因为输入数据长度有限，我们只要将其展开足够长，就可以进行比较。

  注意预处理时对没有 "." 的补 ".0", 对于没有循环的补 "\(0\)", 方便后续处理。

  我们将输入string展开到至少 15 位，比较其前15位是否相等，需要注意 `1 = 0.9(9)` 的这种情况。（之所以是15位，是因为非循环部分最多9位，循环部分长度最多为4，所以15位足够判断了）

  **Java Code:**

  ```java
    class Solution {
        public boolean isRationalEqual(String S, String T) {
            S = expand(preProcess(S));
            T = expand(preProcess(T));

            for (int i = 0; i < 15; ++i) {
                char cs = S.charAt(i);
                char ct = T.charAt(i);
                if (S.charAt(i) == T.charAt(i)) continue;
                if (Math.abs(cs - ct) == 1) {
                    if (cs > ct) {
                        String m = T;
                        T = S;
                        S = m;
                    }
                    // 现在 S[i] == T[i] - 1
                    // 判断大数后面是否都是0，小数后面是否都是9，遇到都是'.'的情况跳过
                    for (int j = i + 1; j < 15; ++j) {
                        cs = S.charAt(j);
                        ct = T.charAt(j);
                        if (cs == '.' && ct == '.') continue;
                        else if (cs == '.' || ct == '.') return false;
                        if (cs != '9') return false;
                        if (ct != '0') return false;
                    }
                    return true; 
                } else {
                    return false;
                }
            }
            return true;
        }

        // 将重复部分扩展到至少15位长
        private String expand(String s) {
            int repeatStart = s.indexOf('(') + 1;
            String repeat = s.substring(repeatStart, s.length() - 1);
            String nonRepeat = s.substring(0, repeatStart - 1);
            s = nonRepeat;
            while (s.length() < 15) {
                s += repeat;
            }
            return s;
        }

        private String preProcess(String s) {
            if (! s.contains(".")) s += ".0";
            if (! s.contains("(")) s += "(0)";
            return s;
        }
    }
  ```

