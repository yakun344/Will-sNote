# Similar RGB Color
_update 2018-05-16 15:35:31_

---
[LeetCode](https://leetcode.com/problems/similar-rgb-color/description/)


In the following, every capital letter represents some hexadecimal digit from 0 to f.

The red-green-blue color `"#AABBCC"` can be written as `"#ABC"` in shorthand.  For example, `"#15c"` is shorthand for the color `"#1155cc"`.

Now, say the similarity between two colors `"#ABCDEF"` and `"#UVWXYZ"` is `-(AB - UV)^2 - (CD - WX)^2 - (EF - YZ)^2`.

Given the color `"#ABCDEF"`, return a 7 character color that is most similar to `#ABCDEF`, and has a shorthand (that is, it can be represented as some `"#XYZ"`.

**Example 1:**

    Input: color = "#09f166"
    Output: "#11ee66"
    Explanation:  
    The similarity is -(0x09 - 0x11)^2 -(0xf1 - 0xee)^2 - (0x66 - 0x66)^2 = -64 -9 -0 = -73.
    This is the highest among any shorthand color.

**Note:**   
* color is a string of length 7.
* color is a valid RGB color: for `i > 0`, `color[i]` is a hexadecimal digit from 0 to f
* Any answer which has the same (highest) similarity as the best answer will be accepted.
* All inputs and outputs should use lowercase letters, and the output is 7 characters.

<br>

### Basic Idea:
将三组十六进制的string分别处理，例如对于 `c5`, 那么候选的就是 `cc, bb, dd` 三个之间找和 `c5` 绝对值差最小的，然后将三组十六进制数都替换好，就完成了。

难点是处理十六进制string，例如对于 `a4`，a 减去一 就组成了 `99`, 对于 `93`，9加一就组成了 `aa`。

* #### C++ Code:
```cpp
    class Solution {
        int parseInt(string s) {
            int ret = 0;
            for (char c : s) {
                if (c <= '9' && c >= '0') ret = ret * 16 + c - '0';
                else if (c <= 'f' && c >= 'a') ret = ret * 16 + c - 'a' + 10;
                else {
                    cout << c << endl;
                    return 100000;
                }
            }
            return ret;
        }
        
        int diff(string c, string c1) {
            int num1 = parseInt(c);
            int num2 = parseInt(c1);
            return abs(num1 - num2);
        }
        
        // "14" -> "22"
        string plusOne(string c) {
            if (c[0] == 'f') return string(2, 'g'); // return an invalid string
            if (c[0] == '9') return string(2, 'a');
            else return string(2, c[0] + 1);
        }
        
        // "25" -> "11"
        string minusOne(string c) {
            if (c[0] == '0') return string(2, 'g'); // invalid string
            if (c[0] == 'a') return string(2, '9'); 
            else return string(2, c[0] - 1);
        }
        
        string helper(string c) {
            if (c[0] == c[1]) return c;
            string c1 = string(2, c[0]);
            string c2 = plusOne(c);
            string c3 = minusOne(c);
            string ret = c1;
            if (diff(c, ret) > diff(c, c2)) ret = c2;
            if (diff(c, ret) > diff(c, c3)) ret = c3;
            return ret;
        }
    public:
        string similarRGB(string color) {
            string c1 = color.substr(1, 2);
            string c2 = color.substr(3, 2);
            string c3 = color.substr(5, 2);
            return "#" + helper(c1) + helper(c2) + helper(c3);
        }
    };
```



