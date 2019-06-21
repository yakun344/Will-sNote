# Count Primes

_update Jul 2, 2017 18:15_

[leetcode](https://leetcode.com/problems/count-primes/#/description)

**Description:**

Count the number of prime numbers less than a non-negative number, n.

## Idea：

Using [Sieve of Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes) to get primes in some given range. 时间复杂度 `O(n log log n)`，一篇论文中证明。

## Java Code：

```java
    // java
    public class Solution {
        public int countPrimes(int n) {
            if (n < 3) return 0;
            boolean[] primes = new boolean[n];
            Arrays.fill(primes, true);
            primes[0] = primes[1] = false;
            for (int i = 2; i <= Math.sqrt(n); ++i) {
                if (! primes[i] || (i > 2 && i % 2 == 0)) continue; // if i is not a prime or i is even, continue
                for (int x = i * i; x < n; x += i) {
                    primes[x] = false;
                }
            }
            int ret = 0;
            for (boolean isPrime : primes) ret += isPrime ? 1 : 0; // count all 'true' in the array
            return ret;
        }
    }
```

_update May 6,2018 15:03_

## C++ Code

相比之前的写法，这种写法会更加简洁明了，如果需要优化，可以将`vector<int>` 换成 `bitset`

```cpp
    class Solution {
    public:
        int countPrimes(int n) {
            vector<int> arr(n);
            int ret = 0;
            for (int i = 2; i < n; ++i) {
                if (arr[i] != 1) {
                    ret++;
                } 
                int t = i;
                while (t < n) {
                    arr[t] = 1;
                    t += i;
                }
            }
            return ret;
        }
    };
```

