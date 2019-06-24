# Brace Expansion II
_update Jun23, 2019_

---
[LeetCode](https://leetcode.com/problems/brace-expansion-ii/)

Under a grammar given below, strings can represent a set of lowercase words.  Let's use R(expr) to denote the set of words the expression represents.

Grammar can best be understood through simple examples:

Single letters represent a singleton set containing that word.

    R("a") = {"a"}
    R("w") = {"w"}

When we take a comma delimited list of 2 or more expressions, we take the union of possibilities.

    R("{a,b,c}") = {"a","b","c"}
    R("{{a,b},{b,c}}") = {"a","b","c"} (notice the final set only contains each word at most once)

When we concatenate two expressions, we take the set of possible concatenations between two words where the first word comes from the first expression and the second word comes from the second expression.

    R("{a,b}{c,d}") = {"ac","ad","bc","bd"}
    R("{a{b,c}}{{d,e},f{g,h}}") = R("{ab,ac}{dfg,dfh,efg,efh}") = {"abdfg", "abdfh", "abefg", "abefh", "acdfg", "acdfh", "acefg", "acefh"}

Formally, the 3 rules for our grammar:

1. For every lowercase letter x, we have `R(x) = {x}`
2. For expressions `e_1, e_2, ... , e_k with k >= 2`, we have `R({e_1,e_2,...}) = R(e_1) ∪ R(e_2) ∪ ...`
3. For expressions e_1 and e_2, we have `R(e_1 + e_2) = {a + b for (a, b) in R(e_1) × R(e_2)}`, where + denotes concatenation, and × denotes the cartesian product.

Given an expression representing a set of words under the given grammar, return the sorted list of words that the expression represents.


**Example 1:**

    Input: "{a,b}{c{d,e}}"
    Output: ["acd","ace","bcd","bce"]

**Example 2:**

    Input: "{{a,z},a{b,c},{ab,z}}"
    Output: ["a","ab","ac","z"]
    Explanation: Each distinct word is written only once in the final answer.

**Constraints:**
1. `1 <= expression.length <= 50`
2. `expression[i]` consists of `'{', '}', ','` or lowercase English letters.
3. The given expression represents a set of words based on the grammar given in the description.

### Basic Idea
读题花了很长时间，但基本规则就是如果两个部分由“,” 相隔，则并列加入结果中，如果直接相连，则需要做两两相乘但组合。输出的结果需要去重并且排序。

思路是使用递归来处理下级`"{}"`中的表达式，从左到右扫描，根据所遇到但字符进行相应操作。细节很多，需要特别注意。

### Code
* #### Java Code
```java
class Solution {
    public List<String> braceExpansionII(String expression) {
        List<String> res = solve(expression);
        Collections.sort(res);
        return res;
    }
    
    private List<String> solve(String input) {
        Set<String> res = new HashSet<>();
        List<String> lastSolution = null;
        int i = 0;
        while (i <= input.length()) {
            if (i == input.length() || input.charAt(i) == ',') {
                if (lastSolution != null) {
                    res.addAll(lastSolution);
                    lastSolution = null;
                }
            } else if (input.charAt(i) == '{') {
                int right = getRightBraceIndex(input, i);
                List<String> innerSolution = solve(input.substring(i + 1, right));
                if (lastSolution != null) {
                    lastSolution = multiply(lastSolution, innerSolution);
                } else {
                    lastSolution = innerSolution;
                }
                i = right;
            } else {
                // word start
                int right = getEndOfWordIndex(input, i);
                List<String> innerSolution = new ArrayList<>();
                innerSolution.add(input.substring(i, right + 1));
                if (lastSolution != null) {
                    lastSolution = multiply(lastSolution, innerSolution);
                } else {
                    lastSolution = innerSolution;
                }
                i = right;
            }
            i++;
        }
        // for (String s : res) System.out.print(s + " ");
        // System.out.println();
        return new ArrayList<String>(res);
    }
    
    private List<String> multiply(List<String> l1, List<String> l2) {
        Set<String> ret = new HashSet<>();
        for (String a : l1) {
            for (String b : l2) {
                ret.add(a + b);
            }
        }
        return new ArrayList<String>(ret);
    }
    
    private int getEndOfWordIndex(String s, int left) {
        for (int i = left; i <= s.length(); ++i) {
            if (i == s.length()) {
                return i - 1;
            }
            char c = s.charAt(i);
            if (c == ',' || c == '{' || c == '}') {
                return i - 1;
            }
        }
        return -1;
    }
    
    private int getRightBraceIndex(String s, int left) {
        int count = 1;
        for (int i = left + 1; i < s.length(); ++i) {
            if (s.charAt(i) == '{') count++;
            else if (s.charAt(i) == '}') count--;
            if (count == 0) return i;
        }
        return -1;
    }
}
```
