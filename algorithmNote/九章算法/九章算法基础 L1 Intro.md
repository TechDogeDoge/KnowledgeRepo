# 九章算法基础 L1 Intro

##  1
算法面试不是完全的解题，和面试官的沟通也十分重要。

拿到一个问题，不要着急马上就开始做，可以首先询问一下有没有别的要求比如说时间复杂度空间复杂度

##  2
举例：

LeetCode 28 implement strStr
Return the index of the first occurrence of needle in haystack, or -1 if needle is not part of haystack.

Example 1:
Input: haystack = "hello", needle = "ll"
Output: 2

Example 2:
Input: haystack = "aaaaa", needle = "bba"
Output: -1

简单解法：
利用 substring bruteforce

高级算法：KMP
虽然这个算法很巧，但是实际上并没有什么必要，不需要这么高的难度。同时准备这个算法就很费时间

注意代码的可读性和style，
1. 变量名命名可以让人一看就知道是什么
2. 注意分号和二元运算符要加空格，换行把代码分开

检查 input 是否 valid，检查 index 是否超出范围了

虽然 KMP 不要求，但是有另一个算法叫 Rabin-Karp 可以用来进行字符串匹配，复杂度和 KMP 是一样的。同时学习这个算法可以学习关于Hash function的知识

##  3
面试的时候尽量先聊后写，写完再聊
尽量避免边聊边写

##  4
刷题的量：
200道，尽量达到每道题3次提交内通过

##  5 Recursion DFS (backtracking) BFS
LeetCode 78 Subsets
Given a set of distinct integers, nums, return all possible subsets (the power set).

Note: The solution set must not contain duplicate subsets.

Example:
Input: nums = [1,2,3]
Output:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]

```java
    class Solution {
        public List<List<Integer>> subsets(int[] nums) {
            List<List<Integer>> result = new ArrayList<>();
            //ArrayList<ArrayList<Integer>> result = new ArrayList<>();
            if (nums == null) {
                return result;
            }

            if (nums.length == 0) {
                result.add(new ArrayList<Integer>());
                return result;
            }

            Arrays.sort(nums);
            // find all the subsets that start with [], put these into result
            helper(nums, 0, new ArrayList<Integer>(), result);

            return result;
        }

    // find all the subsets that 
    // start with 'subset' and end with a number after nums[offset]
    // put these into result
        public void helper (int[] nums,
                            int offset,
                            ArrayList<Integer> subset,
                            List<List<Integer>> result) {
            // the reason why we create a new ArrayList with same value as subset is that
            // we don't want any upcoming operation affect the value stored in result
            result.add(new ArrayList<Integer>(subset));
            for (int i = offset; i < nums.length; i++ ) {
                subset.add(nums[i]);
                // find all the subsets that start with 'subset', put these into result
                helper(nums, i + 1, subset, result);
                subset.remove(subset.size() - 1);
                // backtracking

            }

        }
    }
// 递归三要素
// 1 递归的定义：在nums中寻找所有以subset开头的子集，并放入results
// 2 递归的拆解：
// result里面add new ArrayList<Integer>(subset)是为了即使后面操作改变了subset的值，也不会影响result里面的值，因为new ArrayList<Integer>(subset)就相当于克隆了一份subset放到result里面
// 3 递归的出口
```
还有一个拓展问题，如果说 nums 里面出现了 duplicates 如何处理，如 [1,2,2,3]
分析一下, [1, 2, 2, 3]相比于[1,2,3]只多了一个subset，[1,2,2,3].
所以有 duplicates 时，我们只关心某个数字出现了几次并不关心到底是哪个 duplicates。
由于我们在最开始 sort 了 nums 所以duplicates一定是相邻的，所以规定有 duplicates 时一定从第一个开始连续取，对于[1,2,2,3]来说，标记为$[1,2_1,2_2,3]$，一定要先取$2_1$而不是$2_2$

整个代码的唯一改动就在 helper function里面的 for 循环

```java
        for (int i = offset; i < nums.length; i++ ) {
            // deal with duplicates cases like [1,2,2,3]              
            if (i != offset && nums[i] == nums[i-1]) {
            continue;
            }
            subset.add(nums[i]);
            // find all the subsets that start with 'subset', put these into result
            helper(nums, i + 1, subset, result);
            // backtracking
            subset.remove(subset.size() - 1);
        }
```



## 6 Backtracking Template

Backtracking有两个特点：
1. 遇到一个无效的支路可以中间停止，不必走完这个支路
2. 记录走到的支路位置，每当回溯时，跳回到上一次的支路

```java
def backtrack(candidate):
    if find_solution(candidate):
        output(candidate)
        return
    
    # iterate all possible candidates.
    for next_candidate in list_of_candidates:
        if is_valid(next_candidate):
            # try this partial candidate solution
            place(next_candidate)
            # given the candidate, explore further.
            backtrack(next_candidate)
            # backtrack
            remove(next_candidate)
```
<img src="https://img-blog.csdnimg.cn/20210413114934537.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom: 50%;" />

#  7 四大法宝
1. 多把时间花在 Medium 上面，而不是 Hard 上面，提高编程速度和 bug free。可以计时，30分钟以内
2. 和考官沟通，一起合作，可以适当提问
3. 理解而不是背诵，思维方式，技巧，分类总结
4. 不要把时间浪费在基本不会考又令你心虚的内容上，比如红黑树，KMP，AVL，ACM竞赛题