# 九章算法强化 L6 DP下-双序列和背包

[TOC]



#  双序列

##  例题 1143. Longest Common Subsequence
Given two strings text1 and text2, return the length of their longest common subsequence.
A subsequence of a string is a new string generated from the original string with some characters(can be none) deleted without changing the relative order of the remaining characters. (eg, "ace" is a subsequence of "abcde" while "aec" is not). A common subsequence of two strings is a subsequence that is common to both strings.
If there is no common subsequence, return 0.

Example 1:
Input: text1 = "abcde", text2 = "ace" 
Output: 3  
Explanation: The longest common subsequence is "ace" and its length is 3.

Example 2:
Input: text1 = "abc", text2 = "abc"
Output: 3
Explanation: The longest common subsequence is "abc" and its length is 3.

Example 3:
Input: text1 = "abc", text2 = "def"
Output: 0
Explanation: There is no such common subsequence, so the result is 0.

Constraints:
1. 1 <= text1.length <= 1000
2. 1 <= text2.length <= 1000
3. The input strings consist of lowercase English characters only.

##  Solution
同样是分析最后一步
以 a = "jiuzhang" b = "lijiang" 为例，假设 a 长度为 m，b 长度为 n, 对于最后一个字符有三种情况：
1. a 的最后一位不参与最长子序列
2. b 的最后一位不参与最长子序列
3. a, b 的最后一位都参与最长子序列
第三种情况中，由于子序列的顺序，所以a, b 的最后一位如果都参与了，两个必定配对起来，因为不可能存配对的交叉。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526032404691.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
```java
    public int longestCommonSubsequence(String text1, String text2) {
        if (text1 == null || text1.length() == 0) return 0;
        if (text2 == null || text2.length() == 0) return 0;
        char[] a = text1.toCharArray();
        char[] b = text2.toCharArray();
        
        int m = a.length;
        int n = b.length;
        int[][] res = new int[m+1][n+1];
        
        for (int i = 0; i < m + 1; i++) {
            for (int j = 0; j < n + 1; j++) {
                if (i == 0 || j == 0) {
                    res[i][j] = 0;
                    continue;
                }
                
                res[i][j] = Math.max(res[i-1][j], res[i][j-1]);
                
                if (a[i - 1] == b[j - 1]) {
                    res[i][j] = Math.max(res[i][j],1+res[i-1][j-1]);
                }
            }
        }
        return res[m][n];
    }
```

## 例题 72. Edit Distance
Given two words word1 and word2, find the minimum number of operations required to convert word1 to word2.

You have the following 3 operations permitted on a word:
Insert a character
Delete a character
Replace a character

Example 1:
Input: word1 = "horse", word2 = "ros"
Output: 3
Explanation: 
horse -> rorse (replace 'h' with 'r')
rorse -> rose (remove 'r')
rose -> ros (remove 'e')

Example 2:
Input: word1 = "intention", word2 = "execution"
Output: 5
Explanation: 
intention -> inention (remove 't')
inention -> enention (replace 'i' with 'e')
enention -> exention (replace 'n' with 'x')
exention -> exection (replace 'n' with 'c')
exection -> execution (insert 'u')

##  Solution
两个单词分别为两个序列 a 和 b，从 a 向 b 匹配。同样从最后一步分析，此时全部都匹配了，除去最后一个字符，使最后一个字符匹配有三种情况
1. 增加一个字符，问题变成匹配 a[m] 和 b[n-1]
2. 替换一个字符，问题变成匹配 a[m-1] 和 b[n-1]
3. 删除一个字符，问题变成匹配 a[m-1] 和 b[n]
还有一种情况就是 a 和 b 的末尾字符本来就相等，此时不需要任何编辑
<img src="https://img-blog.csdnimg.cn/2020052603332042.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom: 80%;" />

```java
    public int minDistance(String word1, String word2) {
        if (word1 == null || word1.length() == 0) return word2.length(); 
        if (word2 == null || word2.length() == 0) return word1.length(); 
        
        int m = word1.length();
        int n = word2.length();
        
        int[][] res = new int[m+1][n+1];
        for (int i = 0; i < m+1; i++) {
            for (int j = 0; j < n+1; j++) {
                if (i == 0) {
                    res[i][j] = j;
                    continue;
                }
                if (j == 0) {
                    res[i][j] = i;
                    continue;
                }
                
                res[i][j] = Math.min(res[i-1][j], res[i][j-1]) + 1;
                res[i][j] = Math.min(res[i-1][j-1] + 1, res[i][j]);
                
                if (word1.charAt(i-1) == word2.charAt(j-1)) {
                    res[i][j] = Math.min(res[i-1][j-1], res[i][j]);
                }
            }
        }
        return res[m][n];
    }
```

如果想要得到编辑的路径，可以新定义一个数据类型Node，存储编辑距离和最后一步操作，原本代码中的 int 二维数组转换为 Node 二维数组。得到最小编辑距离后，从res\[m\]\[n\]结果向前推，因为res\[m\]\[n\]记录了最后一步操作，而不同操作对应不同的前一步状态

<img src="C:\Users\jiaoy\AppData\Roaming\Typora\typora-user-images\image-20211016175629236.png" alt="image-20211016175629236" style="zoom:80%;" />

## 例题 97. Interleaving String

Given s1, s2, s3, find whether s3 is formed by the interleaving of s1 and s2.

Example 1:
Input: s1 = "aabcc", s2 = "dbbca", s3 = "aadbbcbcac"
Output: true

Example 2:
Input: s1 = "aabcc", s2 = "dbbca", s3 = "aadbbbaccc"
Output: false

## Solution
和前面思路完全一致，从最后一步向前推。最后一个字符有两种匹配可能，和 a 的最后一个字符或者 b 的最后一个字符匹配。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020052604593437.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
```java
    public boolean isInterleave(String s1, String s2, String s3) {
        if (s1.length() + s2.length() != s3.length()) return false;
        
        int m = s1.length();
        int n = s2.length();
        boolean[][] res = new boolean[m+1][n+1];
        
        for (int i = 0; i < m+1; i++) {
            for (int j = 0; j < n+1; j++) {
                if (i == 0 && j == 0) {
                    res[i][j] = true;
                }
                
                if (i > 0 && s1.charAt(i-1) == s3.charAt(i+j-1)) {
                    res[i][j] = res[i-1][j];
                    if (res[i][j]) continue;
                }
                if (j > 0 && s2.charAt(j-1) == s3.charAt(i+j-1)) {
                    res[i][j] = res[i][j-1];
                    if (res[i][j]) continue;
                }
            }
        }
        return res[m][n];
    }
```

## 例题 623 k edit string
给定目标单词，以及一个包含多个单词的字典，求字典中所有edit distance不大于 k 的单词。

## Solution
最简单的方法，对每一个单词和目标单词对计算 edit distance，添加所有不大于 k 的单词到结果中
但是这样计算的时候会有重复，当字典中的单词有相同的前缀的时候会出现重复计算，所以为了简化可以采用字典树



## 例题 LeetCode 516 Longest Palindromic Subsequence

Given a string `s`, find *the longest palindromic **subsequence**'s length in* `s`.

A **subsequence** is a sequence that can be derived from another sequence by deleting some or no elements without changing the order of the remaining elements.

**Example 1:**

```
Input: s = "bbbab"
Output: 4
Explanation: One possible longest palindromic subsequence is "bbbb".
```

**Example 2:**

```
Input: s = "cbbd"
Output: 2
Explanation: One possible longest palindromic subsequence is "bb".
```

**Constraints:**

- `1 <= s.length <= 1000`
- `s` consists only of lowercase English letters.

## solution

定义一个DP二维数组`dp[i][j]`代表子字符串`s[i..j]`的最大回文子序列长度

假设已知`dp[i+1][j-1]`的结果，如何求`dp[i][j]`的结果？

按照`s[i]`和`s[j]`是否相等分为两种情况：

 1. `s[i] == s[j]`

    `dp[i][j] = dp[i+1][j-1] + 2 `

 2.  `s[i] != s[j]`

    由于字符不相等，所以两个字符中至少有一个不在最大回文子序列中

    `dp[i][j] = max(dp[i+1][j], dp[i][j-1]) `

```java
int longestPalindromeSubseq(char[] s) {
    int n = s.length;
    // dp 数组全部初始化为 0\
    int[][] dp = new int[n][n];
    for (int i = 0; i < n; i++) {
        dp[i][i] = 1;
    }
    for (int diff = 1; diff < n; diff++) {
        for (int start = 0; start < n - 1 - diff; start++) {
            if (s[start] == s[start + diff]) {
                dp[start][start + diff] = dp[start+1][start+diff-1] + 2;
            } else {
                int temp = Math.max(dp[start+1][start+diff], dp[start][start+diff-1]);
                dp[start][start + diff] = temp;
            }
        }
    }
    return dp[0][n - 1];
}
```



# 背包型

## 例题 92 Backpack
Given n items with size A[i], an integer m denotes the size of a backpack. How full you can fill this backpack?
Example
Example 1:
	Input:  [3,4,8,5], backpack size=10
	Output:  9

Example 2:
	Input:  [2,3,5,7], backpack size=12
	Output:  12
	
Challenge

1. O(n x m) time and O(m) memory.
2. O(n x m) memory is also acceptable if you do not know how to optimize memory.

## Solution
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020052704115432.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200527041134464.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)

```java
    public int backPack(int m, int[] A) {
        // write your code here
        if (A == null || A.length == 0) return 0;
        
        boolean[][] res = new boolean[A.length + 1][m + 1];
        for (int i = 0; i < A.length + 1; i++) {
            for (int j = 0; j < m + 1; j++) {
                if (i == 0 && j == 0) {
                    res[i][j] = true;
                    continue;
                }
                
                if (i == 0) {
                    res[i][j] = false;
                    continue;
                }
                
                res[i][j] = res[i-1][j];
                if (A[i-1] <= j) {
                    res[i][j] = res[i][j] || res[i-1][j - A[i-1]]; 
                }
            }
        }
        for (int k = m; k >= 0; k--) {
            if (res[A.length][k]) {
                return k;
            }
        }
        return 0;
    }
```

## 变种例题
给一个数组，将这个数组中的数尽量平分，使得和的差最小。假设数组中所有数的和是 M，这题就相当求不大于 M/2 的最大和，和背包问题一样

## Solution

```java

```

## 例题 Backpack ll
There are n items and a backpack with size m. Given array A representing the size of each item and array V representing the value of each item.

What's the maximum value can you put into the backpack?

Example 1:
Input: m = 10, A = [2, 3, 5, 7], V = [1, 5, 2, 4]
Output: 9
Explanation: Put A[1] and A[3] into backpack, getting the maximum value V[1] + V[3] = 9 

Example 2:
Input: m = 10, A = [2, 3, 8], V = [2, 5, 8]
Output: 10
Explanation: Put A[0] and A[2] into backpack, getting the maximum value V[0] + V[2] = 10 
Challenge
O(nm) memory is acceptable, can you do it in O(m) memory?

## Solution
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200527104218721.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200527113941823.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)

```java
    public int backPackII(int m, int[] A, int[] V) {
        // write your code here
        if (A == null || A.length == 0) return 0;
        
        int[][] res = new int[A.length + 1][m + 1];
        for (int i = 0; i < A.length + 1; i++) {
            for (int j = 0; j < m + 1; j++) {
                if (i == 0 && j == 0) {
                    res[i][j] = 0;
                    continue;
                }
                
                if (i == 0) {
                    res[i][j] = -1;
                    continue;
                }
                
                res[i][j] = res[i-1][j];
                if (A[i-1] <= j && res[i-1][j-A[i-1]] != -1) {
                    res[i][j] = Math.max(res[i][j], res[i-1][j - A[i-1]] + V[i - 1]); 
                }
            }
        }
        
        int temp = 0;
        for (int k = m; k >= 0; k--) {
            temp = Math.max(temp, res[A.length][k]);
        }
        return temp;
    }
```

## 例题 Backpack lll
其他条件和问题与Backpack ll一样，区别在于，每种物品都有无穷多个，背包中可以放入任意数量的相同物品。

## Solution
问题的主要变化在于转移方程的变化，在前一个问题，对于一个物品有两种情况，加到背包和不加到背包中，现在变成了有多种情况：不加到背包中，加一个到背包中，加两个到背包中...
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200527114208928.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
对于转移方程 f[i][w] = max(f[i-1][w-kA~i-1~] + kV~i-1~)，表面上来看需要遍历所有的 k 值来找到最大的值，但是分析以后就会发现计算过程中有冗余

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200527120838628.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020052712091916.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
```java
    public int backPackIII(int m, int[] A, int[] V) {
        // write your code here
        if (A == null || A.length == 0) return 0;
        
        int[][] res = new int[A.length + 1][m + 1];
        for (int i = 0; i < A.length + 1; i++) {
            for (int j = 0; j < m + 1; j++) {
                if (i == 0 && j == 0) {
                    res[i][j] = 0;
                    continue;
                }
                
                if (i == 0) {
                    res[i][j] = -1;
                    continue;
                }
                
                res[i][j] = res[i-1][j];
                if (A[i-1] <= j && res[i][j-A[i-1]] != -1) {
                    res[i][j] = Math.max(res[i][j], res[i][j - A[i-1]] + V[i - 1]); 
                }
            }
        }
        
        int temp = 0;
        for (int k = m; k >= 0; k--) {
            temp = Math.max(temp, res[A.length][k]);
        }
        return temp;
    }
```
这题还可以优化空间复杂度，采用滚动数组写法，只需要两行，再优化可以只采用一行。


## 例题 K sum
给定数组 A，包含 n 个互不相等的正整数，问有多少种方式从中找出 K 个数，使得他们的和是Target

## Solution
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200528012234387.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
```java

```



## 例题 LeetCode 416 Partition Equal Subset Sum

Given a **non-empty** array `nums` containing **only positive integers**, find if the array can be partitioned into two subsets such that the sum of elements in both subsets is equal.

**Example 1:**

```
Input: nums = [1,5,11,5]
Output: true
Explanation: The array can be partitioned as [1, 5, 5] and [11].
```

**Example 2:**

```
Input: nums = [1,2,3,5]
Output: false
Explanation: The array cannot be partitioned into equal sum subsets.
```

**Constraints:**

- `1 <= nums.length <= 200`
- `1 <= nums[i] <= 100`



### solution 

问题可以转化为给定一个可装载重量为 sum/2 的背包和 n 个物品，是否存在一种装法恰好将背包装满

d\[i\]\[j\]代表对于前 i 个物品，背包容量为 j 时，是否能刚好装满

