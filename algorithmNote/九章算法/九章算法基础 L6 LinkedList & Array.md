#  九章算法基础 L6 LinkedList & Array
[TOC]



#  Dummy Node

Dummy Node 在l LnkedList 问题中，head 可能发生变化时，添加一个 dummy node 在前面。最后 return head 时，只需要 return dummyNode.next


##  例题 206. Reverse Linked List
Reverse a singly linked list.

Example:
Input: 1->2->3->4->5->NULL
Output: 5->4->3->2->1->NULL

Follow up:
A linked list can be reversed either iteratively or recursively. Could you implement both?

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head ==null) {
            return null;
        }
        
        ListNode prev = null;
        ListNode cur = head;
        while (cur != null) {
            ListNode tmp = cur.next;
            cur.next = prev;
            prev = cur;
            cur = tmp;
        }
        return prev;
    }
}
```
##  例题 92. Reverse Linked List II
Reverse a linked list from position m to n. Do it in one-pass.
Note: 1 ≤ m ≤ n ≤ length of list.

Example:
Input: 1->2->3->4->5->NULL, m = 2, n = 4
Output: 1->4->3->2->5->NULL

```java
        public ListNode reverseBetween(ListNode head, int m, int n) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        
        int index = 0;
        ListNode ptr = dummy;
        ListNode start = null;
        while (index < m) {
            start = ptr;
            ptr = ptr.next;
            index++;
        }
        // ptr is the m-th node
        // start is the node before ptr
        ListNode cur = ptr.next;
        ListNode prev = ptr;
        // start -> node1 -> node2...-> node(m-n) -> cur...
        // start -> node(m-n)...-> node2 -> node1 -> cur...
        while (index < n) {
            ListNode tmp = cur.next;
            cur.next = prev;
            prev = cur;
            cur = tmp;
            index++;
        }
        // when finish, prev is the last node that needs to be reversed
        // cur is the node after prev
        start.next = prev;
        ptr.next = cur;
        return dummy.next;
    }
```

##  例题 25. Reverse Nodes in k-Group
Given a linked list, reverse the nodes of a linked list k at a time and return its modified list.

k is a positive integer and is less than or equal to the length of the linked list. If the number of nodes is not a multiple of k then left-out nodes in the end should remain as it is.

Example:
Given this linked list: 1->2->3->4->5
For k = 2, you should return: 2->1->4->3->5
For k = 3, you should return: 3->2->1->4->5

Note:
1. Only constant extra memory is allowed.
2. You may not alter the values in the list's nodes, only nodes itself may be changed.

##  solution
解法和
```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if (head == null) return null;
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode prev = dummy;
        while (prev != null) {
            prev = reverseK(prev, k);
        }
        return dummy.next;
    }
    
    // reverse the nodes after prev, k nodes in a time
    // return the prev node for the next round
    // prev -> n1 -> n2... -> nk-1 -> nk -> nk+1
    // prev -> nk -> nk-1... -> n2 -> n1 -> nk+1
    // return n1
    public ListNode reverseK(ListNode prev, int k) {
        if (prev == null) {
            return null;
        }
        
        ListNode test = prev;
        for (int i = 0; i < k; i++) {
            if (test.next == null) {
                return null;
            }
            test = test.next;
        }
        
        ListNode first = prev.next;
        ListNode start = first;
        ListNode cur = first.next;
        // with k nodes, k-1 swaps needed
        // thus initialize count as 1
        int count = 1;
        
        while(count < k) {
            ListNode tmp = cur.next;
            cur.next = first;
            first = cur;
            cur = tmp;
            count++;
        }
        prev.next = first;
        start.next = cur;
        return start;
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200109143226770.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)

#  High Frequency
##  21. Merge Two Sorted Lists
Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

Example:
Input: 1->2->4, 1->3->4
Output: 1->1->2->3->4->4

##  solution
```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) return l2;
        if (l2 == null) return l1;
        
        ListNode dummy = new ListNode(Integer.MIN_VALUE);
        ListNode ptr = dummy;
        ListNode h1 = l1;
        ListNode h2 = l2;
        
        while (h1 != null && h2 != null) {
            if (h1.val < h2.val) {
                ptr.next = h1;
                ptr = ptr.next;
                h1 = h1.next;
            } else {
                ptr.next = h2;
                ptr = ptr.next;
                h2 = h2.next;
            }
        }
        if (h1 == null) {
            ptr.next = h2;
        }
        if (h2 == null) {
            ptr.next = h1;
        }
        return dummy.next;
    }
}
```



##  例题 138. Copy List with Random Pointer
A linked list is given such that each node contains an additional random pointer which could point to any node in the list or null.
Return a deep copy of the list.

The Linked List is represented in the input/output as a list of n nodes. Each node is represented as a pair of [val, random_index] where:
1. val: an integer representing Node.val
2. random_index: the index of the node (range from 0 to n-1) where random pointer points to, or null if it does not point to any node.

 Example 1:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200109143642723.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
Input: head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
Output: [[7,null],[13,0],[11,4],[10,2],[1,0]]

Example 2:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200109143710564.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
Input: head = [[1,1],[2,1]]
Output: [[1,1],[2,1]]

Example 3:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200109143738220.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
Input: head = [[3,null],[3,0],[3,null]]
Output: [[3,null],[3,0],[3,null]]

Example 4:
Input: head = []
Output: []
Explanation: Given linked list is empty (null pointer), so return null.

Constraints:
1. -10000 <= Node.val <= 10000
2. Node.random is null or pointing to a node in the linked list.
3. Number of Nodes will not exceed 1000.

##  solution 1
解法一采用了 HashMap，额外空间为 $O(N)$.
```java
class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) {
            return null;
        }
        
        HashMap<Node, Node> copy = new HashMap<>();
        Node start = head;
        Node res = head;
        
        // copy the nodes
        while (head != null) {
            copy.put(head, new Node(head.val));
            head = head.next;
        }
        
        // copy the link (next, random)
        while (start != null) {
            copy.get(start).next = copy.get(start.next);
            copy.get(start).random = copy.get(start.random);
            start = start.next;
        }
        return copy.get(res);
    }
}
```
##  solution 2
解法二采用 in-place 的算法，额外空间为 $O(N)$。这道题的算法很难想到，==直接背下来==
假设原始的链表结构是 
```java
1 -> 2 -> 3 -> 4
```
现在把每一copy都插入到被copy的节点的后面
```java
1 -> 1' -> 2 -> 2' -> 3 -> 3' -> 4 -> 4'
```
于是整个解法分为三个步骤
1. copy 每个节点并且把点插入到相应位置
2. 给每一个新节点添加 .random link  ==start.next.random = start.random.next;==
3. 拆分链表，把1 -> 1' -> 2 -> 2' -> 3 -> 3' -> 4 -> 4' 变回 1 -> 2 -> 3 -> 4 和 1' -> 2' -> 3' -> 4'

尤其需要注意的是链表中是否 null 的判断

```java
class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) {
            return null;
        }
         
        // insert the new nodes
        Node start = head;
        while (start != null) {
            Node tmp = start.next;
            start.next = new Node(start.val);
            start.next.next = tmp;
            start = start.next.next;
        }
        
        start = head;
        // add the .random links
        while (start != null) {
            if (start.random == null) {
                start.next.random = null;
            } else {
                start.next.random = start.random.next;
            }
            start = start.next.next;
        }
        
        // split the list
        Node newHead = head.next;
        Node newPtr = newHead;
        Node oldPtr = head;
        // old -> new -> ...
        // 1 -> 1' -> 2 -> 2' -> 3 -> 3'
        while (oldPtr != null) {
            if (newPtr.next != null) {
                oldPtr.next = oldPtr.next.next;
                newPtr.next = oldPtr.next.next;
            } else {
                oldPtr.next = null;
                newPtr.next = null;
            }
            newPtr = newPtr.next;
            oldPtr = oldPtr.next;
        } 
        return newHead;
    }
}
```
##  例题 141. Linked List Cycle
Given a linked list, determine if it has a cycle in it.
To represent a cycle in the given linked list, we use an integer pos which represents the position (0-indexed) in the linked list where tail connects to. If pos is -1, then there is no cycle in the linked list.

 Example 1:
Input: head = [3,2,0,-4], pos = 1
Output: true
Explanation: There is a cycle in the linked list, where tail connects to the second node.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200112004646545.png)
Example 2:
Input: head = [1,2], pos = 0
Output: true
Explanation: There is a cycle in the linked list, where tail connects to the first node.
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020011200560839.png)
Example 3:
Input: head = [1], pos = -1
Output: false
Explanation: There is no cycle in the linked list.

Follow up:
Can you solve it using O(1) (i.e. constant) memory?

##  solution 1
空间复杂度为 $O(N)$
```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null) return false;
        HashSet<ListNode> set = new HashSet<>();
        while (head != null) {
            if (!set.contains(head)) {
                set.add(head);
            } else {
                return true;
            }
            head = head.next;
        }
        return false;
    }
}
```
##  solution 2
==解法直接背下来，没必要琢磨==
定义两个指针，一个快指针，一个慢指针，慢指针向后移动一位，快指针移动两位。如果出现快慢指针指到相同位置就说明有环
空间复杂度为 $O(1)$
```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null) return false;
        ListNode fast = head;
        ListNode slow = head;
        while (fast != null) {
            if (fast.next == null || fast.next.next == null) {
                return false;
            }
            fast = fast.next.next;
            slow = slow.next;
            if (fast.equals(slow)) {
                return true;
            }
        }
        return false;
    }
}
```
##  例题 142. Linked List Cycle II
Given a linked list, return the node where the cycle begins. If there is no cycle, return null.

To represent a cycle in the given linked list, we use an integer pos which represents the position (0-indexed) in the linked list where tail connects to. If pos is -1, then there is no cycle in the linked list.

Note: Do not modify the linked list.

##  solution
和上面一题的区别在于不断要判断是否有 cycle，同时要返回cycle起始点。
==解法直接背下来，没必要琢磨==
当快慢指针相遇之后，从head重新定义一个慢指针，这个慢指针和最开始的慢指针相遇的点就是 cycle 起始点
```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if(head == null) return null;
        ListNode fast = head;
        ListNode slow = head;
        
        while (fast != null) {
            if (fast.next == null || fast.next.next == null) {
                return null;
            }
            fast = fast.next.next;
            slow = slow.next;
            if (fast.equals(slow)) {
                break;
            }
        }
        
        ListNode secSlow = head;
        while (!secSlow.equals(slow)) {
            slow = slow.next;
            secSlow = secSlow.next;
        }
        return slow;
    }
}
```

##  follow up
还有一个相关的问题，就是判断两个链表是否有相交部分。
如果有相交部分一定是，从某一个点开始，两个链表重合了。假设有 L1，L2 两个链表，遍历 L1 到结尾，然后把 L1 结尾和 L2 的 head 连在一起，如果两个链表有重合，那么这样得到的链表一定有 cycle。于是这个问题就转换为判断是否有 cycle 的问题


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200112011717589.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)

##  例题 148. Sort List
Sort a linked list in O(n log n) time using constant space complexity.

Example 1:
Input: 4->2->1->3
Output: 1->2->3->4

Example 2:
Input: -1->5->3->4->0
Output: -1->0->3->4->5

##  solution merge sort
这题可以采用 merge sort， 因为链表的 merge sort 可以保证额外空间为 $O(1)$，虽然在数组中必须定义一个 aux 数组来储存，导致额外空间为$O(N)$。
和 array 的merge sort写法类似，需要注意的是取中点时需要定义一个 function，采用快慢指针找到中点，同时 fast = head.next，否则出现 stack overflow。因为假设加入了一个长度为 2 的 linked list。如果 fast = head; slow = head。 最终返回的slow 就是 linked list 的结尾，等于没有起到一分为二的作用，整个 recursion 陷入死循环。

```java
class Solution {
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode mid = findMiddle(head);
        ListNode l2 = sortList(mid.next);
        mid.next = null;
        ListNode l1 = sortList(head);
        return merge(l1, l2);
    }
    
    public ListNode findMiddle (ListNode head) {
        ListNode fast = head.next; // fast must be head.next. otherwise stackoverflow 
        ListNode slow = head;
        
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
        }
        return slow;
    }
    
    public ListNode merge(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(0);
        ListNode ptr = dummy;
        while (true) {
            if (l1 == null || l2 == null) {
                break;
            }
            if (l1.val <= l2.val) {
                ptr.next = l1;
                l1 = l1.next;
                ptr = ptr.next;
            } else if (l1.val > l2.val) {
                ptr.next = l2;
                l2 = l2.next;
                ptr = ptr.next;
            }
        }
        if (l1 == null) {
            ptr.next = l2;
        }
        if (l2 == null) {
            ptr.next = l1;
        }
        return dummy.next;
    }
}
```

##  solution quick sort
这题也可以采用 quick sort，但是写起来会较麻烦一些。因为涉及到链表元素的交换 swap。



#  Array
##  Sorted Array
##  例题 88. Merge Sorted Array
Given two sorted integer arrays nums1 and nums2, merge nums2 into nums1 as one sorted array.

Note:
The number of elements initialized in nums1 and nums2 are m and n respectively.
You may assume that nums1 has enough space (size that is greater or equal to m + n) to hold additional elements from nums2.

Example:
Input:
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],          n = 3

Output: [1,2,2,3,5,6]

##  solution
```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        if (nums1 == null || nums2 == null) {
            return;
        }
        if (nums2.length != n || n <= 0 || m + n > nums1.length) {
            return;
        }
        int[] aux = new int[m + n];
        for (int i = 0; i < m; i++) {
            aux[i] = nums1[i];
        }
        int tmp = 0;
        for (int i = m; i < m + n; i++) {
            aux[i] = nums2[tmp++]; 
        }
        
        int ptr1 = 0, ptr2 = m;
        for (int i = 0; i < m + n; i++) {
            if (ptr1 == m) {
                nums1[i] = aux[ptr2++];
            } else if (ptr2 == nums1.length) {
                nums1[i] = aux[ptr1++];
            } else if (aux[ptr1] <= aux[ptr2]) {
                nums1[i] = aux[ptr1++];
            } else if (aux[ptr1] > aux[ptr2]) {
                nums1[i] = aux[ptr2++];
            }
        }
    }
}
```
上面的做法利用了 auxiliary array，所以空间复杂度为$O(m + n)$。

如果想写出一个空间复杂度为$O(1)$的算法，可以采用下面的解法。这个解法是从后往前比较哪个数更大。更大的数放在后面，可以这样做的原因是因为 nums1 的后面留有空位，所以改变后面的值不会损失任何信息

```java
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        if (nums1 == null || nums2 == null) {
            return;
        }
        if (nums2.length != n || n <= 0 || m + n > nums1.length) {
            return;
        }
        
        int ptr1 = m - 1;// nums1
        int ptr2 = n - 1;// nums2
        
        for (int i = m + n - 1; i >= 0; i--) {
            if (ptr1 < 0) {
                nums1[i] = nums2[ptr2--];
            } else if (ptr2 < 0) {
                nums1[i] = nums1[ptr1--];
            } else if (nums1[ptr1] >= nums2[ptr2]) {
                nums1[i] = nums1[ptr1--];
            } else if (nums1[ptr1] < nums2[ptr2]) {
                nums1[i] = nums2[ptr2--];
            }
        }
    }
```
## 例题 349. Intersection of Two Arrays
Given two arrays, write a function to compute their intersection.

Example 1:
Input: nums1 = [1,2,2,1], nums2 = [2,2]
Output: [2]

Example 2:
Input: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
Output: [9,4]

Note:
Each element in the result must be unique.
The result can be in any order.

##  solution
```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        HashSet<Integer> set = new HashSet<>();
        for (int i = 0; i < nums1.length; i++) {
            set.add(nums1[i]);
        }
        HashSet<Integer> res = new HashSet<>();
        for (int i = 0; i < nums2.length; i++) {
            if (set.contains(nums2[i])) {
                res.add(nums2[i]);
            }
        }
        int[] result = new int[res.size()];
        int i = 0;
        for (Integer tmp : res) {
            result[i++] = tmp;
        }
        return result;
    }
    
    /*
    // try the case when nums1 and nums2 are sorted. time O(N) space O(1)
    public int[] intersection(int[] nums1, int[] nums2) {
        // [1,1,2,2]
        // [2,2]
        int m = nums1.length;
        int n = nums2.length;

        int[] res = new int[Math.min(m, n)];
        int index = 0;
        int ptr1 = 0, ptr2 = 0;
        while (ptr1 < m && ptr2 < n) {
            while (ptr1 < m - 1 && nums1[ptr1] == nums1[ptr1 + 1]) {
                ptr1++;
            }
            while (nums2[ptr2] < nums1[ptr1]) {
                ptr2++;
            }
            if (nums2[ptr2] == nums1[ptr1]) {
                res[index++] = nums1[ptr1++];
                ptr2++;
            } else if (nums2[ptr2] > nums1[ptr1]) {
                ptr1++;
            }
        }
        return Arrays.copyOf(res, index);
    }
    */
    
    // solution 2
    // mergesort + merge
    // space O(n)
    // time (nlogn + mlongm + m + n)
    
    // solution 3
    // quicksort + merge
    // space O(1)
    // time (nlogn + mlongm + m + n)
    
    // solution 4
    // quick sort one of the array, traverse the other array and 
    // use binary search to search whether it eists in the other array
    // space O(1)
    // time O(nlogn + mlogn) = O((m + n)logn)
    // space O(1)
}
```
这道题的解法很多，每种解法的时间空间复杂度都略有不同


##  例题 4. Median of Two Sorted Arrays
There are two sorted arrays nums1 and nums2 of size m and n respectively.

Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

You may assume nums1 and nums2 cannot be both empty.

Example 1:
nums1 = [1, 3]
nums2 = [2]
The median is 2.0

Example 2:
nums1 = [1, 2]
nums2 = [3, 4]
The median is (2 + 3)/2 = 2.5

##  solution 1 
最直观的解法是采用 merge 找到中位数
这种解法的时间和空间复杂度都是 $O(m+n)$
```java
class Solution {
    // time O(m+n)
    // space O(m+n)
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int left = 0;
        int right = 0;
        int m = nums1.length;
        int n = nums2.length;
        int[] res = new int[m+n];
        if ( m == 0 && n == 0) throw new IllegalArgumentException("Invalid input");
        else if (m == 0 && n%2 == 0) return (nums2[n/2-1]+nums2[n/2])/2.0;
        else if (m == 0) return nums2[n/2];
        else if (n == 0 && m % 2 ==0) return (nums1[m/2-1]+nums1[m/2])/2.0;
        else if (n == 0) return nums1[m/2];
        else {
            int index = 0;
            while (left < m || right < n) {
                if (nums1[left] <= nums2[right])
                    res[index++] = nums1[left++];
                else
                    res[index++] = nums2[right++];
                if (left == m) {
                    for (int i = right; i < n; i++) {
                        res[index++] = nums2[i];
                    }
                    break;
                }

                if (right == n) {
                    for (int i = left; i < m; i++) {
                        res[index++] = nums1[i];
                    }
                    break;
                }
            }
        }
        return ((m+n)%2==0)? (res[(m+n)/2]+res[(m+n)/2-1])/2.0 : res[(m+n)/2];
    }
}
```

##  solution 2
采用 binary search。时间复杂度为 $O(log(min(n,m)))$
由于两个 array 都是 sorted， 所以 array 右边的值一定比左边的大。求中位数相当于求一个数使得比它小和比它大的数的个数相等。假设我们在两个任意的位置$(i,j)$分别断开两个数组
```java
A[0] A[1] A[2] ... A[i-1]    |   A[i] A[i+1] ... A[m-1]
B[0] B[1] B[2] ... B[j-1]    |   B[j] B[j+1] ... B[n-1]
```
同时使得被分开的左边部分**全部都**小于右边部分，此时就找到了中位数，如果总长度是奇数，那么左半边的最大值就是中位数；如果中长度是偶数那么左边最大值和右边最小值的平均值就是中位数。
$(i,j)$需要满足条件 $i+j = m-i+n-j$或者$i+j = m-i+n-j+1$
由于知道$i$就可以求$j$，所以问题变成在$[0, m]$的区间 binary search $i$。
![](https://img-blog.csdnimg.cn/20200121024823953.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
```java
class Solution {
    public double findMedianSortedArrays(int[] A, int[] B) {
        int m = A.length;
        int n = B.length;
        if (m > n) { // to ensure m<=n
            int[] temp = A; A = B; B = temp;
            int tmp = m; m = n; n = tmp;
        }
        int iMin = 0, iMax = m, halfLen = (m + n + 1) / 2;
        while (iMin <= iMax) {
            int i = (iMin + iMax) / 2;
            int j = halfLen - i;
            if (i < iMax && B[j-1] > A[i]){
                iMin = i + 1; // i is too small
            }
            else if (i > iMin && A[i-1] > B[j]) {
                iMax = i - 1; // i is too big
            }
            else { // i is perfect
                int maxLeft = 0;
                if (i == 0) { maxLeft = B[j-1]; }
                else if (j == 0) { maxLeft = A[i-1]; }
                else { maxLeft = Math.max(A[i-1], B[j-1]); }
                if ( (m + n) % 2 == 1 ) { return maxLeft; }

                int minRight = 0;
                if (i == m) { minRight = B[j]; }
                else if (j == n) { minRight = A[i]; }
                else { minRight = Math.min(B[j], A[i]); }

                return (maxLeft + minRight) / 2.0;
            }
        }
        return 0.0;
    }
}
```

## Subarray
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200121031133281.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
PrefixSum可以用来计算任意一段连续区间的元素和
比如说计算从 $i$ 到 $j$ 的元素和，$PS[j+1] - PS[i]$

##  例题 53. Maximum Subarray
Given an integer array nums, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

Example:
Input: [-2,1,-3,4,-1,2,1,-5,4],
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.

Follow up:
If you have figured out the O(n) solution, try coding another solution using the divide and conquer approach, which is more subtle.

##  solution 1
这道题的本质就是要求$Max[sum(i,j) = preSum(j+1) - preSum(i)]$
但是需要注意的是不可以直接求出 prefixSum 数组，然后找到其中的最大最小值相减，因为 prefixSum 数组第一个数总是0，这样对于全是负数的 input 数组会出现问题
由于是求两个参数相减的最大值$(x-y)$，可以固定一个，然后遍历另一个。为了使得结果更大，需要确保 y最小，于是遍历 x 的过程中，maintain 一个参数记录到当前为止， y 的最小值。最后求出差值的最大值。
```java
class Solution {
    // time O(n)
    public int maxSubArray(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }   
        int sum = 0;
        int max = Integer.MIN_VALUE;
        int minSum = 0;
        for (int i = 0; i < nums.length; i++) {
            sum += nums[i];
            max = Math.max(sum - minSum, max);
            minSum = Math.min(sum, minSum);
        }
        return max;
    }
}
```

##  solution 2 DP
采用DP的算法。在 input array 上面 in-place 的修改元素值，元素值代表当前的最大 sum 值，同时 maintain 一个参数记录到当前位置的的最大 sum 值。由于是 in-place 的，空间复杂度为$O(1)$。

```java
class Solution {
    // DP
  public int maxSubArray(int[] nums) {
    int n = nums.length, maxSum = nums[0];
    for(int i = 1; i < n; ++i) {
      if (nums[i - 1] > 0) nums[i] += nums[i - 1];
      maxSum = Math.max(nums[i], maxSum);
    }
    return maxSum;
  }
}
```

##  例题 Subarray Sum Closest
Given an integer array, find a  subarray with sum closest to zero. Return the indexes of the first number and last number.

Example
Given [-3, 1, 1, -3, 5], return [0,2], [1, 3], [1, 1], [2, 2]