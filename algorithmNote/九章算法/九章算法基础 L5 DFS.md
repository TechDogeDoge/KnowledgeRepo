# 九章算法基础 L5 DFS
碰到让你找到所有方案的题，一定是 DFS
90% DFS 的题，要么是排列，要么是组合

**解决一个 DFS 问题，最重要的就是画出来递归的树**

关于时间复杂度分析
搜索的时间复杂度： O(答案总数 * 构造每个答案的时间)
例如 subsets 问题，子集一共 2<sup>n</sup> 个，每个子集的平均长度或者说花费的时间平均是 O(n)，所以时间复杂度是 O(2<sup>n</sup> *n)$。

动态规划时间复杂度： O(状态总数 * 计算每个状态的时间复杂度)

Divide Conquer 时间复杂度：O(二叉树节点个数 * 每个结点的计算时间)


## Subsets
Given a set of distinct integers, nums, return all possible subsets (the power set).

Note: The solution set must not contain duplicate subsets.

Example:
<img src="https://img-blog.csdnimg.cn/20200412111555887.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70#pic_center =200x300" alt="在这里插入图片描述" style="zoom:67%;" />

## Solution
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
    
    // find all the subsets that start with 'subset', put these into result
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
#  组合 Combination
求出所有满足条件的组合，组合中的元素与顺序无关
##  例题 39. Combination Sum
Given a set of candidate numbers (candidates) (without duplicates) and a target number (target), find all unique combinations in candidates where the candidate numbers sums to target.

The same repeated number may be chosen from candidates unlimited number of times.

Note:
All numbers (including target) will be positive integers.
The solution set must not contain duplicate combinations.

Example 1:
Input: candidates = [2,3,6,7], target = 7,
A solution set is:
[
  [7],
  [2,2,3]
]

Example 2:
Input: candidates = [2,3,5], target = 8,
A solution set is:
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]

##  solution
首先 candidates 里面没有duplicates，然后为了避免结果的重复，要求每个数后面只能加大于等于它的数。

```java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> result = new ArrayList<>();
        if (candidates == null) {
            return result;
        }
        Arrays.sort(candidates);
        ArrayList<Integer> solu = new ArrayList<>(); 
        helper(candidates, target, solu, result, 0, 0);
        return result;
    }
    
    // define the helper function
    // solu:  current potential solution prefix
    // sum:   sum of the solu
    // index: 
    // find the all the solutions that start with solu, sum of which is 'sum' 
    // and put them into the result,
    // also, all the solutions are attached with integer from index to the end,
    // which is making sure no duplicates solutions
    public void helper(int[] candidates, int target, ArrayList<Integer> solu, List<List<Integer>> result, int sum, int index) {
        for (int i = index; i < candidates.length; i++) {
            solu.add(candidates[i]);
            sum = sum + candidates[i];
            if (sum == target) {
                result.add(new ArrayList<Integer>(solu));
                continue;
            } else if (sum > target) {
                continue;
            } else if(sum < target) {
                helper(candidates, target, new ArrayList<Integer>(solu), result, sum, i);
                solu.remove(solu.size() - 1);
                sum = sum - candidates[i];
            }
        }
    }
}
```
上面这种写法并没有写出递归的出口是因为，for 循环里面判断 sum 和 target 的关系确保了出口，还有一种写法是，不在for循环里面判断，而是在 helper 一开始判断，这样就构成了一个递归的出口。

##  例题 40. Combination Sum II
Given a collection of candidate numbers (candidates) and a target number (target), find all unique combinations in candidates where the candidate numbers sums to target.

Each number in candidates may only be used once in the combination.

Note:
All numbers (including target) will be positive integers.
The solution set must not contain duplicate combinations.

Example 1:
Input: candidates = [10,1,2,7,6,1,5], target = 8,
A solution set is:
[
  [1, 7],
  [1, 2, 5],
  [2, 6],
  [1, 1, 6]
]

Example 2:
Input: candidates = [2,5,2,1,2], target = 5,
A solution set is:
[
  [1,2,2],
  [5]
]
##  solution
解法和上面一题基本一致，主要有两点区别：
1. candidates 里面有 duplicates
2. 选取数时，一个数字不可以反复选

为了解决 1，添加了一个 if 语句，当后面数字和当前数字一样时就会继续下一个循环
为了解决 2， 在 helper 的 index 加了一个 1

```java
class Solution {
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        List<List<Integer>> result = new ArrayList<>();
        if (candidates == null) {
            return result;
        }
        Arrays.sort(candidates);
        ArrayList<Integer> solu = new ArrayList<>(); 
        helper(candidates, target, solu, result, 0, 0);
        return result;
    }
    
    // define the helper function
    // solu:  current potential solution prefix
    // sum:   sum of the solu
    // index: 
    // find the all the solutions that start with solu, sum of which is 'sum' 
    // and put them into the result,
    // also, all the solutions are attached with integer from index to the end,
    // which is making sure no duplicates solutions
    public void helper(int[] candidates, int target, ArrayList<Integer> solu, List<List<Integer>> result, int sum, int index) {
        for (int i = index; i < candidates.length; i++) {
            // add this if phrase to avoid duplicates,
            // like for candidates = [1, 1, 2, 4], target = 3
            // avoid [1, 2] and [1, 2]
            if (i != index && candidates[i] == candidates[i-1]) {
                continue;
            }
            
            solu.add(candidates[i]);
            sum = sum + candidates[i];
            if (sum == target) {
                result.add(new ArrayList<Integer>(solu));
                continue;
            } else if (sum > target) {
                continue;
            } else if(sum < target) {
                helper(candidates, target, new ArrayList<Integer>(solu), result, sum, i+1);
                solu.remove(solu.size() - 1);
                sum = sum - candidates[i];
            }
        }
    }
}
```

##  例题 131. Palindrome Partitioning
Given a string s, partition s such that every substring of the partition is a palindrome.

Return all possible palindrome partitioning of s.

Example:

Input: "aab"
Output:
[
  ["aa","b"],
  ["a","a","b"]
]

##  Solution
```java
// version 1: shorter but slower
public class Solution {
    /**
     * @param s: A string
     * @return: A list of lists of string
     */
    public List<List<String>> partition(String s) {
        List<List<String>> results = new ArrayList<>();
        if (s == null || s.length() == 0) {
            return results;
        }
        List<String> partition = new ArrayList<String>();
        helper(s, 0, partition, results);
        return results;
    }
    
    private void helper(String s, int startIndex, List<String> partition, List<List<String>> results) {
        if (startIndex == s.length()) {
            results.add(new ArrayList<String>(partition));
            return;
        }
        
        for (int i = startIndex; i < s.length(); i++) {
            String subString = s.substring(startIndex, i + 1);
            if (!isPalindrome(subString)) {
                continue;
            }
            partition.add(subString);
            helper(s, i + 1, partition, results);
            partition.remove(partition.size() - 1);
        }
    }
    
    private boolean isPalindrome(String s) {
        for (int i = 0, j = s.length() - 1; i < j; i++, j--) {
            if (s.charAt(i) != s.charAt(j)) {
                return false;
            }
        }
        return true;
    }
}
```
## Solution
```java
// version 2: longer but faster
public class Solution {
    List<List<String>> results;
    boolean[][] isPalindrome;
    
    /**
     * @param s: A string
     * @return: A list of lists of string
     */
    public List<List<String>> partition(String s) {
        results = new ArrayList<>();
        if (s == null || s.length() == 0) {
            return results;
        }
        
        getIsPalindrome(s);
        
        helper(s, 0, new ArrayList<Integer>());
        
        return results;
    }
    
    private void getIsPalindrome(String s) {
        int n = s.length();
        isPalindrome = new boolean[n][n];
        
        for (int i = 0; i < n; i++) {
            isPalindrome[i][i] = true;
        }
        for (int i = 0; i < n - 1; i++) {
            isPalindrome[i][i + 1] = (s.charAt(i) == s.charAt(i + 1));
        }
        
        for (int i = n - 3; i >= 0; i--) {
            for (int j = i + 2; j < n; j++) {
                isPalindrome[i][j] = isPalindrome[i + 1][j - 1] && s.charAt(i) == s.charAt(j);
            }
        }
    }
    
    private void helper(String s,
                        int startIndex,
                        List<Integer> partition) {
        if (startIndex == s.length()) {
            addResult(s, partition);
            return;
        }
        
        for (int i = startIndex; i < s.length(); i++) {
            if (!isPalindrome[startIndex][i]) {
                continue;
            }
            partition.add(i);
            helper(s, i + 1, partition);
            partition.remove(partition.size() - 1);
        }
    }
    
    private void addResult(String s, List<Integer> partition) {
        List<String> result = new ArrayList<>();
        int startIndex = 0;
        for (int i = 0; i < partition.size(); i++) {
            result.add(s.substring(startIndex, partition.get(i) + 1));
            startIndex = partition.get(i) + 1;
        }
        results.add(result);
    }
}
```

#  排列 Permutation
求出所有满足条件的排列，排列中的元素是顺序相关的
时间复杂度与 $n!$ 相关

##  例题 46. Permutations
Given a collection of distinct integers, return all possible permutations.

Example:
Input: [1,2,3]
Output:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]

## Solution
之前提供过一种写法，把每种 permutation 都看作 input 中的元素相互 swap 产生的。还可以把每种 permutation 都看作向一个空集里面不断添加元素，直到结果长度和 input 相等，同时添加的元素不可以重复。同样需要注意的是在向 result 添加元素时要新创建一个对象

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        if (nums == null || nums.length == 0) {
            return result;
        }
        
        ArrayList<Integer> permu = new ArrayList<>();
        helper(result, nums, permu);
        return result;
    }
    
    public void helper(List<List<Integer>> result, int[] nums, ArrayList<Integer> permu) {
        if (permu.size() == nums.length) {
            result.add(new ArrayList<Integer>(permu));
        }
        
        for (int i = 0; i < nums.length; i++) {
            if (permu.contains(nums[i])) {
                continue;
            }
            permu.add(nums[i]);
            helper(result, nums, permu);
            permu.remove(permu.size() - 1);
        }
    }
}
```

##  Solution
利用visited来记录每个数字是否选过了
```java
public class Solution {
    /*
     * @param nums: A list of integers.
     * @return: A list of permutations.
     */
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> results = new ArrayList<>();
        if (nums == null) {
            return results;
        }
        dfs(nums, new boolean[nums.length], new ArrayList<Integer>(), results);
        return results;
    }
    
    private void dfs(int[] nums,
                     boolean[] visited,
                     List<Integer> permutation,
                     List<List<Integer>> results) {
        if (nums.length == permutation.size()) {
            results.add(new ArrayList<Integer>(permutation));
            return;
        }
        
        for (int i = 0; i < nums.length; i++) {
            if (visited[i]) {
                continue;
            }
            permutation.add(nums[i]);
            visited[i] = true;
            dfs(nums, visited, permutation, results);
            visited[i] = false;
            permutation.remove(permutation.size() - 1);
        }
    }
}
```


```java
public class Solution {
    public List<List<Integer>> permute(int[] nums) {
         List<List<Integer>> results = new ArrayList<>();
         if (nums == null) {
             return results; 
         }
         List<Integer> permutation = new ArrayList<Integer>();
         Set<Integer> set = new HashSet<>();
         helper(nums, permutation, set, results);
         return results;
    }
    
    // 1. 找到所有以permutation开头的排列
    public void helper(int[] nums, List<Integer> permutation, 
    		Set<Integer> set, List<List<Integer>> results) {
        // 3. 递归的出口
        if (permutation.size() == nums.length) {
            results.add(new ArrayList<Integer>(permutation));
            return;
        }
        // [3] => [3,1], [3,2], [3,4] ...
        for (int i = 0; i < nums.length; i++) {
            if (set.contains(nums[i])) {
                continue;
            }
            permutation.add(nums[i]);
            set.add(nums[i]);
            helper(nums, permutation, set, results);
            set.remove(nums[i]);
            permutation.remove(permutation.size() - 1);
        }
    }
}

// Non-Recursion
class Solution {
    /**
     * @param nums: A list of integers.
     * @return: A list of permutations.
     */
    public List<List<Integer>> permute(int[] nums) {
        ArrayList<List<Integer>> permutations = new ArrayList<List<Integer>>();
        if (nums == null) {
            return permutations;
        }

        if (nums.length == 0) {
            permutations.add(new ArrayList<Integer>());
            return permutations;
        }
        
        int n = nums.length;
        ArrayList<Integer> stack = new ArrayList<Integer>();
        
        stack.add(-1);
        while (stack.size() != 0) {
            Integer last = stack.get(stack.size() - 1);
            stack.remove(stack.size() - 1);
            // increase the last number
            int next = -1;
            for (int i = last + 1; i < n; i++) {
                if (!stack.contains(i)) {
                    next = i;
                    break;
                }
            }
            if (next == -1) {
                continue;
            }
            // generate the next permutation
            stack.add(next);
            for (int i = 0; i < n; i++) {
                if (!stack.contains(i)) {
                    stack.add(i);
                }
            }
            // copy to permutations set
            ArrayList<Integer> permutation = new ArrayList<Integer>();
            for (int i = 0; i < n; i++) {
                permutation.add(nums[stack.get(i)]);
            }
            permutations.add(permutation);
        }
        return permutations;
    }
}
```

##  例题 47. Permutations II
Given a collection of numbers that might contain duplicates, return all possible unique permutations.

Example:
Input: [1,1,2]
Output:
[
  [1,1,2],
  [1,2,1],
  [2,1,1]
]

采用和上面一样的思路，唯一的问题是 nums 里面允许出现duplicates。所以需要修改代码去除出现 duplicates 的情况


### Solution
```java
class Solution {
    public List<List<Integer>> permuteUnique(int[] nums) {
         Arrays.sort(nums);
         List<List<Integer>> results = new ArrayList<>();
         List<Integer> permutation = new ArrayList<Integer>();
         Set<Integer> set = new HashSet<>();
         helper(nums, permutation, set, results, 0);
         return results;
    }

    public void helper(int[] nums,
                       List<Integer> permutation,
                       Set<Integer> set,
                       List<List<Integer>> results, int idx) {
        // 3. 递归的出口
        if (permutation.size() == nums.length) {
            results.add(new ArrayList<Integer>(permutation));
            return;
        }
        // [3] => [3,1], [3,2], [3,4] ...
        for (int i = 0; i < nums.length; i++) {
            if (set.contains(i) || (i > 0 && nums[i] == nums[i-1]) && !set.contains(i-1)) {
                continue;
            }
            permutation.add(nums[i]);
            set.add(i);
            helper(nums, permutation, set, results, idx + 1);
            set.remove(i);
            permutation.remove(permutation.size() - 1);
        }
    }
}
```

##  例题 51. N-Queens
The n-queens puzzle is the problem of placing n queens on an n×n chessboard such that no two queens attack each other.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200108123628620.png)
Given an integer n, return all distinct solutions to the n-queens puzzle.

Each solution contains a distinct board configuration of the n-queens' placement, where 'Q' and '.' both indicate a queen and an empty space respectively.

Example:
Input: 4
Output: [
 [".Q..",  // Solution 1
  "...Q",
  "Q...",
  "..Q."],

 ["..Q.",  // Solution 2
  "Q...",
  "...Q",
  ".Q.."]
]
Explanation: There exist two distinct solutions to the 4-queens puzzle as shown above.

##  solution
这个题目最主要的就是如何把棋盘化简。有一种方法是把 queen 的 layout 转化成数组的 permutation。
以 n = 4 为例，第一种 solution 就可以表示为 [1, 3, 0, 2]，第二种 solution 就可以表示为 [2, 0, 3, 1]。 数组中的第 i 个数字代表 queen 在第 i 行中摆放的位置。所有的 solution 都是数组 [0, 1, 2, 3] 的 permutation。同时这种表示方法确保了queen 在水平和竖直方向上不会冲突，在后面判断答案时，只需要考虑斜向的问题

```java
class Solution {
    public List<List<String>> solveNQueens(int n) {
        // tranfer the layout of queen into a permuation of an int array
        List<List<String>> result = new ArrayList<>();
        if (n <= 0) {
            return result;
        }
        
        int[] nums = new int[n];
        for (int i = 0; i < n; i++) {
            nums[i] = i;
        }
        
        ArrayList<Integer> pre = new ArrayList<>();
        helper(result, pre, nums);
        return result;
    }
    
    // get all the permutation
    public void helper(List<List<String>> result, ArrayList<Integer> pre, int[] nums) {
        if (pre.size() == nums.length) {
            if (isValid(pre)) {
                ArrayList<String> tmp = transfer(pre);
                result.add(tmp);
            }
        }
        for (int i = 0; i < nums.length; i++) {
            if (pre.contains(nums[i])) {
                continue;
            }
            pre.add(nums[i]);
            helper(result, pre, nums);
            pre.remove(pre.size() - 1);
        }
    }
    
    // transfer a permutaion to a chessboard layout
    public ArrayList<String> transfer(ArrayList<Integer> pre) {
        ArrayList<String> res = new ArrayList<>();
        for (int i = 0; i < pre.size(); i++) {
            StringBuffer str = new StringBuffer();
            for (int j = 0; j < pre.size(); j++) {
                if (pre.get(i) == j) {
                    str.append("Q");
                } else {
                    str.append(".");
                }
            }
            res.add(str.toString());
        }
        return res;
    }
    
    // judge whether the permutation is valid
    public boolean isValid(ArrayList<Integer> pre) {
        for (int i = 0; i < pre.size(); i++) {
            for (int j = i; j < pre.size(); j++) {
                if (i != j && Math.abs(pre.get(i) - pre.get(j)) == Math.abs(i - j)) {
                    return false;
                }
            }
        }
        return true;
    }
}
```
上面的算法运行时间较长，可以继续优化

##  solution
LeetCode 提供了另一种写法，同样采用 DFS 的算法。 在棋盘上从第 0 行开始放置 queen，每次放置 queen 以后计算棋盘上不可以放置的位置，然后在下一行放置下一个 queen 直到找到一种 layout 或者去除一种可能的解。
```java
class Solution {
  int rows[];
  // "hill" diagonals
  int hills[];
  // "dale" diagonals
  int dales[];
  int n;
  // output
  List<List<String>> output = new ArrayList();
  // queens positions
  int queens[];

  public boolean isNotUnderAttack(int row, int col) {
    int res = rows[col] + hills[row - col + 2 * n] + dales[row + col];
    return (res == 0) ? true : false;
  }

  public void placeQueen(int row, int col) {
    queens[row] = col;
    rows[col] = 1;
    hills[row - col + 2 * n] = 1;  // "hill" diagonals
    dales[row + col] = 1;   //"dale" diagonals
  }

  public void removeQueen(int row, int col) {
    queens[row] = 0;
    rows[col] = 0;
    hills[row - col + 2 * n] = 0;
    dales[row + col] = 0;
  }

  public void addSolution() {
    List<String> solution = new ArrayList<String>();
    for (int i = 0; i < n; ++i) {
      int col = queens[i];
      StringBuilder sb = new StringBuilder();
      for(int j = 0; j < col; ++j) sb.append(".");
      sb.append("Q");
      for(int j = 0; j < n - col - 1; ++j) sb.append(".");
      solution.add(sb.toString());
    }
    output.add(solution);
  }

  public void backtrack(int row) {
    for (int col = 0; col < n; col++) {
      if (isNotUnderAttack(row, col)) {
        placeQueen(row, col);
        // if n queens are already placed
        if (row + 1 == n) addSolution();
          // if not proceed to place the rest
        else backtrack(row + 1);
        // backtrack
        removeQueen(row, col);
      }
    }
  }

  public List<List<String>> solveNQueens(int n) {
    this.n = n;
    rows = new int[n];
    hills = new int[4 * n - 1];
    dales = new int[2 * n - 1];
    queens = new int[n];

    backtrack(0);
    return output;
  }
}
```

##  例题 127. Word Ladder
Given two words (beginWord and endWord), and a dictionary's word list, find the length of shortest transformation sequence from beginWord to endWord, such that:

Only one letter can be changed at a time.
Each transformed word must exist in the word list. Note that beginWord is not a transformed word.

Note:
Return 0 if there is no such transformation sequence.
All words have the same length.
All words contain only lowercase alphabetic characters.
You may assume no duplicates in the word list.
You may assume beginWord and endWord are non-empty and are not the same.

Example 1:
Input:
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]
Output: 5
Explanation: As one shortest transformation is "hit" -> "hot" -> "dot" -> "dog" -> "cog",
return its length 5.

Example 2:
Input:
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log"]
Output: 0
Explanation: The endWord "cog" is not in wordList, therefore no possible transformation.


##  Solution
解法也很简单，因为题目要求最短的长度，所以判断为 BFS 问题。关键就是把 word 的变换转化成树。
额外知识点：
list 转 set  ```HashSet<String> dict = new HashSet(wordList);```
String中修改一个字符的值 ```char[] chars = word.toCharArray();  chars[i] = ch; return new String(chars);```
```java
class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        if (!wordList.contains(endWord)) {
            return 0;
        }
        
        Queue<String> queue = new LinkedList<>();
        HashSet<String> set = new HashSet<>();
        HashSet<String> dict = new HashSet(wordList);
        int n = beginWord.length();
        int len = 1;
        
        queue.offer(beginWord);
        set.add(beginWord);
        
        while (!queue.isEmpty()) {
            int size = queue.size();
            len++;
            for (int i = 0; i < size; i++) {
                String word = queue.poll();
                for (String nextWord : getNext(word, dict)) {
                    if (nextWord.equals(endWord)) {
                        return len;
                    }
                    if (!set.contains(nextWord)) {
                        set.add(nextWord);
                        queue.offer(nextWord);
                    }
                }
            }
        }
        return 0;
    }
    
    public ArrayList<String> getNext(String word, HashSet<String> dict) {
        ArrayList<String> nextWords = new ArrayList<>();
        int n = word.length();
        for (int i = 0; i < n; i++) {
            for (char j = 'a'; j <= 'z'; j++) {
                if (word.charAt(i) == j) {
                    continue;
                }
                String next = setChar(word, i, j);
                if (dict.contains(next)) {
                    nextWords.add(next);
                }
            }
        }
        return nextWords;
    }
    
    public String setChar(String word, int i, char ch) {
        char[] chars = word.toCharArray();
        chars[i] = ch;
        return new String(chars);
        // StringBuffer str = new StringBuffer();
        // str.append(word.substring(0,i));
        // str.append(ch);
        // str.append(word.substring(i+1));
        // return str.toString();
    }
}
```



### Binary tree path sum

Given a binarytree, find all the paths that sum of the nodes in the path equals to a given number target.
A valid path is from root node to any leaf nodes.

**Example:**
Given a binary tree, and target = 5
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229095344787.png)
output:

```java
[
	{1, 4},
	{1, 2, 2}
]
```

> 解法

典型采用回溯法进行求解的问题。一个节点的左右两个子节点即为两个不同选择分支。

```java
class Solution {
    public List<List<Integer>> binaryTreePathSum(TreeNode root, int target) {
        List<List<Integer>> result = new ArrayList<>();
        if (root == null) {
            return result;
        }
        List<Integer> path = new ArrayList<>();
        path.add(root.val);
        helper(root, target, path, result, root.val);
        return result;
    }
    
    public void helper(TreeNode root, int target, List<Integer> path, List<List<Integer>> result, int currSum) {
        if (currSum > target) return;
        if (root.left == null && root.right == null) {
            if (currSum == target) {
                result.add(new ArrayList<Integer>(path));
            }
            return;
        }
        if (root.left != null) {
            path.add(root.left.val);
            helper(root.left, target, path, result, currSum + root.left.val);
            path.remove(path.size - 1);
        }
        if (root.right != null) {
            path.add(root.right.val);
            helper(root.right, target, path, result, currSum + root.right.val);
            path.remove(path.size - 1);
        }
    }
}
```



#  Non-recursive DFS

多数采用 Stack
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200109052408112.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)

