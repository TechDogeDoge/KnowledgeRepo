# 九章算法强化 L3 Heap & Stack
[TOC]

支持 

1. Min/Max: $O(1)$
2. Push:  $O(logN)$
3. Pop: $O(logN)$
Java中的Heap就是PriorityQueue

## 例题 295. Find Median from Data Stream
Median is the middle value in an ordered integer list. If the size of the list is even, there is no middle value. So the median is the mean of the two middle value.

For example,
[2,3,4], the median is 3
[2,3], the median is (2 + 3) / 2 = 2.5

Design a data structure that supports the following two operations:
**void addNum(int num)** - Add a integer number from the data stream to the data structure. 
**double findMedian()** - Return the median of all elements so far.

Example:
addNum(1)
addNum(2)
findMedian() -> 1.5
addNum(3) 
findMedian() -> 2

Follow up:
If all integer numbers from the stream are between 0 and 100, how would you optimize it?
If 99% of all integer numbers from the stream are between 0 and 100, how would you optimize it?

## Solution
创建两个堆(PriorityQueue)用来维护所有的数字，分别叫最大堆和最小堆。最大堆用来保存较小的数，poll()会返回堆中的最大数，需要自定义comparator；最小堆用来保存较大的数，poll()会返回堆中的最小数。
注意：Priority默认按照从小到大的顺序排列，是个最小堆，root的值是最小的
还需要注意由于初始化的时候利用了Integer.MAX_VALUE和Integer.MIN_VALUE，所以自定义的comparator不能直接把compare函数的返回值写坐 b - a， 而应该写做 b.compareTo(a)。

```java
class MedianFinder {
    PriorityQueue<Integer> minHeap; // store the larger half, poll the minimum
    PriorityQueue<Integer> maxHeap; // store the smaller half, poll the maximum
    int size;
    
    public Comparator<Integer> comparator = new Comparator<>() {
        public int compare(Integer a, Integer b) {
            return b.compareTo(a);
        }
    };
    
    /** initialize your data structure here. */
    public MedianFinder() {
        minHeap = new PriorityQueue<Integer>();
        maxHeap = new PriorityQueue<Integer>(comparator);
        minHeap.add(Integer.MAX_VALUE);
        maxHeap.add(Integer.MIN_VALUE);
        size = 0;
    }
    
    public void addNum(int num) {
        size = size + 1;
        if (maxHeap.size() > minHeap.size()) {
            if (num < maxHeap.peek()) {
                int tmp = maxHeap.poll();
                minHeap.add(tmp);
                maxHeap.add(num);
            } else {
                minHeap.add(num);
            }
        } else {
            if (num < minHeap.peek()) {
                maxHeap.add(num);
            } else {
                int tmp = minHeap.poll();
                maxHeap.add(tmp);
                minHeap.add(num);
            }
        }
    }
    
    public double findMedian() {
        if (size % 2 == 0) {
            return maxHeap.peek() * 0.5 + minHeap.peek() * 0.5;
        } else {
            return maxHeap.peek();
        }
    }
}
```

## 例题 480. Sliding Window Median
Median is the middle value in an ordered integer list. If the size of the list is even, there is no middle value. So the median is the mean of the two middle value.

Examples:
[2,3,4] , the median is 3
[2,3], the median is (2 + 3) / 2 = 2.5

Given an array nums, there is a sliding window of size k which is moving from the very left of the array to the very right. You can only see the k numbers in the window. Each time the sliding window moves right by one position. Your job is to output the median array for each window in the original array.

For example,
Given nums = [1,3,-1,-3,5,3,6,7], and k = 3.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200425101340970.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70#pic_center =400x300)
Therefore, return the median sliding window as [1,-1,-1,3,5,6].

Note:
You may assume k is always valid, ie: k is always smaller than input array's size for non-empty array.
Answers within 10^-5 of the actual value will be accepted as correct.

## Solution 
使用最大堆最小堆，window向右移动的时候，向其中插入一个数，去除一个数

```java
class Solution {
    public Comparator<Integer> comparator = new Comparator<>() {
        public int compare(Integer a, Integer b) {
            return b.compareTo(a);
        }
    };
    PriorityQueue<Integer> minHeap = new PriorityQueue<Integer>();
    PriorityQueue<Integer> maxHeap = new PriorityQueue<Integer>(comparator);
    int size;
    
    
    public double[] medianSlidingWindow(int[] nums, int k) {
        if (nums == null || nums.length < k || k <= 0) {
            return null;
        }
        size = 0;
        minHeap.add(Integer.MAX_VALUE);
        maxHeap.add(Integer.MIN_VALUE);
        while (size < k) {
            addNum(nums[size]);
            size++;
        }
        double[] result = new double[nums.length - k + 1];
        for (int i = 0; i < result.length; i++) {
            result[i] = findMedian();
            if (i == result.length - 1) break;
            removeNum(nums[i]);
            addNum(nums[i+k]);
        }
        return result;
    }
    
    public void addNum(int num) {
        size++;
        if (maxHeap.size() > minHeap.size()) {
            if (num < maxHeap.peek()) {
                int tmp = maxHeap.poll();
                minHeap.add(tmp);
                maxHeap.add(num);
            } else {
                minHeap.add(num);
            }
        } else {
            if (num < minHeap.peek()) {
                maxHeap.add(num);
            } else {
                int tmp = minHeap.poll();
                maxHeap.add(tmp);
                minHeap.add(num);
            }
        }
    }
    
    public void removeNum(int num) {
        size--;
        if (num < minHeap.peek()) {
            maxHeap.remove(num);
        } else {
            minHeap.remove(num);
        }
    }
    
    public double findMedian() {
        if (size % 2 == 0) {
            return maxHeap.peek() * 0.5 + minHeap.peek() * 0.5;
        } else {
            return maxHeap.peek();
        }
    }
}
```

## 例题 42. Trapping Rain Water
Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200430021321423.png)
The above elevation map is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped. Thanks Marcos for contributing this image!

Example:
Input: [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6

## Solution
这道题可以采用 Two Pointers，DP，heap 的解法。算法的基本想法是某一个可以存储的水量等于它左右两边最大高度中较小的一个减去它的高度。

DP解法
```java
class Solution {
    public int trap(int[] height) {
        if (height == null || height.length == 0) {
            return 0;
        }
        int[] leftmax = new int[height.length];
        int[] rightmax = new int[height.length];
        leftmax[0] = height[0];
        for (int i = 1; i < height.length; i++) {
            leftmax[i] = Math.max(leftmax[i-1], height[i]);
        }
        rightmax[height.length - 1] = height[height.length - 1];
        for (int i = height.length - 2; i >= 0; i--) {
            rightmax[i] = Math.max(rightmax[i+1], height[i]); 
        }
        
        int res = 0;
        for (int i = 0; i < height.length; i++) {
            res+=Math.min(leftmax[i], rightmax[i])- height[i];
        }
        return res;
    }
}
```

Heap解法

Two Pointer解法
一个位置 i 所储存的水量等于左右两边最大高度的较小值减去当前高度
two pointer解法维护两个值 leftMax, rightMax分别代表当前左右两边最大高度
利用当前的左右指针进行值的更新
由于储存水量只与较小值有关，所以虽然leftMax, rightMax不是 全局值，只代表从left到0和从right到-1的最大值，但是确保了两个中的较小值确实是全局值，所以结果是正确的

```java
class Solution {
    public int trap(int[] height) {
        int ans = 0;
        int left = 0, right = height.length - 1;
        int leftMax = 0, rightMax = 0;
        while (left < right) {
            leftMax = Math.max(leftMax, height[left]);
            rightMax = Math.max(rightMax, height[right]);
            if (leftMax < rightMax) {
                ans += leftMax - height[left];
                ++left;
            } else {
                ans += rightMax - height[right];
                --right;
            }
        }
        return ans;
    }
}
```
## 例题 407. Trapping Rain Water II
Given an m x n matrix of positive integers representing the height of each unit cell in a 2D elevation map, compute the volume of water it is able to trap after raining.

Example:
Given the following 3x6 height map:
[
  [1,4,3,1,3,2],
  [3,2,1,3,2,4],
  [2,3,3,2,3,1]
]

Return 4.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200430024717755.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
The above image represents the elevation map [[1,4,3,1,3,2],[3,2,1,3,2,4],[2,3,3,2,3,1]] before the rain.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200430024727795.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
After the rain, water is trapped between the blocks. The total volume of water trapped is 4.

## Solution
这题还可以采用 DP 的写法。每个点的存水量等于它前后左右四个方向的最大高度中的最小值减去它的高度。
还可以采用 Heap 的写法

Heap
```java
class Cell {
    public int x, y, height;
    Cell() {}
    Cell(int x, int y, int height) {
        this.x = x;
        this.y = y;
        this.height = height;
    }
}

class CellComparator implements Comparator<Cell> {
    @Override
    public int compare(Cell left, Cell right) {
        return left.height - right.height;
    }
}

public class Solution {
    int[] dx = {1, -1, 0, 0};
    int[] dy = {0, 0, 1, -1};
    
    public int trapRainWater(int[][] heights) {
        if (heights == null || heights.length == 0) {
            return 0;
        }
    
        PriorityQueue<Cell> minheap =  new PriorityQueue<>(new CellComparator());
        
        int n = heights.length;
        int m = heights[0].length;
        boolean[][] visited = new boolean[n][m];
        
        for (int i = 0; i < n; i++) {
            minheap.offer(new Cell(i, 0, heights[i][0]));
            minheap.offer(new Cell(i, m - 1, heights[i][m-1]));
            visited[i][0] = true;
            visited[i][m - 1] = true;
        }
        
        for (int i = 0; i < m; i++) {
            minheap.offer(new Cell(0, i, heights[0][i]));
            minheap.offer(new Cell(n-1, i, heights[n-1][i]));
            visited[0][i] = true;
            visited[n - 1][i] = true;
        }

        int water = 0;
        while (!minheap.isEmpty()) {
            Cell cell = minheap.poll();
            
            for (int i = 0; i < 4; i++) {
                int nx = cell.x + dx[i];
                int ny = cell.y + dy[i];
                
                if (nx < 0 || nx >= n || ny < 0 || ny >= m) {
                    continue;
                }
                if (visited[nx][ny]) {
                    continue;
                }
                
                visited[nx][ny] = true;
                minheap.offer(new Cell(nx, ny, Math.max(cell.height, heights[nx][ny])));
                water = water + Math.max(0, cell.height - heights[nx][ny]);
            }
        }
        
        return water;
    } 
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200430031508281.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)

# Stack
支持 
1. Push: $O(1)$
2. Top: $O(1)$
3. Pop: $O(1)$

## 例题 155. Min Stack
Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

push(x) -- Push element x onto stack.
pop() -- Removes the element on top of the stack.
top() -- Get the top element.
getMin() -- Retrieve the minimum element in the stack.

Example 1:
Input
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

Output
[null,null,null,null,-3,null,0,-2]

Explanation
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin(); // return -3
minStack.pop();
minStack.top();    // return 0
minStack.getMin(); // return -2

Constraints:
Methods pop, top and getMin operations will always be called on non-empty stacks.

## Solution
定义两个stack，一个用来存储值，另一个stack存储从stack底部到当前层的最小值
```java
class MinStack {
    Stack<Integer> stack;
    Stack<Integer> minStack;
    
    /** initialize your data structure here. */
    public MinStack() {
        stack = new Stack<>();
        minStack = new Stack<>();
    }
    
    public void push(int x) {
        stack.push(x);
        if (!minStack.isEmpty()) {
            minStack.push(Math.min(x, minStack.peek()));
        } else {
            minStack.push(x);
        }
    }
    
    public void pop() {
        stack.pop();
        minStack.pop();
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int getMin() {
        return minStack.peek();
    }
}
```
## 例题 394. Decode String
Given an encoded string, return its decoded string.

The encoding rule is: k[encoded_string], where the encoded_string inside the square brackets is being repeated exactly k times. Note that k is guaranteed to be a positive integer.

You may assume that the input string is always valid; No extra white spaces, square brackets are well-formed, etc.

Furthermore, you may assume that the original data does not contain any digits and that digits are only for those repeat numbers, k. For example, there won't be input like 3a or 2[4].

Examples:
s = "3[a]2[bc]", return "aaabcbc".
s = "3[a2[c]]", return "accaccacc".
s = "2[abc]3[cd]ef", return "abcabccdcdcdef".

## Solution
```java
    public String decodeString(String s) {
        Stack<Object> stack = new Stack<>();
        int number = 0;
        
        for (char c : s.toCharArray()) {
            if (Character.isDigit(c)) {
            	// 一次读取一个字符，确保数字是多位数也是正确的
                number = number * 10 + c - '0';
            } else if (c == '[') {
                stack.push(Integer.valueOf(number));
                number = 0;
            } else if (c == ']') {
                String newStr = popStack(stack);
                Integer count = (Integer) stack.pop();
                for (int i = 0; i < count; i++) {
                    stack.push(newStr);
                }
            } else {
                stack.push(String.valueOf(c));
            }
        }
        return popStack(stack);
    }
    
    private String popStack(Stack<Object> stack) {
        // pop stack until get a number or empty
        Stack<String> buffer = new Stack<>();
        while (!stack.isEmpty() && (stack.peek() instanceof String)) {
            buffer.push((String)stack.pop());
        }
        
        StringBuilder sb = new StringBuilder();
        while (!buffer.isEmpty()) {
            sb.append(buffer.pop());
        }
        return sb.toString();
    }
```

## 单调栈 Monotonous stack
> 栈中只保存升序序列 
> 新元素插入前pop 掉所有比它大的 
> stack([1,2,8,10]).push(5) => stack([1,2,5])


### LIFO FIFO
用两个队列FIFO实现栈LIFO:
一个队列A负责push和pop，另一个队列B负责中转
1 push
直接放到队列A中
2 pop
将A中除了最后一个元素全部都转移到B中，然后A中最后一个元素输出，然后把B中的元素重新转移到A中

用两个栈LIFO实现队列FIFO:
一个栈A负责push，另一个栈B负责pop
1 push
直接push到栈A中
2 pop
首先判断栈B是否有元素，如果有，栈B直接pop到输出；如果没有，把A中所有元素pop出来 ，然后push到B中，最后从B中pop一个元素到输出