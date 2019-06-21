# Fraction Addition and Subtraction

_update Jul 12, 2017 19:21_

[leetcode](https://leetcode.com/problems/fraction-addition-and-subtraction/#/description)

Given a string representing an expression of fraction addition and subtraction, you need to return the calculation result in string format. The final result should be irreducible fraction. If your final result is an integer, say 2, you need to change it to the format of fraction that has denominator 1. So in this case, 2 should be converted to 2/1.

```text
Example 1:
Input:"-1/2+1/2"
Output: "0/1"
Example 2:
Input:"-1/2+1/2+1/3"
Output: "1/3"
Example 3:
Input:"1/3-1/2"
Output: "-1/6"
Example 4:
Input:"5/3+1/3"
Output: "2/1"
```

**Note:** The input string only contains '0' to '9', '/', '+' and '-'. So does the output. Each fraction \(input and output\) has format ±numerator/denominator. If the first input fraction or the output is positive, then '+' will be omitted. The input only contains valid irreducible fractions, where the numerator and denominator of each fraction will always be in the range \[1,10\]. If the denominator is 1, it means this fraction is actually an integer in a fraction format defined above. The number of given fractions will be in the range \[1,10\]. The numerator and denominator of the final result are guaranteed to be valid and in the range of 32-bit int.

## Basic Idea:

1. 首先涉及到的内容是字符串的split，使用regex解决
2. 接下来需要解决求最大公约数的问题

## java code:

```java
    public class Solution {
        public String fractionAddition(String expression) {
            String[] split = expression.split("(?=[-+])");
            long num1 = 0, den1 = 1;
            for (String fraction : split) {
                long num2 = Integer.parseInt(fraction.split("/")[0]);
                long den2 = Integer.parseInt(fraction.split("/")[1]);
                long gcd = gcd(Math.abs(den1), Math.abs(den2));
                num1 = (num1 * den2 + num2 * den1) / gcd;
                den1 = den1 * den2 / gcd;
                gcd = gcd(Math.abs(den1), Math.abs(num1));
                den1 /= gcd;
                num1 /= gcd;
            }
            return num1 + "/" + den1;
        }
        private long gcd(long a, long b) {
            if (b == 0) return a;
            return gcd(b, a % b);
        }
    }
```

