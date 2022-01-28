# 九章算法强化 L1 Two Pointers & K-Largest

[TOC]



 # Two Pointers

## 例题 209. Minimum Size Subarray Sum
Given an array of n positive integers and a positive integer s, find the minimal length of a contiguous subarray of which the sum ≥ s. If there isn't one, return 0 instead.

Example: 
Input: s = 7, nums = [2,3,1,2,4,3]
Output: 2
Explanation: the subarray [4,3] has the minimal length under the problem constraint.
Follow up:
If you have figured out the O(n) solution, try coding another solution of which the time complexity is O(n log n). 

##  solution 1
最简单的思路，采用双指针，右指针遍历，左指针向右一个一个的移动

##  solution 2
第二种思路：右指针向右一个一个的移动，左指针也向右移动直到大于等于 s 的最靠右位置。然后下一次循环，右指针向右移动一个时，左指针也一定向右移动。有一个重要的特性， 当右指针向右移动时，左指针也只能向右移动，不能向左移动
时间复杂度$O(n)$
```java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int res = Integer.MAX_VALUE;
        int[] sum = new int[nums.length + 1];
        sum[0] = 0;
        
        for (int i = 1; i < sum.length; i++) {
            sum[i] = sum[i - 1] + nums[i - 1];
        }
        
        int left = 0;
        for (int i = 0; i < nums.length; i++) {
            if (sum[i + 1] - sum[left] < s) {
                continue;
            }
            while (sum[i + 1] - sum[left + 1] >= s) {
                left++;
            }
            if (i + 1 - left < res) {
                res = i + 1 - left;
            }
        }
        return (res==Integer.MAX_VALUE) ? 0:res;
    }
}
```

##  例题 3. Longest Substring Without Repeating Characters
Given a string, find the length of the longest substring without repeating characters.

Example 1:
Input: "abcabcbb"
Output: 3 
Explanation: The answer is "abc", with the length of 3. 

Example 2:
Input: "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3. 
Note that the answer must be a substring, "pwke" is a subsequence and not a substring.

##  solution 
和上面的思路类似，当右指针向右移动时，如果出现了重复的字符，左指针向右移动，直到遇见被重复的字符，然后右指针继续向右移动一位。
举例： s = 'abcdefbwg'
右指针移动到第二个 b 的位置时，出现了重复，此时左指针向右移动，直到在index = 1 的位置遇到 b。最终左指针指向了 c， 右指针指向了 w。
```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }
        char[] ss = s.toCharArray();
        int[] occu = new int[128];
        for (int i = 0; i < 26; i++) {
            occu[i] = 0;
        }
        
        int res = 0;
        int left = 0;
        for (int right = 0; right < ss.length; right++) {
            occu[ss[right]]++;
            while (occu[ss[right]] > 1) {
                occu[ss[left]]--;
                left++;
            }
            res = Math.max(res, right - left + 1);
        }
        return res;
    }
}
```

##  例题 76. Minimum Window Substring
Given a string S and a string T, find the minimum window in S which will contain all the characters in T in complexity O(n).

Example:
Input: S = "ADOBECODEBANC", T = "ABC"
Output: "BANC"

Note:
If there is no such window in S that covers all characters in T, return the empty string "".
If there is such window, you are guaranteed that there will always be only one unique minimum window in S.

##  solution
和上面题目思路一致，右指针向右移动直到第一次substring包含所有的字符，然后再让左指针向右移动，直到刚刚好满足条件，然后右指针再向右移动，直到又遇到需要的字符，此时让左指针尝试向右移动
```java
public class Solution {
    /**
     * @param source : A string
     * @param target: A string
     * @return: A string denote the minimum window, return "" if there is no such a string
     */
    public String minWindow(String s, String t) {
        if (s == null || t == null) {
            return "";
        }
        char[] ss = s.toCharArray();
        char[] tt = t.toCharArray();
        int[] target = new int[128];
        int[] source = new int[128];
        
        for (int i = 0; i < 128; i++) {
            target[i] = 0;
            source[i] = 0;
        }
        int K = 0;
        for (char ch : tt) {
            target[ch]++;
            if (target[ch] == 1) {
                K++;
            }
        }
        int start = -1;
        int end = -1;
        int S = 0;
        int l = 0;
        int r = 0;
        
        for (l = 0; l < ss.length; l++) {
            while (S < K && r < ss.length) {
                source[ss[r]]++;
                if (source[ss[r]] == target[ss[r]]) {
                    S++;
                }
                r++;
            }
            //r实际指向真实右指针的位置的右边一位
            if (S == K) {
                if (start == -1 || r - l < end - start) {
                    start = l;
                    end = r;
                }
            }
            source[ss[l]]--;
            if (source[ss[l]] == target[ss[l]] - 1) {
                S--;
            }
        }
        if (start == -1) {
            return "";
        } else {
            return s.substring(start, end);
        }
    } 
}
```
##  Solution 
还有另一种写法：让右指针一位一位的向右移动，每次都找到最靠右的左指针。但是由于写法的问题，当正好使左右指针之间的substring满足阈值时，左指针会向右多移动一位（l++），所以返回substring时，左边的起始点需要减一。
```java
class Solution {
    public String minWindow(String s, String t) {
        if (s == null || t == null) {
            return "";
        }
        
        char[] ss = s.toCharArray();
        char[] tt = t.toCharArray();
        
        int[] target = new int[128];
        int[] source = new int[128];
        
        for (int i = 0; i < 128; i++) {
            target[i] = 0;
            source[i] = 0;
        }
        
        int K = 0;
        for (char ch : tt) {
            target[ch]++;
            if (target[ch] == 1) {
                K++;
            }
        }
        
        int start = -1;
        int end = -1;
        int S = 0;
        int l = 0;
        int r = 0;
        
        for (r = 0; r < ss.length; r++) {
            source[ss[r]]++;
            if (source[ss[r]] == target[ss[r]]) {
                S++;
            }
            if (S < K) {
                continue;
            }
            while (S >= K) {
                source[ss[l]]--;
                if (source[ss[l]] == target[ss[l]] - 1) {
                    S--;
                }
                l++;
            }
            if (start == -1 || r - l < end - start) {
                start = l - 1;
                end = r;
            }
        }
        if (start == -1) {
            return "";
        } else {
            return s.substring(start, end + 1);
        }
    }
}
```

##  例题 340. Longest Substring with At Most K Distinct Characters
Given a string, find the length of the longest substring T that contains at most k distinct characters.

Example 1:

Input: s = "eceba", k = 2
Output: 3
Explanation: T is "ece" which its length is 3.
Example 2:

Input: s = "aa", k = 1
Output: 2
Explanation: T is "aa" which its length is 2.

##  solution
对于上面这两种问题，开一个数组用来记录每个字符出现的次数，当增加到或者减小到临界值时，更新distinct的字符个数

```java
class Solution {
    public int lengthOfLongestSubstringKDistinct(String s, int k) {
        if (s == null || s.length() == 0 || k <=0) {
            return 0;
        }
        char[] ss = s.toCharArray();
        int[] occu = new int[128];
        int res = 0;
        int l = 0;
        int r = 0;
        int dist = 0;
        for (r = 0; r < s.length(); r++) {
            occu[ss[r]]++;
            if (occu[ss[r]] == 1) {
                dist++;
            }
            while (dist > k) {
                occu[ss[l]]--;
                if (occu[ss[l]] == 0) {
                    dist--;
                }
                l++;
            }
            res = Math.max(res, r - l + 1);
        }
        return res;
    }
}
```

# 快慢指针类
## 例题 19. Remove Nth Node From End of List
Given a linked list, remove the n-th node from the end of list and return its head.

Example:
Given linked list: 1->2->3->4->5, and n = 2.
After removing the second node from the end, the linked list becomes 1->2->3->5.

Note:
Given n will always be valid.

Follow up:
Could you do this in one pass?

##  Solution
需要考虑一种情况就是快指针向后先移动N位时，变成了NULL，此时去掉的Node恰好是起始点。
```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        if (head == null || n <= 0) {
            return null;
        }
        ListNode fast = head;
        ListNode slow = head;
        for (int i = 0; i < n; i++) {
            fast = fast.next;
        }
        if (fast == null) {
            return head.next;
        }
        
        while (fast.next != null) {
            slow = slow.next;
            fast = fast.next;
        }
        ListNode tmp = slow;
        slow.next = tmp.next.next;
        return  head;
    }
}
```


##  例题 876. Middle of the Linked List
Given a non-empty, singly linked list with head node head, return a middle node of linked list.
If there are two middle nodes, return the second middle node.
Example:
Input: [1,2,3,4,5]
Output: Node 3 from this list (Serialization: [3,4,5])
The returned node has value 3.  (The judge's serialization of this node is [3,4,5]).
Note that we returned a ListNode object ans, such that:
ans.val = 3, ans.next.val = 4, ans.next.next.val = 5, and ans.next.next.next = NULL.

Note:
The number of nodes in the given list will be between 1 and 100.
## Solution
```java
    public ListNode middleNode(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode fast = head;
        ListNode slow = head;
        
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
        }
        return slow;
    }
```


##  如何处理新题和 follow up
有没有类似的题目，共同点是什么
主要考察什么？算法思想还是实践
做题的过程是否顺利？是否需要再练习一遍

定期整理自己做过的题目，归纳相似的问题：
1 哪些条件可以看出时同一类的问题
2 思维上有什么相似的
3 代码上有什么相似的

##  例题 215. Kth Largest Element in an Array
Find the kth largest element in an unsorted array. Note that it is the kth largest element in the sorted order, not the kth distinct element.

Example 1:
Input: [3,2,1,5,6,4] and k = 2
Output: 5

Example 2:
Input: [3,2,3,1,2,4,5,5,6] and k = 4
Output: 4

## Solution 
最简单的方法直接排序，取出第 K 大的数。或者采用PriorityQueue

```java
class Solution {
    public Comparator<Integer> comparator = new Comparator<>() {
        public int compare (Integer a, Integer b) {
            return a - b;
        }
    };
    
    public int findKthLargest(int[] nums, int k) {
        if (nums == null || nums.length < k) {
            return -1;
        }
        Queue<Integer> queue = new PriorityQueue<>(comparator);
        
        for (int i : nums) {
            queue.add(i);
            if (queue.size() > k) {
                queue.poll();
            }
        }
        return queue.poll();
        
    }
}
```

## Solution
 采用QuickSort的算法，partition然后找到第 K 大的数
```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        return findKthLargest(nums, 0, nums.length - 1, k-1);
    }
    
    
    // partition the nums array from 'start' to 'end' section and return the K th largest number
    public int findKthLargest(int[] nums, int start, int end, int k) {
        if (start >= end) {
            return nums[k];
        }
        int left = start;
        int right = end;
        int pivot = nums[start + (end - start) / 2];
        while (left <= right) {
            while (left <= right && nums[left] > pivot) {
                left++;
            }
            while (left <= right && nums[right] < pivot) {
                right--;
            }
            if (left <= right) {
                int tmp = nums[left];
                nums[left] = nums[right];
                nums[right] = tmp;
                left++;
                right--;
            }
        }
        // judge the possible location of the solution
        // the second conditions, left >= start and right <= end deal with the case of 
        // nums been sorted between start and end, which can just return nums[k].
        // x x x x x x x 
        //             ^ ^
        //             | |
        //             r l  descending
    //   ^ ^
    //   | |
    //   r l ascending
        if (right < k && left >= start) {
            return findKthLargest(nums, left, end, k);
        } else if (left > k && right <= end) {
            return findKthLargest(nums, start, right, k);
        } else {
            return nums[k];
        }
    }
}
```

##  例题 Kth Largest in N Arrays
Description
Find K-th largest element in N arrays. 

Example 1:
Input:
k=3, [[9,3,2,4,7],[1,2,3,4,8]]
Output:
7
Explanation:
the 3rd largest element is 7

Example 2:
Input:
k = 2, [[9,3,2,4,8],[1,2,3,4,2]]
Output:
8
Explanation:
the 1st largest element is 9, 2nd largest element is 8, 3rd largest element is 4 and etc.

##  Solution
解法一： 先将每个数组排序，然后将每个数组最大的元素放入到PriorityQueue。
解法二：不预先排序，将所有的元素全部放到PriorityQueue中，取其中第 K 大的。

##  例题 378. Kth Smallest Element in a Sorted Matrix
Given a n x n matrix where each of the rows and columns are sorted in ascending order, find the kth smallest element in the matrix.
Note that it is the kth smallest element in the sorted order, not the kth distinct element.

Example:
matrix = [
   [ 1,  5,  9],
   [10, 11, 13],
   [12, 13, 15]
],
k = 8,
return 13.
Note: You may assume k is always valid, 1 ≤ k ≤ $n^2$.

##  Solution
根据行列都排好序的特征知道最小值一定在左上角，最大值一定在右下角。从左上角开始把元素加入到PriorityQueue中，然后取出一个最小的数，再加入这个数的右边一个和下边一个数。直至最后刚好取出来第 K 个数。维护一个 used 二维Boolean数组用来记录每个数是否加入到PriorityQueue，曾经加入过的就不需要反复加入了。

```java
class Solution {
    class Point {
        private int x;
        private int y;
        private int val;
        
        public Point(int x, int y, int val) {
            this.x = x;
            this.y = y;
            this.val = val;
        }
    }
    
    public Comparator<Point> comparator = new Comparator<>() {
        public int compare (Point a, Point b) {
            return a.val - b.val;
        }
    };
    
    public int kthSmallest(int[][] matrix, int k) {
        boolean[][] used = new boolean[matrix.length][matrix[0].length];
        for (int i = 0; i < used.length; i++) {
            for (int j = 0; j < used[0].length; j++) {
                used[i][j] = false;
            }
        }
        
        Queue<Point> queue = new PriorityQueue<>(comparator);
        queue.add(new Point(0, 0, matrix[0][0]));
        used[0][0] = true;
        
        int count = 0;
        while (!queue.isEmpty()) {
            Point point = queue.poll();
            count++;
            if (count == k) {
                return point.val; 
            }
            if (inBound(point.x + 1, point.y, matrix)) {
                Point right = new Point(point.x + 1, point.y, matrix[point.y][point.x + 1]);
                if (!used[right.y][right.x]) {
                    queue.add(right);
                    used[right.y][right.x] = true;
                }
            }
            if (inBound(point.x, point.y + 1, matrix)) {
                Point bot = new Point(point.x, point.y + 1, matrix[point.y + 1][point.x]);
                if (!used[bot.y][bot.x]) {
                    queue.add(bot);
                    used[bot.y][bot.x] = true;
                }
            }
        }
        return -1;
    }
    
    public boolean inBound(int x, int y, int[][] matrix) {
        if (x < 0 || x >= matrix[0].length) {
            return false;
        }
        if (y < 0 || y >= matrix.length) {
            return false;
        }
        return true;
    }
}
```

##  例题 373. Find K Pairs with Smallest Sums
You are given two integer arrays nums1 and nums2 sorted in ascending order and an integer k.

Define a pair (u,v) which consists of one element from the first array and one element from the second array.

Find the k pairs (u1,v1),(u2,v2) ...(uk,vk) with the smallest sums.

Example 1:
Input: nums1 = [1,7,11], nums2 = [2,4,6], k = 3
Output: [[1,2],[1,4],[1,6]] 
Explanation: The first 3 pairs are returned from the sequence: 
             [1,2],[1,4],[1,6],[7,2],[7,4],[11,2],[7,6],[11,4],[11,6]

Example 2:
Input: nums1 = [1,1,2], nums2 = [1,2,3], k = 2
Output: [1,1],[1,1]
Explanation: The first 2 pairs are returned from the sequence: 
             [1,1],[1,1],[1,2],[2,1],[1,2],[2,2],[1,3],[1,3],[2,3]

Example 3:
Input: nums1 = [1,2], nums2 = [3], k = 3
Output: [1,3],[2,3]
Explanation: All possible pairs are returned from the sequence: [1,3],[2,3]

##  Solution
和上面一道题的思路时一样的。有两个数组，长度分别为 m, n，可以构成一个m x n的矩阵，第一行为第一个数组的第一个元素加上第二个数组的每个元素，第 i 行为第一个数组的第 i 个元素加上第二个数组的每个元素。

```java
class Solution {
    public class Point{
        int x;
        int y;
        int val;
        
        public Point(int x, int y, int val) {
            this.x = x;
            this.y = y;
            this.val = val;
        }
    }
    
    public Comparator<Point> comparator = new Comparator<>() {
        public int compare(Point a, Point b) {
            return a.val - b.val;
        }
    };
    
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> res = new ArrayList<>();
        int m = nums1.length;
        int n = nums2.length;
        if (nums1 == null || nums2 == null || nums1.length == 0 || nums2.length == 0) {
            return res;
        }

        boolean[][] used = new boolean[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                used[i][j] = false;
            }
        }
        Queue<Point> queue = new PriorityQueue<>(comparator);
        queue.add(new Point(0,0,nums1[0]+nums2[0]));
        used[0][0] = true;
        
        int[] dx = {0, 1};
        int[] dy = {1, 0};
        int count = 0;
        while (!queue.isEmpty() && count < k) {
            Point point = queue.poll();
            int x = point.x;
            int y = point.y;            
            res.add(new ArrayList<Integer>(Arrays.asList(nums1[y],nums2[x])));
            count++;
            for (int j = 0; j < 2; j++) {
                if (x + dx[j] < n && y + dy[j] < m && !used[y + dy[j]][x + dx[j]]) {
                    Point tmp = new Point(x + dx[j], y + dy[j], nums1[y + dy[j]] + nums2[x + dx[j]]);
                    used[y + dy[j]][x + dx[j]] = true;
                    queue.add(tmp);
                }
                
            }
        }
        return res;
    }
}
```

> **Follow Up里面有很重要的一类就是变换一下数据结构，比如说数组，链表，二叉树等**