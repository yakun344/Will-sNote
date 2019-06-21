# Binary Watch \(easy\)

_update Jan 26, 2018 23:15_

[LeetCode](https://leetcode.com/problems/binary-watch/description/) A binary watch has 4 LEDs on the top which represent the hours \(0-11\), and the 6 LEDs on the bottom represent the minutes \(0-59\).

Each LED represents a zero or one, with the least significant bit on the right.

![Watch Picture here. From LeetCode website](https://upload.wikimedia.org/wikipedia/commons/8/8b/Binary_clock_samui_moon.jpg)

For example, the above binary watch reads "3:25".

Given a non-negative integer n which represents the number of LEDs that are currently on, return all possible times the watch could represent.

**Example:**

```text
Input: n = 1
Return: ["1:00", "2:00", "4:00", "8:00", "0:01", "0:02", "0:04", "0:08", "0:16", "0:32"]
```

**Note:**

1. The order of output does not matter.
2. The hour must not contain a leading zero, for example "01:00" is not valid, it should be "1:00".
3. The minute must be consist of two digits and may contain a leading zero, for example "10:2" is not valid, it should be "10:02".

## Basic Idea:

按顺序枚举每个时刻，计数该时刻二进制表示中的 1 的个数，如果和input相等，将其 format 成相应格式的 string 加入 result list 中；

* **Java Code:**

  ```java
    class Solution {
        public List<String> readBinaryWatch(int num) {
            List<String> res = new ArrayList<>();
            for (int hour = 0; hour < 12; ++hour) {
                for (int minute = 0; minute < 60; ++minute) {
                    int bitNum = countBits(hour) + countBits(minute);
                    if (bitNum == num) {
                        res.add(String.format("%d:%02d", hour, minute));
                    }
                }
            }
            return res;
        }

        private int countBits(int n) {
            int ret = 0;
            while (n > 0) {
                ret += n & 1;
                n >>>= 1;
            }
            return ret;
        }
    }
  ```

_update May 8,2018 16:24_

### C++ Code:

注意 c++ 中string format 需要使用字符数组和 sprintf\(\) 配合, 然后可以用 string\(char\[\]\) 直接生成string。

```cpp
    class Solution {
    public:
        vector<string> readBinaryWatch(int num) {
            vector<string> res;
            for (int h = 0; h < 12; ++h) {
                for (int m = 0; m < 60; ++m) {
                    if (countBit(h) + countBit(m) == num) {
                        char buffer[20];
                        sprintf(buffer, "%d:%02d", h, m);
                        res.push_back(string(buffer));
                    }
                }
            }
            return res;
        }

        int countBit(int num) {
            int ret = 0;
            while (num > 0) {
                ret += num & 1;
                num >>= 1;
            }
            return ret;
        }
    };
```

