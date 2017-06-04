### KMP Search Algorithm

---

KMP 的基本思想就是对O\(n^2\) 的brute force的匹配算法进行改进，改进的办法是对pattern字符串进行预处理，生成一个next数组，存放当前位置字符失配之后pattern指针要跳到的位置。

##### 如果我们已经得到了next\[\]数组

我们需要进行匹配，匹配的过程非常简单。维持两个指针**i** , **j**， **i** 对应source字符串，**j **对应pattern。每次失配后 i 不动，移动 j 指向next\[j\] 位置的pattern character， 继续匹配。

##### 下面我们来看如何获得next\[\] 数组

Basic idea 是这个**next\[\] **数组的本质是当前位置之前段 **pattern**有一定长度相同前缀和后缀，而数组中所存储的就是这个长度。于是我们从这里出发，可以** set next\[0\] to 0**, 然后令 **i = 0, j = 1 **， 然后用ij右移进行匹配。详情见下方的code。

```java
    public static void main(String[] args) {
        Character[] p = "abcgabcfabcgabcg".chars().mapToObj(i -> (char)i)
            .toArray(i -> new Character[i]);
        int[] next = new int[p.length];
        next[0] = 0;
        int i = 0, j = 1;
        while (j < next.length) {
            if (p[i] == p[j]) {
                next[j++] = ++i;
            } else {
                if (i == 0) next[j++] = 0; // 如果最左边的都不匹配，则只把j+1继续匹配。
                else i = next[i - 1]; // 如果在中段失配，则尝试更短的同样前缀去匹配，关键步骤，解释见下面。
            }
        }
        Arrays.stream(next).forEach(System.out::println);
    }
```

```java
    // output : 0000123012345674
```

上面的code用了一个比较有说服力的 example：

| a | b | c | g | a | b | c | f | a | b | c | g | a | b | c | g |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | 0 | 0 | 0 | 1 | 2 | 3 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 4 |

注意最右边g对应的4. 当 **i **在 **next\[7\] **失配之后，我们让** i = next\[i - 1\] **即 **i = next\[6\] = 3** , 继续让 **i **和 **j **比较，发现 **p\[i\] == p\[j\] \(p\[3\] = p\[15\]\), **所以 **next\[j\] = ++i == 3 + 1 = 4 . **需要注意的是，这里的 **i = next\[i - 1\] **实际上说的是上一个更短匹配前缀的下一个需要匹配的位置。（如果理解不了，就写出这个例子，一定可以理解。）

