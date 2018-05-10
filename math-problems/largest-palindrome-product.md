## Largest Palindrome Product
_update May,9 2018_

---
[LeetCode](https://leetcode.com/problems/largest-palindrome-product/description/)

Find the largest palindrome made from the product of two n-digit numbers.

Since the result could be very large, you should return the largest palindrome mod 1337.

**Example:**
    
    Input: 2
    
    Output: 987
    
    Explanation: 99 x 91 = 9009, 9009 % 1337 = 987

**Note:**

    The range of n is [1,8].
    
<br>

### Basic Idea:
Brute force 解法，穷举可能的回文数乘积candidate，对每个可能的成绩判断能否由 N 位数相乘获得。时间复杂度最坏情况为 `O(10^2n)`, `O(10^n)` for generate all candidate products, `O(10^n)` for validation for each product candidate.

* #### C++ Code:
```cpp
    typedef long long ll;
    
    class Solution {
        // 检验从最小n位数_min开始，到最大的_max为止，看能否乘出prod
        bool isValid(int _min, int _max, ll prod, ll maxProd) {
            if (prod > maxProd) return false;
            for (int i = _max; i >= sqrt(prod); --i) {
                if (prod % i == 0 && prod / i <= _max && prod / i >= _min) {
                    return true; 
                }
            }
            return false;
        }
        
        // 生成回文数，例如 input=123, output=123 321
        ll generatePalindrome(int num) {
            ll t = num, ret = num;
            while (t > 0) {
                ret = ret * 10 + t % 10;
                t /= 10;
            }
            return ret;
        }
    public:
        int largestPalindrome(int n) {
            if (n == 1) return 9;
            int _min = pow(10, n - 1);
            int _max = pow(10, n) - 1;
            ll maxProd = (ll)_max * (ll)_max;
            for (int i = _max; i >= 1; --i) {
                ll prod = generatePalindrome(i);
                if (isValid(_min, _max, prod, maxProd)) {
                    return prod % 1337;
                }
            }
            return 0;
        }
    };
```