# 九章算法基础 L9 Dynamic Programming

##  摘要
1. 理解动态规划
1.1递归与动态规划的联系和区别
1.2 记忆化搜索
2. 什么时候使用动态规划
2.1 适用动态规划的三个条件
2.2 不适用动态规划的三个条件
3.  动态规划四要素
3.1 vs. 递归三要素
4. 面试中常见动态规划的分类
5. 坐标（矩阵）动态规划
6. 接龙型动态规划

### 动态规划解题思路

1. 明确状态
2. 定义DP数组或DP函数的含义
3. 明确分支选择，即前一状态经过哪些分支转移成当前状态
4. 确定初始状态或base case

> 自顶向下
>
> 将一个大问题逐渐分解成一个个小问题，直到分解到base case，而大问题的解答由多个小问题的组合而成；
>
> **这是递归采用的方法；**

> 自底向上
>
> 从base case开始，逐渐将小问题聚合成最终的大问题，而每一个大问题的解答都由小问题的解答组合而成；
>
> **这是DP采用的方法**；

最优子结构：可以从子问题的最优结果推出大问题的最优结果

==动态规划解法要求问题具有最优子结构，且一般用于求解最值==


##  例题 120. Triangle
Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.

For example, given the following triangle
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
The minimum path sum from top to bottom is 11 (i.e., 2 + 3 + 5 + 1 = 11).

Note:
Bonus point if you are able to do this using only O(n) extra space, where n is the total number of rows in the triangle.

### solution 1

问题是一个最短路问题，所以可以采用DFS的思路进行 Traverse。遍历所以的路线，但是由于 Triangle 中的点有交集，所以实际上有很多冗余，时间复杂度也较高
定义一个 function 用来 traverse从起点到点(x, y)。
<img src="https://img-blog.csdnimg.cn/20200216105314239.JPG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom:50%;" />

### solution 2

和上面的解法一致，同样没有避免冗余，但是采用 divide conquer 从下往上
定义一个 function 用来返回从一个点到最下面的最短距离
<img src="https://img-blog.csdnimg.cn/20200216110248610.JPG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom:50%;" />

### solution 3

同样采用DFS，但是区别在于会创建一个数组用来记录之前计算得到的结果，这样后面不需要重复计算。
定义一个 function 用来返回从一个点到最下面的最短距离，定义一个二维数组用来存储各个点到最低层的最短距离
<img src="https://img-blog.csdnimg.cn/20200216111421502.JPG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom:50%;" />

记忆化的搜索就是一种 Dynamic Programming， 由于记忆化的特性，避免了冗余计算。

<img src="https://img-blog.csdnimg.cn/2020021611211819.JPG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom:50%;" />

### solution 4

多重循环写法的 DP
<img src="https://img-blog.csdnimg.cn/2020021611251624.JPG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom:50%;" />

自底向上
```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
    	// dist 储存从(i,j)到 最下一层的最短距离
        ArrayList<ArrayList<Integer>> dist = new ArrayList(triangle);
        // bottom-up, not in-place
        for (int i = dist.size() - 2; i >= 0; i--) {
            for (int j = 0 ; j <= i; j++) {
            	int min =Math.min(dist.get(i+1).get(j),dist.get(i+1).get(j+1))+triangle.get(i).get(j);
                dist.get(i).set(j, min);
            }
        }
        return dist.get(0).get(0);
    }
}
```

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        // bottom-up, in-place
        for (int i = triangle.size() - 2; i >= 0; i--) {
            for (int j = 0; j <= i; j++) {
            	int min = Math.min(triangle.get(i+1).get(j),triangle.get(i+1).get(j+1))+triangle.get(i).get(j)
                triangle.get(i).set(j, min);
            }
        }
        return triangle.get(0).get(0);
    }
}
```

自顶向下
```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
    	//top-down, in-place
        for (int i = 1; i < triangle.size(); i++) 
            for (int j = 0; j < triangle.get(i).size(); j++) {
                if (j == 0) 
                    triangle.get(i).set(j, triangle.get(i-1).get(j) + triangle.get(i).get(j));
                else if (j == triangle.get(i).size()-1)
                    triangle.get(i).set(j, triangle.get(i-1).get(j-1) + triangle.get(i).get(j));
                else
                    triangle.get(i).set(j, Math.min(triangle.get(i-1).get(j-1), triangle.get(i-1).get(j)) 
                    + triangle.get(i).get(j));
            }
        return Collections.min(triangle.get(triangle.size()-1));
    }
}
```

<img src="https://img-blog.csdnimg.cn/20200216121338590.JPG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom:50%;" />

##  DP 使用情况
1. 使用 DP 的可能三个条件
1.1 求最大值最小值
1.2 判断是否可行
1.3 统计方案个数

2. 不使用 DP 的条件
2.1 求出所有具体的**方案**而非方案个数
2.2 输入数据是一个**集合**而不是**序列**
2.3 暴力算法的复杂度已经是**多项式**级别：动态规划擅长将指数级别复杂度优化到多项式级别

<img src="https://img-blog.csdnimg.cn/20200216122834479.JPG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom:50%;" />

<img src="https://img-blog.csdnimg.cn/20200216123028330.JPG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom:80%;" />

##  动态规划常见类型
坐标型 10%
接龙型 20%
划分型 20%
匹配型 20%
背包型 20%
区间型 10%

## 坐标型DP
state:
f()x) 表示从起起点走到坐标 x
f(x)(y)表示从起点走到坐标 x, y

function: 研究走到 x, y 这个点之前的一部
initialize： 起点
answer：终点

## 例题 64. Minimum Path Sum

Given a m x n grid filled with non-negative numbers, find a path from top left to bottom right which minimizes the sum of all numbers along its path.

Note: You can only move either down or right at any point in time.
Example:
Input:
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
Output: 7
Explanation: Because the path 1→3→1→1→1 minimizes the sum.

###  solution 1:
**Top-down**。问题本身就是一个二维坐标型问题，所以采用in-place的方法来计算最短距离。由于是从左上移动到右下，所以移动方向只有两个， 向右和向下。
由此来推算上一步的位置，以及两者的转换关系。需要注意的是第一行和第一列和其他点的情况有一些不太一样，需要单独计算
```java
class Solution {
    public int minPathSum(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return 0;
        }
        // first column
        for (int i = 1; i < grid.length; i++) {
            grid[i][0] += grid[i-1][0]; 
        }
        // first row
        for (int i = 1; i < grid[0].length; i++) {
            grid[0][i] += grid[0][i-1];
        }
        
        for (int i = 1; i < grid.length; i++) {
            for (int j = 1; j < grid[0].length; j++) {
                grid[i][j] += Math.min(grid[i-1][j], grid[i][j-1]);
            }
        }
        return grid[grid.length-1][grid[0].length-1];
    }
}
```

这种解法可以进一步进行优化，由于到一个点的最短距离只取决于它左边和上边的点，所以实际上不需要存储所有的最短距离值，只需要存储两行的最短距离，当前行和上面一行，采用滚动数组的写法可以节省空间。

```java
    public int minPathSum(int[][] grid) {
        if (grid == null || grid.length == 0) return 0;
        int m = grid.length;
        int n = grid[0].length;
        int[][] dist = new int[2][n];
        dist[0][0] = grid[0][0];
        
        
        int current = 0;
        int prev = 0;
        for (int i = 0; i < m; i++) {
            current = 1 - current;
            prev = 1 - current;
            for (int j = 0; j < n; j++) {
                if (i == 0 && j == 0) {
                    dist[current][j] = grid[0][0];
                    continue;
                }
                dist[current][j] = Integer.MAX_VALUE;
                
                if (i > 0) {
                    dist[current][j] = Math.min(dist[prev][j] + grid[i][j], dist[current][j]);
                }
                if (j > 0) {
                    dist[current][j] = Math.min(dist[current][j - 1] + grid[i][j], dist[current][j]);
                }
            }
        }
        return dist[current][n - 1];
    }
```

实际测试时发现，空间复杂度确实降低，但运行时间却增加了，很可能是因为 if 判断减慢了运算时间。



## 例题 62. Unique Paths

A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).
The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).
How many possible unique paths are there?
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200217002010719.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
Note: m and n will be at most 100.
Example 1:
Input: m = 3, n = 2
Output: 3
Explanation:
From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:
1. Right -> Right -> Down
2. Right -> Down -> Right
3. Down -> Right -> Right

###  solution 
```java
class Solution {
    public int uniquePaths(int m, int n) {
        if (m < 1 || n < 1) {
            return 0;
        }
        int[][] res = new int[m][n];
        
        for (int i = 0; i < m; i++) {
            res[i][0] = 1;
        }
        
        for (int i = 0; i < n; i++) {
            res[0][i] = 1;
        }
        
        
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                res[i][j] = res[i][j-1] + res[i-1][j];
            }
        }
        return res[m-1][n-1];
    }
}
```

## 例题 70. Climbing Stairs

You are climbing a stair case. It takes n steps to reach to the top.
Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?
Note: Given n will be a positive integer.

Example :
Input: 2
Output: 2
Explanation: There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps

###  solution 
```java
class Solution {
    public int climbStairs(int n) {
        if (n < 1) return 0;
        if (n == 1) return 1;
        
        int[] res = new int[n+1];
        res[0] = 1;
        res[1] = 1;
        for (int i = 2; i < n+1; i ++) {
            res[i] = res[i-1] + res[i-2];
        }
        return res[n];
    }
}
```

## 例题 55. Jump Game

Given an array of non-negative integers, you are initially positioned at the first index of the array.
Each element in the array represents your maximum jump length at that position.
Determine if you are able to reach the last index.

Example 1:
Input: [2,3,1,1,4]
Output: true
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.

###  solution
定义数组 canReach 表示点 x 是否可以到最后一个点
```java
class Solution {
        public boolean canJump(int[] nums) {
            if (nums == null || nums.length == 0) {
                return false;
            }
            
            int[] canReach = new int[nums.length];
            canReach[nums.length - 1] = 1;
            
            for (int i = nums.length - 2; i >= 0; i--) {
                int jump = i + nums[i];
                for (int j = i + 1; j <= jump; j++) {
                    if (canReach[j] == 1) {
                        canReach[i] = 1;
                        break;
                    }
                }
            }
            return canReach[0] == 1;
        }
}
```

### solution 2 Greedy
采用贪心算法，但是需要证明
从右向左循环，lastPos 代表了最靠左的 GOOD 点（可以到达终点的点）。只要当前点最远到达的位置大于 lastPos 就说明当前点可以到终点。
```java
public class Solution {
    public boolean canJump(int[] nums) {
        int lastPos = nums.length - 1;
        for (int i = nums.length - 1; i >= 0; i--) {
            if (i + nums[i] >= lastPos) {
                lastPos = i;
            }
        }
        return lastPos == 0;
    }
}
```

## 例题 45. Jump Game II

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Your goal is to reach the last index in the minimum number of jumps.

Example:
Input: [2,3,1,1,4]
Output: 2
Explanation: The minimum number of jumps to reach the last index is 2. Jump 1 step from index 0 to 1, then 3 steps to the last index.

Note: You can assume that you can always reach the last index.

###  solution
和前面一题的思路一致，steps[i]代表从index为 i 的位置调到最后需要的最少步数
```java
class Solution {
    public int jump(int[] nums) {
        int len = nums.length;
        int[] steps = new int[len];
        steps[len - 1] = 0;
        for (int i = len - 2; i >= 0; i--) {
            steps[i] = Integer.MAX_VALUE;
            for (int j = i + 1; j <= i + nums[i] && j < len; j++) {
            	// 当调到的位置不能到达终点，跳过这个位置
                if (steps[j] == Integer.MAX_VALUE) continue;
                steps[i] = Math.min(steps[i], steps[j] + 1);
            }
        }
        return steps[0];
    }
}
```


##  接龙型 DP
###  例题 300. Longest Increasing Subsequence
Given an unsorted array of integers, find the length of longest increasing subsequence.

Example:
Input: [10,9,2,5,3,7,101,18]
Output: 4 
Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4. 

Note:
There may be more than one LIS combination, it is only necessary for you to return the length.
Your algorithm should run in O(n2) complexity.
Follow up: Could you improve it to O(n log n) time complexity?

###  solution 
定义一个数组用来存储数据。 `res[x]`表示到坐标 `x` 为止，且**以坐标 `x` 为上升子序列的最后一个元素**的最大长度。定义 `res` 一定不能定义到坐标 `x` 为止的最大长度，因为这样很难判断坐标 `x-1` 和坐标 `x` 的转换关系。同时需要注意**并不是 `x` 越大，长度越大**。

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        
        int[] res = new int[nums.length];
        for (int i = 0; i < nums.length; i++) {
            res[i] = 1;
        }
        
        int length = 1;
        
        for (int i = 1; i < nums.length; i++) {
            for (int j = i - 1; j >= 0; j--) {
                if (nums[j] < nums[i]) {
                    res[i] = Math.max(res[i], res[j] + 1);
                }
            }
            length = Math.max(length, res[i]);
        }
        return length;
    }
}
```

###  例题 368. Largest Divisible Subset
Given a set of distinct positive integers, find the largest subset such that every pair (Si, Sj) of elements in this subset satisfies:

S~i~ % S~j~ = 0 or S~j~ % S~i~ = 0.

If there are multiple solutions, return any subset is fine.

Example 1:
Input: [1,2,3]
Output: [1,2] (of course, [1,3] will also be ok)

Example 2:
Input: [1,2,4,8]
Output: [1,2,4,8]

###  solution 
和上面题目的思路一致，但是问题在于返回值并不是这个 `subset` 的大小，而是 `subset` 的值，于是就需要我们添加一个额外的数组用来记录它整除的前一个数的坐标位置 `pre`，同时还要记录最大 `subset` 最后一个点的坐标
```java
class Solution {
    public List<Integer> largestDivisibleSubset(int[] nums) {
        List<Integer> res = new ArrayList<>();
        if (nums == null || nums.length == 0) {
            return res;
        }
        Arrays.sort(nums);
        int[] memo = new int[nums.length];
        int[] pre = new int[nums.length];
        for (int i = 0; i < nums.length; i++) {
            memo[i] = 1;
            pre[i] = i;
        }
        
        int length = 1;
        int pos = 0;
        for (int i = 1; i < nums.length; i++) {
            for (int j = i-1; j >= 0; j--) {
                if (nums[i] % nums[j] == 0 && memo[j] + 1 > memo[i]) {
                    memo[i] = memo[j] + 1;
                    pre[i] = j;
                }
            }
            if (memo[i] > length) {
                length = memo[i];
                pos = i;
            }
        }
        
        int divi = nums[pos];
        res.add(nums[pos]);
        while (true) {
            if (pos == pre[pos]) {
                break;
            }
            pos = pre[pos];
            res.add(nums[pos]);
        }
        return res;
    }
}
```

###  例题 354. Russian Doll Envelopes
You have a number of envelopes with widths and heights given as a pair of integers (`w`, `h`). One envelope can fit into another if and only if both the width and height of one envelope is greater than the width and height of the other envelope.
What is the maximum number of envelopes can you Russian doll? (put one inside other)
Note:
Rotation is not allowed.

Example:
Input: `[[5,4],[6,4],[6,7],[2,3]]`
Output: 3 
Explanation: The maximum number of envelopes you can Russian doll is `3` ([2,3] => [5,4] => [6,7]).

###  solution 
首先对长度进行排序，然后再判断宽度的关系
```java
class Solution {
    public int maxEnvelopes(int[][] envelopes) {
        // Arrays.sort(envelopes, new Comparator<int[]>() {
        //     public int compare(int[] arr1, int[] arr2) {
        //         if (arr1[0] == arr2[0]) {
        //             return arr2[1] - arr1[1];
        //         } else {
        //             return arr1[0] - arr2[0];
        //         }
        //    }
        // });
        
        Arrays.sort(envolopes, new Comparator<int[]>() {
            public int compare(int[] a, int[] b) {
                if (a[0] == b[0]) {
                    return a[1] - b[1];
                } else {
                    return a[0] - b[0];
                }
            }
        });
        
        
    }
}
```
将长度进行递增排序后，只需要选出一串信封，确保宽度是递增的，这个问题就转化为前面的最长自增子序列 
需要注意的是，长度宽度必须严格保证大于，当长度递增排序以后，如果有相等的情况出现会发生错误，比如 `[ (1,2), (1,3), (1,4) ]`，如果不加处理，这三个信封都会被选中。
为了解决问题，排序时以长度为第一排序关键字升序排列，以宽度 为第二排序关键字降序排列，这样就变成了``[ (1,4), (1,3), (1,2) ]``，而这个宽度序列中最大增序列长度为 `1`。

### Solution
```java
class Solution {
    public int maxEnvelopes(int[][] envelopes) {
        if (envelopes.length == 0) {
            return 0;
        }
        
        int n = envelopes.length;
        Arrays.sort(envelopes, new Comparator<int[]>() {
            public int compare(int[] e1, int[] e2) {
                if (e1[0] != e2[0]) {
                    return e1[0] - e2[0];
                } else {
                    return e2[1] - e1[1];
                }
            }
        });

        int[] f = new int[n];
        Arrays.fill(f, 1);
        int ans = 1;
        for (int i = 1; i < n; ++i) {
            for (int j = 0; j < i; ++j) {
                if (envelopes[j][1] < envelopes[i][1]) {
                    f[i] = Math.max(f[i], f[j] + 1);
                }
            }
            ans = Math.max(ans, f[i]);
        }
        return ans;
    }
}
```

###  例题 403. Frog Jump
A frog is crossing a river. The river is divided into x units and at each unit there may or may not exist a stone. The frog can jump on a stone, but it must not jump into the water.
Given a list of stones' positions (in units) in sorted ascending order, determine if the frog is able to cross the river by landing on the last stone. Initially, the frog is on the first stone and assume the first jump must be 1 unit.
If the frog's last jump was k units, then its next jump must be either` k - 1`, `k`, or `k + 1` units. Note that the frog can only jump in the forward direction.

Note:
The number of stones is ≥ 2 and is < 1,100.
Each stone's position will be a non-negative integer < 231.
The first stone's position is always 0.

Example 1:
[0,1,3,5,6,8,12,17]
There are a total of 8 stones. The first stone at the 0th unit, second stone at the 1st unit, third stone at the 3rd unit, and so on... The last stone at the 17th unit.

Return true. The frog can jump to the last stone by jumping 
1. 1 unit to the 2nd stone, then 2 units to the 3rd stone, then 
2. 2 units to the 4th stone, then 3 units to the 6th stone, 
3. 4 units to the 7th stone, and 5 units to the 8th stone.

###  solution 

### 例题 5. Longest Palindromic Substring

Given a string s, find the longest palindromic substring in s. You may assume that the maximum length of s is 1000.

Example 1:
Input: "babad"
Output: "bab"
Note: "aba" is also a valid answer.

Example 2:
Input: "cbbd"
Output: "bb"

## Solution
状态：`res[i][j]`，字符串坐标为 `i` 的字符到坐标为 j 的字符是否是回文 转移方程： `res[i][j] =res[i+1][j-1]` and S<sub>i</sub> == S<sub>j</sub>
初始条件: `res[i][i]= true`,`res[i][i+1]` = (S<sub>i</sub> == S<sub>i+1</sub>)
计算顺序：子问题是长度较短的`substring`，大问题是较长的`substring`，顺序就是从长度为 `1` 算起，从坐标 `0` 为起始算起。

```java
    public String longestPalindrome(String s) {
        String str = new String();
        if (s == null || s.length() == 0) return str;
        int n = s.length();
        
        boolean[][] res = new boolean[n][n];
        
        for (int len = 1; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                if (len == 1) {
                    res[i][i + len - 1] = true;
                    continue;
                }
                if (len == 2) {
                    res[i][i + len - 1] = s.charAt(i) == s.charAt(i + len - 1);
                    continue;
                }
                
                res[i][i + len - 1]= res[i+1][i + len - 2] && s.charAt(i) == s.charAt(i + len - 1);
            }
        }
        
        for (int len = n; len >= 0; len--) {
            for (int i = 0; i <= n - len; i++) {
                if (res[i][i+len-1]) return s.substring(i, i+len);
            }
        }
        return str;
    }
```