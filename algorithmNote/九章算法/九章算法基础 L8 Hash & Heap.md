#  九章算法基础 L8 Hash & Heap
[TOC]



##  Hash Function

magic number, Hash table size
index = (num * magic number) % HashTableSize

##  Closed Addressing
use linked list to resolve collision
##  Open Addressing

 - Linear probing
 - Quadratic probing

##  例题 146. LRU Cache
Design and implement a data structure for Least Recently Used (LRU) cache. It should support the following operations: get and put.

get(key) - Get the value (will always be positive) of the key if the key exists in the cache, otherwise return -1.
put(key, value) - Set or insert the value if the key is not already present. When the cache reached its capacity, it should invalidate the least recently used item before inserting a new item.
The cache is initialized with a positive capacity.

Follow up:
Could you do both operations in O(1) time complexity?

Example:
LRUCache cache = new LRUCache( 2 /* capacity */ );
cache.put(1, 1);
cache.put(2, 2);
cache.get(1);       // returns 1
cache.put(3, 3);    // evicts key 2
cache.get(2);       // returns -1 (not found)
cache.put(4, 4);    // evicts key 1
cache.get(1);       // returns -1 (not found)
cache.get(3);       // returns 3
cache.get(4);       // returns 4

##  solution 
```java
class LRUCache {
    private class Node{
        Node prev;
        Node next;
        int key;
        int value;
        public Node(int key, int value) {
            this.next = null;
            this.prev = null;
            this.key = key;
            this.value = value;
        }
    }
    
    private int capacity;
    private Node head = new Node(-1, -1);
    private Node tail = new Node(-1, -1);
    private HashMap<Integer, Node> hs = new HashMap<Integer, Node>();

    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.tail.prev = this.head;
        this.head.next = tail;
    }
    
    public int get(int key) {
        if (!hs.containsKey(key)) {
            return -1;
        }
        
        Node curr = hs.get(key);
        curr.prev.next = curr.next;
        curr.next.prev = curr.prev;
        
        tail.prev.next = curr;
        curr.prev = tail.prev;
        curr.next = tail;
        tail.prev = curr;
        
        return curr.value;
    }
    
    public void put(int key, int value) {
        if (get(key) != -1) {
            hs.get(key).value = value;
            return;
        }
        
        if (hs.size() == capacity) {
            hs.remove(head.next.key);
            head.next = head.next.next;
            head.next.prev = head;
        }
        
        Node node = new Node(key, value);
        hs.put(key, node);
        
        tail.prev.next = node;
        node.prev = tail.prev;
        node.next = tail;
        tail.prev = node;
    }
}
/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```
#  Heap
##  PriorityQueue vs. Heap
Heap Add: O(logN); Remove O(logN); Min or Max O(1);
PriorityQueue Add: O(logN); Remove O(N); Min or Max O(1);
在Java中PriorityQueue就是用Heap实现的

##  例题 264. Ugly Number II
Write a program to find the n-th ugly number.
Ugly numbers are positive numbers whose prime factors only include 2, 3, 5. 

Example:
Input: n = 10
Output: 12

Explanation: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 is the sequence of the first 10 ugly numbers.

Note:  
1. 1 is typically treated as an ugly number.
2. n does not exceed 1690.

```java
class Solution {
    // remember to deal with overflow
    // use Long instead of Integer
    public int nthUglyNumber(int n) {
        Queue<Long> queue = new PriorityQueue();
        HashSet<Long> set = new HashSet<>();
        queue.offer(1L);
        int counter = 0;
        Long[] multip = {2L,3L,5L};
        
        while (!queue.isEmpty()) {
            Long num = queue.poll();
            counter++;
            if (counter == n) {
                return num.intValue();
            }
            for (Long tmp : multip) {
                Long temp = num * tmp;
                if (!set.contains(temp)) {
                    set.add(temp);
                    queue.offer(temp);                    
                }
            } 
        }
        return 0;
    }
}
```

##  例题 215. Kth Largest Element in an Array
Find the kth largest element in an unsorted array. Note that it is the kth largest element in the sorted order, not the kth distinct element.

Example 1:
Input: [3,2,1,5,6,4] and k = 2
Output: 5

Example 2:
Input: [3,2,3,1,2,4,5,5,6] and k = 4
Output: 4

Note:
You may assume k is always valid, 1 ≤ k ≤ array's length.

##  solution 1
最简单的就是直接排序，然后找到第 K 大的数，或者使用PriorityQueue，本质上没有区别
```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        if (nums == null || nums.length < k) {
            return -1;
        }
        
        PriorityQu eue<Integer> queue = new  PriorityQueue<>();
        for (int i = 0; i < nums.length; i++) {
            queue.offer(nums[i]);
        }
        int counter = 0;
        int num;
        while (true) {
            num = queue.poll();
            counter++;
            if (counter == nums.length + 1 - k) {
                break;
            }
        }
        return num;
    }
}
```

##  solution 2
同样采用 Priority Queue， 但是区别在于Queue的大小发生了变化。由于只需要取第 k 大的数，其他小于这个的数都可以扔掉。所以保持 Queue 的大小在 K。当超过的时候就把 Queue中最小的数 poll 出去

```java
    public int findKthLargest(int[] nums, int k) {
        if (nums == null || nums.length < k) {
            return -1;
        }
        PriorityQueue<Integer> queue = new  PriorityQueue<>();
        
        for (int i = 0; i < nums.length; i++) {
            queue.add(nums[i]);
            if (queue.size() > k) {
                queue.poll();
            }
        }
        return queue.poll();
    }
    /*
	Arrays.sort(b, new Comparator<Integer>() {
	    @Override
	    public int compare(Integer a, Integer b) {
	        return a - b;
	    }
	});
	*/
```