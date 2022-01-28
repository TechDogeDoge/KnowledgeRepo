

# 九章算法强化 L5 DP上

[TOC]

## **动态规划的四个组成部分**

**1. 确定状态**
研究最有策略的最后一步
化为子问题

**2. 转移方程**
根据子问题定义直接得到

**3. 初始条件和边界情况**
细心，考虑周全

**4. 计算顺序**
利用之前的计算结果和状态转移方程




# 划分型动态规划
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200524032937954.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
## 例题 91. Decode Ways
A message containing letters from A-Z is being encoded to numbers using the following mapping:

'A' -> 1
'B' -> 2
...
'Z' -> 26
Given a non-empty string containing only digits, determine the total number of ways to decode it.

Example 1:
Input: "12"
Output: 2
Explanation: It could be decoded as "AB" (1 2) or "L" (12).

Example 2:
Input: "226"
Output: 3
Explanation: It could be decoded as "BZ" (2 26), "VF" (22 6), or "BBF" (2 2 6).

## Solution 
采用DP解法，到最后一个char为止，可以解码的个数取决于到前面一个char和前面两个char为止的解码个数。当满足条件时，$res[i] = res[i-1] + res[i-2]$. 这道题目需要讨论的边界条件十分多，首先当 `i = 1`时， `i - 2`不存在，其次需要判断字符串的最后两位看是否可以进行解码，
如 100， 101， 137.....，需要判断最后两位构成的数目是否在 10 到 26 之间。

```java
public int numDecodings(String s) {
    if (s == null || s.length() == 0 || s.charAt(0) == '0') {
        return 0;
    }

    int l = s.length();
    int[] res = new int[l];
    res[0] = 1;
    for (int i = 1; i < l; i++) {
        int one = Integer.parseInt(s.substring(i, i+1));
        int two = Integer.parseInt(s.substring(i-1, i+1));


        if (i == 1) {
            if (one == 0) {
                if (two >= 10 && two <= 26) {
                    res[i] = 1;
                } else {
                    res[i] = 0;
                }
            } else {
                if (two >= 10 && two <= 26) {
                    res[i] = 2;
                } else {
                    res[i] = 1;
                }
            }
        } else {
            if (two >= 10 && two <= 26) {
                res[i] += res[i - 2];
            }
            if (one != 0) {
                res[i] += res[i - 1];
            }
        }

    }
    return res[l-1];
}
```

有一种较为巧妙的写法就是将 $res[i]$的长度增加一，这样不需要单独讨论 `i = 1`的情况。

```java
class Solution {

    public int numDecodings(String s) {
        if(s == null || s.length() == 0) {
            return 0;
        }
        // DP array to store the subproblem results
        int[] dp = new int[s.length() + 1];
        dp[0] = 1;
        // Ways to decode a string of size 1 is 1. Unless the string is '0'.
        // '0' doesn't have a single digit decode.
        dp[1] = s.charAt(0) == '0' ? 0 : 1;

        for(int i = 2; i < dp.length; i += 1) {
            // Check if successful single digit decode is possible.
            if(s.charAt(i-1) != '0') {
               dp[i] += dp[i-1];  
            }

            // Check if successful two digit decode is possible.
            int twoDigit = Integer.valueOf(s.substring(i-2, i));
            if(twoDigit >= 10 && twoDigit <= 26) {
                dp[i] += dp[i-2];
            }
        }
        return dp[s.length()];
    }
}
```
## 例题 639. Decode Ways II
A message containing letters from A-Z is being encoded to numbers using the following mapping way:
'A' -> 1
'B' -> 2
...
'Z' -> 26
Beyond that, now the encoded string can also contain the character '*', which can be treated as one of the numbers from 1 to 9.

Given the encoded message containing digits and the character '*', return the total number of ways to decode it.

Also, since the answer may be very large, you should return the output mod 109 + 7.

Example 1:
Input: "*"
Output: 9
Explanation: The encoded message can be decoded to the string: "A", "B", "C", "D", "E", "F", "G", "H", "I".

Example 2:
Input: "1*"
Output: 9 + 9 = 18
Note:
The length of the input string will fit in range [1, 105].
The input string will only contain the character '*' and digits '0' - '9'.

## Solution
和上面一题的思路完全一致，唯一的区别是需要讨论的情况更多了。
<img src="https://img-blog.csdnimg.cn/20200524052954644.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom:67%;" />
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200524053124278.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)

```java
class Solution {
    public int numDecodings(String s) {
        char[] ch = s.toCharArray();
        int n = ch.length;
        long mod = 1000000000 + 7;
        long[] res = new long[n + 1];
        res[0] = 1;
        for (int i = 1; i <= n; i++) {
            res[i] = res[i - 1] * one(ch[i-1]);
            if (i > 1) {
                res[i] += res[i-2] * two(ch[i-2], ch[i-1]);
            }
            res[i] = res[i] % mod;
        }
        return (int)res[n];
    }
    
    public int one(char ch) {
        if (ch == '0') {
            return 0;
        } else if ('0' < ch && ch <= '9') {
            return 1;
        } else {
            return 9;
        }
    }
    
    public int two(char a, char b) {
        if (a == '0') {
            return 0;
        } else if (a == '1') {
            if (b != '*') {
                return 1;
            } else {
                return 9;
            }
        } else if (a == '2') {
            if ('0' <= b && '6' >= b) {
                return 1;
            } else if (b == '*') {
                return 6;
            } else {
                return 0;
            }
        } else if (a == '*') {
            if ('0' <= b && '6' >= b) {
                return 2;
            } else if (b >= '7' && '9' >= b) {
                return 1;
            } else {
                return 15;
            }
        } else {
            return 0;
        }
    }
}
```

# 博弈动态规划
主要是类似下棋的问题。下棋中存在先手后手的问题。但是在博弈型动态规划问题中，并不区分先手后手，而是考虑当下是谁要下棋，也就是先手。
## 例题 394. Coins in a Line
There are n coins in a line. Two players take turns to take one or two coins from right side until there are no more coins left. The player who take the last coin wins.
Could you please decide the first player will win or lose?
If the first player wins, return true, otherwise return false.

Example 1:
Input: 1
Output: true

Example 2:
Input: 4
Output: true
Explanation:
The first player takes 1 coin at first. Then there are 3 coins left.
Whether the second player takes 1 coin or two, then the first player can take all coin(s) left.

Challenge：
O(n) time and O(1) memory

## Solution
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200524101315540.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020052410120721.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
解法中$res[i]$代表面临剩下`i`个棋子的情况，是否可以必胜，如果必胜true，如果必败false。由于一个人可以拿一个或者两个棋子，于是面对`i`个棋子是否必胜取决于，$i-1$和$i-2$个棋子时，前一个人是否必败(false)。

```java
public boolean firstWillWin(int n) {
    if (n == 0) return false;
    if (n < 3) return true;
    // write your code here
    boolean[] res = new boolean[n+1];

    res[0] = false;
    res[1] = true;
    res[2] = true;

    for (int i = 3; i <= n; i++) {
        res[i] = !res[i-1] || !res[i-2];
    }
    return res[n];
}
```
## 例题 395. Coins in a Line II
There are n coins with different value in a line. Two players take turns to take one or two coins from left side until there are no more coins left. The player who take the coins with the most value wins.
Could you please decide the first player will win or lose?
If the first player wins, return true, otherwise return false.

Example 1:
Input: [1, 2, 2]
Output: true
Explanation: The first player takes 2 coins.

Example 2:
Input: [1, 2, 4]
Output: false
Explanation: Whether the first player takes 1 coin or 2, the second player will gain more value.

## Solution
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200525012659140.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
```java
    public boolean firstWillWin(int[] values) {
        // write your code here
        if (values == null || values.length == 0) return false;
        
        int n = values.length;
        int[] res = new int[n + 1];
        res[n] = 0;
        res[n-1] = values[n-1] - res[n];
        
        for (int i = n - 2; i >= 0; i--) {
            res[i] = Math.max(values[i] - res[i+1], values[i]+values[i+1]-res[i+2]);
        }
        return res[0] >= 0;
    }
```

# 区间型动态规划
1. 求一段区间的最大最小值
2. 转移方程通过区间更新
3. 大区间的值依赖于小区间

## 例题 312. Burst Balloons
Given n balloons, indexed from 0 to n-1. Each balloon is painted with a number on it represented by array nums. You are asked to burst all the balloons. If the you burst balloon i you will get nums[left] * nums[i] * nums[right] coins. Here left and right are adjacent indices of i. After the burst, the left and right then becomes adjacent.
Find the maximum coins you can collect by bursting the balloons wisely.

Note:
You may imagine nums[-1] = nums[n] = 1. They are not real therefore you can not burst them.
0 ≤ n ≤ 500, 0 ≤ nums[i] ≤ 100

Example:
Input: [3,1,5,8]
Output: 167 

Explanation: 
nums = [3,1,5,8] --> [3,5,8] -->   [3,8]   -->  [8]  --> []
coins =  `3*1*5`  + `3*5*8` + `1*3*8` + `1*8*1` = 167

## Solution
不能直接顺着题意做，需要逆向思考。当扎到最后只剩一个气球 $nums[i]$时，此时两边有两个不能被扎且值为1的气球，扎掉这个气球可以得到的分数是$1*nums[i]*1$。此时由于我们规定了$nums[i]$是最后一个扎破的气球，于是$nums[i]$也成为了一个不能扎破的气球，$nums[i]$左边和右边被分成了两个区间，左边区间两端有两个不能扎破的气球，右边区间也是两端有两个不能扎破的气球。于是一个我们得到了一个子问题。

子问题是区间 i,i+1, i+2,..., j 两端有两个不能扎破的气球，求可以得到的最大值，大问题是区间 i,i+1,i+2,...,k两端有两个不能扎破的气球求可以得到的最大值。

定义 $res[i][j]$为扎破 i+1 至 j-1 气球，得到的最大值
转移方程：
$res[i][j] = Max(res[i][k] + res[k][j] + nums[k]*nums[i]*nums[j]), i < k < j$
计算时要注意计算顺序，先计算子问题再计算大问题， 子问题和大问题的区别在于区间的大小，于是计算顺序就是先计算小区间然后计算大区间。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200525020423769.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)

```java
    public int maxCoins(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        int  n = nums.length;
        
        // define the new balloons sequnce
        int[] num = new int[n + 2];
        num[0] = num[n+1] = 1;
        for (int i = 1; i <= n; i++) {
            num[i] = nums[i-1];
        }
        
        // define the result matrix
        int[][] res = new int[n+2][n+2];
        // edge case
        for (int i = 0; i < n + 1; i++) {
            res[i][i+1] = 0;
        }
        
        // traverse the lenth of the interval, start from 3
        for (int len = 3; len <= n + 2; len++) {
            // traverse the interval start point
            for (int i = 0; i <= n - len + 2; i++) {
                // end point
                int j = i + len - 1;
                res[i][j] = 0;
                // find the maximum
                for (int k = i + 1; k < j; k++) {
                    res[i][j] = Math.max(res[i][k] + res[k][j] + num[i] * num[j] * num[k], res[i][j]);
                }
            }
        }
        return res[0][n+1];
    }
```



## 例题 LeetCode 887 Super Egg Drop

You are given `k` identical eggs and you have access to a building with `n` floors labeled from `1` to `n`.

You know that there exists a floor `f` where `0 <= f <= n` such that any egg dropped at a floor **higher** than `f` will **break**, and any egg dropped **at or below** floor `f` will **not break**.

Each move, you may take an unbroken egg and drop it from any floor `x` (where `1 <= x <= n`). If the egg breaks, you can no longer use it. However, if the egg does not break, you may **reuse** it in future moves.

Return *the **minimum number of moves** that you need to determine **with certainty** what the value of* `f` is.

**Example 1:**

```
Input: k = 1, n = 2
Output: 2
Explanation: 
Drop the egg from floor 1. If it breaks, we know that f = 0.
Otherwise, drop the egg from floor 2. If it breaks, we know that f = 1.
If it does not break, then we know f = 2.
Hence, we need at minimum 2 moves to determine with certainty what the value of f is.
```

**Example 2:**

```
Input: k = 2, n = 6
Output: 3
```

**Example 3:**

```
Input: k = 3, n = 14
Output: 4
```

**Constraints:**

- `1 <= k <= 100`
- `1 <= n <= 104`

## solution

状态：当前剩余鸡蛋数，需要测试的楼层个数

选择：到哪个楼层去扔鸡蛋；

​	扔鸡蛋以后又会出现两种状况：鸡蛋碎了以及没有碎

​	没碎的情况：需要测试的楼层从`1`到`n`变为`i+1`到`n`，层数为`n-i`;

​	鸡蛋碎的情况：需要测试的楼层从`1`到`n`变为`1`到`i-1`，层数为`i-1`;

自顶向下递归解法

`dp(k, n)`代表剩余`k`个鸡蛋，面对`n`个楼层，最少扔鸡蛋次数

```java
HashMap<Integer, Integer> memo = new HashMap<>();

public int dp(int k, int n) {
    if (k == 0) {
        return n;
    }
    if (n == 0) {
        return 0;
    }
    if (memo.containsKey(100 * n + k)) {
        return memo.get[100 * n + k];
    }
    int res = Integer.MAX_VALUE;
    for (int i = 1; i < n; i++) {
        res = Math.min(res, Math.max(dp(k, n - i), dp(k - 1, i - 1)) + 1);
    }
    memo.put(100 * n + k, res);
    return res;
}
```



## solution 2

本题还可以使用二分法对`for`循环进行二分搜索优化

`for`循环中，更新值是`dp(k, n - 1)`和`dp(k - 1, i - 1)`中的较大值的最小值

假设，`k`和`n`不变，两个`dp`函数是关于`i`的函数，可以得到下面的图

![image-20211017104719708](C:\Users\jiaoy\AppData\Roaming\Typora\typora-user-images\image-20211017104719708.png)

可以发现我们要求的值就是`dp(k, n - 1)`和`dp(k - 1, i - 1)`恰好相等的值，且两个`dp`函数都具有单调性

```java
HashMap<Integer, Integer> memo = new HashMap<>();

public int dp(int k, int n) {
    if (k == 0) {
        return n;
    }
    if (n == 0) {
        return 0;
    }
    if (memo.containsKey(100 * n + k)) {
        return memo.get[100 * n + k];
    }
    int res = Integer.MAX_VALUE;
    int lo = 1;
    int hi = n;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        int broken = dp(k - 1, i - 1);
        int unbroken = dp(k, n - 1);
        if (broken > unbroken) {
            // 处于函数图像的后半段
            hi = mid - 1;
            res = Math.min(res, broken + 1);
        } else {
            lo = mid + 1;
            res = Math.min(res, unbroken + 1);
        }
    }
    memo.put(100 * n + k, res);
    return res;
}
```

## solution 3

还有一种解法就是改变一下状态方程的含义以及状态转移方程

`dp[k][m]`代表剩余`k`个鸡蛋，扔`m`次鸡蛋，可以测试的楼层数

所以问题就转化成求`d[k][m] = n`时的`m`的值

状态转移方程：

`dp[k][m] = dp[k-1][m-1] + dp[k][m-1] + 1`

初始状态：

`dp[0][..] = 0`

`dp[..][0] = 0`

> 虽然由于最终要求的值在二维数组的`index`中，无法确定二维数组的大小，但是可以确定的是对于一个`n`层楼，最大扔鸡蛋次数不会超过`n`，因为采用从一楼逐层扔鸡蛋的方法，最坏情况下需要`n`次扔鸡蛋，所以可以将二维数组的大小定义为`k+1`h和`n+1`

```java
int solution(int k, int n) {
    int[][] dp = new int[k + 1][n + 1];
    int m = 0;
    while (dp[k][m] < n) {
        m++;
        for (int i = 1; i <= k; i++) {
            dp[i][m] = dp[i-1][m-1] + dp[i][m-1] + 1;
        }
    }
    return m;
}
```





# 区间博弈型

## 例题 Coins in a Line lll
在 Coins in a Line ll 的基础上添加了可以从队列的两边拿取的规定，不再局限于从前面取。

## Solution
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200525023228968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
```java
    public boolean firstWillWin(int[] values) {
        if (values == null || values.length == 0) return false;
        
        int  n = values.length;
        
        int[][] res = new int[n][n];
        
        for (int i = 0; i < n; i++) {
            res[i][i] = values[i];
        }
        
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i <= n - len ; i++) {
                int j = len + i - 1;
                f[i][j] = Math.max(values[i] - res[i+1][j], values[j] - res[i][j - 1]);
            }
        }
        return res[0][n-1] >= 0;
    }
```