### KMP Search Algorithm

KMP 的基本思想就是对O\(n^2\) 的brute force的匹配算法进行改进，改进的办法是对pattern字符串进行预处理，生成一个next数组，存放当前位置字符失配之后pattern指针要跳到的位置。

##### 如果我们已经得到了next\[\]数组

我们需要进行匹配，匹配的过程非常简单。维持两个指针**i** , **j**， **i** 对应source字符串，**j **对应pattern。每次失配后 i 不动，移动 j 指向next\[j\] 位置的pattern character， 继续匹配。

##### 下面我们来看如何获得next\[\] 数组





