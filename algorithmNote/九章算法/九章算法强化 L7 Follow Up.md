# 九章算法强化 L7 Follow Up

## 例题 138. Subarray Sum

Given an integer array, find a subarray where the sum of numbers is zero. Your code should return the index of the first number and the index of the last number.

Example 1:
Input:  [-3, 1, 2, -3, 4]
Output: [0, 2] or [1, 3].
Explanation: return anyone that the sum is 0.

Example 2:
Input:  [-3, 1, -4, 2, -3, 4]
Output: [1,5]	

## Solution
要计算子数组和，很自然的想到前缀和数组 S[i]，题目就变成了求 i，j 使得$S[j] - S[i-1] = 0$， 就是$S[i-1] = S[j]$

```java
    public List<Integer> subarraySum(int[] nums) {
        // write your code here
        List<Integer> res = new ArrayList<>();
        if (nums == null || nums.length == 0) {
            return res;
        }
        int n = nums.length;
        int[] s = new int[n + 1];
        s[0] = 0;
        
        for (int i = 0; i < n; i++) {
            s[i+1] = s[i] + nums[i];
        }
        
        HashMap<Integer, Integer> map  =new HashMap<>();
        for (int i = 0; i < n + 1; i++) {
            if (!map.containsKey(s[i])) {
                map.put(s[i], i);
            } else {
                int index = map.get(s[i]);
                res.add(index);
                res.add(i-1);
                break;
            }
        }
        return res;
    }
```

## 例题 139. Subarray Sum Closest
Given an integer array, find a subarray with sum closest to zero. Return the indexes of the first number and last number.

Example
Input: 
[-3,1,1,-3,5] 
Output: 
[0,2]
Explanation: [0,2], [1,3], [1,1], [2,2], [0,4]

Challenge
O(nlogn) time

## Solution 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200528023303483.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)

```java
    public int[] subarraySumClosest(int[] nums) {
        // write your code here
        int[] res = new int[2];
        if (nums == null || nums.length == 0) {
            return res;
        }
        
        int n = nums.length;
        Pair[] s = new Pair[n + 1];
        s[0] = new Pair(0, 0);
        int tmp = 0;
        for (int i = 0; i < n; i++) {
            tmp += nums[i];
            s[i + 1] = new Pair(tmp, i+1);
        }
        
        Arrays.sort(s, new Comparator<Pair>() {
            public int compare(Pair a, Pair b) {
                return a.sum - b.sum;
            }    
        });
        int temp = Integer.MAX_VALUE;
        res[0] = 0;
        res[1] = 0;
        for (int i = 1; i < s.length; i++) {
            if (s[i].sum - s[i - 1].sum < temp) {
                temp = s[i].sum - s[i - 1].sum;
                int[] tmp_res = new int[]{s[i].index - 1, s[i-1].index - 1};
                Arrays.sort(tmp_res);
                res[0] = tmp_res[0] + 1;
                res[1] = tmp_res[1];
            }
        }
        return res;
    }
```

## 例题 405. Submatrix Sum
Given an integer matrix, find a submatrix where the sum of numbers is zero. Your code should return the coordinate of the left-up and right-down number.

If there are multiple answers, you can return any of them.

Example 1:
Input:
[
  [1, 5, 7],
  [3, 7, -8],
  [4, -8 ,9]
]
Output: [[1, 1], [2, 2]]

Example 2:
Input:
[
  [0, 1],
  [1, 0]
]
Output: [[0, 0], [0, 0]]

Challenge
O(n3) time.

## Solution
采用二维的前缀和数组，$S[i][j]代表$从 (i, j) 到 (0,0) 为止的矩阵的和。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200528032359362.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
这种方法的复杂度是 $O(m^2n^2)$。

如果要优化的话，需要把问题转化为一维。
规定好矩阵的上下界，这样上下界内的一列数字的和就可以当作一维数组中的一个值。
遍历上下界，复杂度为$O(m^2)$，一维数组求为 0 的和，复杂度为$O(n)$，于是复杂度是$O(m^2n)$
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200528035323299.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)