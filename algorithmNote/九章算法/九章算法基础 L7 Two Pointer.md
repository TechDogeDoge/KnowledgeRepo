 #  九章算法基础 L7 Two Pointer
[TOC]

#  同向双指针

## 例题 Window Sum
Given an array of n integer, and a moving window (size k, move the window at each iteration from the start of the array. Find the sum of the element inside the window at each moving.

Example
Array a = [1, 2, 7, 8, 15], moving window size k = 3
1 + 2 + 7 = 10
2 + 7 + 8 = 17
7 + 8 + 15 = 30
return [10, 17, 30]

##  Solution 
计算的结果就是首先计算最前面 K 个元素的和，然后定义两个指针在 i = 0 和 i = k-1 的位置，然后每一次循环两个指针都向右移动一位，并计算指针中间 K个数的和。注意corner cases: k 小于1； k 大于等于数组的长度

```java
public class Solution {
    public int[] winSum(int[] nums, int k) {
        if (k < 1 || nums.length < k) return null;
        int ptr1 = 0;
        int ptr2 = k - 1;
        int[] res = new int(nums.length - k + 1);
        while (ptr2 < nums.length) {
            res[ptr1] = sum(nums, ptr1, ptr2);
            ptr1++;
            ptr2++;
        }
        return res;
    }
    
    private int sum(int[] nums, int from, int to) {
        int res = 0;
        for (int i = from; i <= to; i++) {
            res += nums[i];
        }
        return res
    }
}
```

##  例题 283. Move Zeroes
Given an array nums, write a function to move all 0's to the end of it while maintaining the relative order of the non-zero elements.

Example:
Input: [0,1,0,3,12]
Output: [1,3,12,0,0]
Note:

You must do this in-place without making a copy of the array.
Minimize the total number of operations.

##  solution
定义两个同向指针。当左指针的数为 0  时， 找到第一个非零的右指针数，然后进行交换；当左指针的数不为 0 时，左右指针同时向右移动一位。

```java
class Solution {
    public void moveZeroes(int[] nums) {
        if (nums == null || nums.length <= 1) {
            return;
        }
        int left = 0;
        int right = 1;
        while (left < nums.length && right < nums.length) {
            if (nums[left] == 0) {
                while (right < nums.length && nums[right] == 0) {
                    right++;
                }
                if (right == nums.length) break;
                swap(nums, left, right);
                left++;
            } else {
                left++;
                right++;
                 
            }
        }
    }
    
    private void swap(int[] nums, int i, int j) {
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
        return;
    }
}
```

##  例题 442. Find All Duplicates in an Array
Given an array of integers, 1 ≤ a[i] ≤ n (n = size of array), some elements appear twice and others appear once.

Find all the elements that appear twice in this array.

Could you do it without extra space and in O(n) runtime?

Example:
Input:
[4,3,2,7,8,2,3,1]
Output:
[2,3]

##  solution 
遍历数组，对于每一个元素值$num[i]$, 将位置为 $num[i] - 1$的元素的值改成负数，如果此时位置为 $num[i] - 1$的元素的值已经是负数，说明$num[i]$是 duplicates。之所以可以这样解是因为 **1 ≤ a[i] ≤ n (n = size of array)**
```java
    public List<Integer> findDuplicates(int[] nums) {
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < nums.length; ++i) {
            int index = Math.abs(nums[i])-1;
            if (nums[index] < 0)
                res.add(Math.abs(index+1));
            nums[index] = -nums[index];
        }
        return res;
    }
```

## 例题 Remove Duplicates Numbers in an Array
Given an array of integers, remove the duplicate numbers in it.
You should:
1. do it in place in the array.
2. move the unique numbers to the front of the array.
3. return the total number of unique numbers

Example:
Given numbers [1, 3, 1, 4, 4, 2], you should:

1. move unique numbers to the left [1,3,4,2,?,?]
2. return the number of unique numbers, 4

```java
    public List<Integer> removeDuplicates(int[] nums) {
        if (nums == null || nums.length == 0) {
            return new ArrayList<Integer>();
        }
        
        Arrays.sort(nums);
        
        int index = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != nums[index]) {
            	index++;
                nums[index] = nums[i];
            }
        }
        return index + 1;
    }
```

#  Two Sum
##  例题 1. Two Sum
Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

Example:
Given nums = [2, 7, 11, 15], target = 9,
Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].

##  solution
最直观的思路固定一个值，寻找 array 钟是否有另一个值，采用HashSet。时间复杂度为$O(n)$, 空间复杂度为$O(n)$

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer, Integer> test = new HashMap<>();
        for (int i = 0; i<nums.length;i++) {
            int complement = target - nums[i];
            if (test.containsKey(complement)) {
                return new int[] {i, test.get(complement)};
            }
            test.put(nums[i],i);
        }
        throw new IllegalArgumentException("No such solution");
    }
}
```

##  例题 167. Two Sum II - Input array is sorted
Given an array of integers that is already sorted in ascending order, find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2.

Note:

Your returned answers (both index1 and index2) are **not zero-based**.
You may assume that each input would have exactly one solution and you may not use the same element twice.

Example:
Input: numbers = [2,7,11,15], target = 9
Output: [1,2]
Explanation: The sum of 2 and 7 is 9. Therefore index1 = 1, index2 = 2.

##  solution
采用相向双指针，由于数组是 sorted 的，左指针向右移动，和变大，右指针向左移动，和变小。最终可以找到和等于 target 的两个元素。时间复杂度为$O(n)$, 空间复杂度为$O(1)$


```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0;
        int right = numbers.length - 1;
        while (left < right) {
            if (numbers[left] + numbers[right] == target) {
                return new int[] {left + 1, right + 1};
            }
            if (numbers[left] + numbers[right] < target) {
                left++;
            } else {
                right--;
            }
        }
        return null;
    }
}
```

##  例题 170. Two Sum III - Data structure design
Design and implement a TwoSum class. It should support the following operations: add and find.

add - Add the number to an internal data structure.
find - Find if there exists any pair of numbers which sum is equal to the value.

Example 1:
add(1); add(3); add(5);
find(4) -> true
find(7) -> false

Example 2:
add(3); add(1); add(2);
find(3) -> true
find(6) -> false

##  solution
这道题的重点是需要注意数组中存在 duplicates 的情况，同时要注意数组中存在值恰好为 target 的一半时的情况
```java
class TwoSum {
    private HashMap<Integer, Integer> map;
    private ArrayList<Integer> list;
    
    /** Initialize your data structure here. */
    public TwoSum() {
        this.map = new HashMap<Integer, Integer>();
        this.list = new ArrayList<Integer>();
    }
    
    /** Add the number to an internal data structure.. */
    public void add(int number) {
        if (this.map.containsKey(number)) {
            this.map.put(number, this.map.get(number) + 1);
        } else {
            this.map.put(number, 1);
            this.list.add(number);
        }
    }
    
    /** Find if there exists any pair of numbers which sum is equal to the value. */
    public boolean find(int value) {
        for (Integer num : this.list) {
            int num1 = num;
            int num2 = value - num1;
            if (num1 == num2 && this.map.get(num1) > 1) {
                return true;
            }
            if (num1 != num2 && this.map.containsKey(num2)) {
                return true;
            }
        }
        return false;
    }
}

/**
 * Your TwoSum object will be instantiated and called as such:
 * TwoSum obj = new TwoSum();
 * obj.add(number);
 * boolean param_2 = obj.find(value);
 */
```

##  例题 Two Sum- Unique pairs
Given an array of integers, find how many unique pairs in the array such that their sum is equal to a specific target number target number. Please return the number of pairs.

Example:
Given nums = [1, 1, 2, 45, 46, 46], target = 47
return 2

1 + 46 = 47
2 + 45 = 47

##  solution 
首先对数组进行排序，然后定义左右指针，遍历数组然后找到所有 unique pairs
```java
publuc int twoSumUnique(int[] nums, int target) {
    if (nums == null || nums.length < 2) {
        return 0;
    }
    Arrays.sort(nums);
    int res = 0;
    int left = 0;
    int right = nums.length - 1;
    while (left < right) {
        if (nums[left] + nums[right] == target) {
            res++;
            left++;
            right--;            
            while (left < right && nums[left] == nums[left - 1]) {
                left++;
            }
            while (left < right && nums[right] == nums[right + 1]) {
                right++;
            }
        } else if (nums[left] + nums[right] < target) {
            left++;
        } else if (nums[left] + nums[right] > target) {
            right++;
        }
    }
    return res;
}
```

##  例题 15. 3 Sum
Given an array nums of n integers, are there elements a, b, c in nums such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero.

Note:
The solution set must not contain duplicate triplets.

Example:
Given array nums = [-1, 0, 1, 2, -1, -4],
A solution set is:
[
  [-1, 0, 1],
  [-1, -1, 2]
]

##  solution HashMap
首先把所有元素放到map里面，然后两个 for 循环，找 -(a+b) 是否在Map里面
时间复杂度为$O(n^2)$，空间复杂度为$O(n)$

##  solution Two Pointer
首先将数组进行排序，然后 for 循环 a，然后在 a 的右边定义两个pointer，分别找到 b, c 使得 b + c = - a。时间复杂度为$O(n^2)$，空间复杂度为$O(1)$
需要注意的点：
首先进行 Two pointer 时要注意去掉重复的组合，然后就是对 a 进行 for 循环时，要注意重复的 a 需要跳过。

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums == null || nums.length < 3) {
            return res;
        }
        Arrays.sort(nums);
        
        for (int i = 0; i < nums.length - 2; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }
            int left = i + 1, right = nums.length - 1;
            int target = -nums[i];
            while (left < right) {
                if (nums[left] + nums[right] == target) {
                    ArrayList<Integer> tmp = new ArrayList<>();
                    tmp.add(nums[i]);
                    tmp.add(nums[left]);
                    tmp.add(nums[right]);
                    res.add(tmp);
                    left++;
                    right--;
                    while (left < right && nums[left] == nums[left - 1]) {
                        left++;
                    }
                    while (left < right && nums[right] == nums[right + 1]) {
                        right--;
                    }
                } else if (nums[left] + nums[right] < target) {
                    left++;
                } else if (nums[left] + nums[right] > target) {
                    right--;
                }
            }
        }
        return res;
    }
}
```

##  例题 611. Valid Triangle Number
Given an array consists of non-negative integers, your task is to count the number of triplets chosen from the array that can make triangles if we take them as side lengths of a triangle.
Example 1:
Input: [2,2,3,4]
Output: 3
Explanation:
Valid combinations are: 
2,3,4 (using the first 2)
2,3,4 (using the second 2)
2,2,3
Note:
The length of the given array won't exceed 1000.
The integers in the given array are in the range of [0, 1000].

##  solution
和前面做法类似，采用 two pointer 的做法，但是由于是不等关系，需要修改一下。首先 for 循环最大的数，循环最大的数是为了减少时间复杂度。
然后定义两个左右指针。当左右指针和大于最大数时，固定右指针，左指针向右移动，所有都满足要求，因此解的个数增加 right - left 个， 然后将右指针向左移动一位，此时确保了当前左指针的左边不可能有解，因为右指针变小了。如果左右指针和小于最大数时。右指针固定不动，左指针向右移动。
所以 for 循环最大的数就是为了确保，满足三角形条件越来越难，保证新的解只会出现在左指针的右边，因此左右指针都只进行单向的移动
**如果 for 循环最小的数，则无法保证指针的单向移动**
```java
class Solution {
    public int triangleNumber(int[] nums) {
        if(nums == null || nums.length < 3) {
            return 0;
        }
        int res = 0;
        
        Arrays.sort(nums);
        
        for (int i = 2; i < nums.length; i++) {
            int left = 0;
            int right = i - 1;
            int num = nums[i];
            while (left < right) {
                if (nums[left] + nums[right] > num) {
                    res += right - left;
                    right--;
                } else {
                    left++;
                }
            }
        }
        return res;
    }
}
```

##  例题 16. 3 Sum Closest
Given an array nums of n integers and an integer target, find three integers in nums such that the sum is closest to target. Return the sum of the three integers. You may assume that each input would have exactly one solution.

Example:
Given array nums = [-1, 2, 1, -4], and target = 1.
The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).

##  solution
和 3 sum的解法一样，for 循环一个数，然后定义左右指针。
```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        if (nums == null || nums.length < 3) {
            return -1;
        }
        Arrays.sort(nums);
        int dist = Integer.MAX_VALUE;
        int res = 0;
        for (int i = 0; i < nums.length - 2; i++) {
            int num = nums[i];
            int left = i + 1, right = nums.length - 1;
            while (left < right) {
                int temp = num + nums[left] + nums[right];
                if (Math.abs(temp - target) < dist) {
                    dist = Math.abs(temp - target);
                    res = temp;
                }
                if (temp < target) {
                    left++;
                } else {
                    right--;
                }
            }
        }
        return res;
    }
}
```

**对于之和的问题定义相向双指针，对于之差的问题，定义同向双指针**

#  Partition
##  quick sort
写法一
```java
    public static void quickSort(int[] nums) {
        shuffle(nums);
        quickSort(nums, 0, nums.length - 1);
    }

    public static void quickSort(int[] nums, int lo, int hi) {
        if (lo >= hi) {
            return;
        }
        int index = partition(nums, lo, hi);
        quickSort(nums, lo, index - 1);
        quickSort(nums, index + 1, hi);
    }

    public static int partition (int[] nums, int lo, int hi) {
        int from = lo;
        int to = hi + 1;
        int par = nums[lo];
        while (true) {
            while (nums[++from] <= par) {
                if (from == hi) break;
            }
            while (nums[--to] >= par) {
                if (to == lo) break;;
            }
            if (from >= to) break;
            swap(nums, from, to);
        }
        swap(nums, to, lo);
        return to;
    }
    public static void swap(int[] nums, int i, int j) {
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }

    public static void shuffle(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            int tmp = (int) (Math.random()*(nums.length-1));
            swap(nums, i, tmp);
        }
    }
```
写法二
```java
    public static void quickSort(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
    }

    public static void quickSort(int[] nums, int start, int end) {
        if (start >= end) {
            return;
        }
        int pivot = nums[(start + end) / 2];
        int left = start, right = end;
        while (left <= right) {
            while(left <= right && nums[left] < pivot) {
                left++;
            }
            while(left <= right && nums[right] > pivot) {
                right--;
            }
            if (left <= right) {
                int tmp = nums[left];
                nums[left] = nums[right];
                nums[right] = tmp;
                //swap(nums, left, right);
                left++;
                right--;
            }
        }
        quickSort(nums, start, right);
        quickSort(nums, left, end);
    }
```
##  例题 Partition Array
Given an array "nums" of integers and an int "k", Partition the array (i.e move the elements in "nums") such that.
1. All elements < k are moved to the left
2. All elements >= k are moved to the right
Return the partitioning Index, i.e the first index "i" nums[i] >= k.
If all elements in "nums" are smaller than k, then return "nums.length"

Example
If nums=[3,2,2,1] and k=2, a valid answer is 1.

##  Solution
```java
public int partitionArray(int[] nums, int k) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    
    int left = 0, right = nums.length - 1;
    while (left < right) {
        while (left < right && nums[left] < k) {
            left++;
        }
        while (left < right && nums[right] >=k ) {
            right++;
        }
        if (left < right) {
            int tmp = nums[left];
            nums[left] = nums[right];
            nums[right] = tmp;
            left++;
            right--;
        }
    }
    
    // the case when all the elements are smaller than target k
    if (nums[left] < k) {
        return left + 1;
    }
    return left;
    
}
```

## Quick Select
##  例题 215. Kth Smallest Element in an Array
Find the kth smallest element in an unsorted array. Note that it is the kth smallest  element in the sorted order, not the kth distinct element.

Example 1:
Input: [3,2,1,5,6,4] and k = 2
Output: 2

Example 2:
Input: [3,2,3,1,2,4,5,5,6] and k = 4
Output: 3
Note:
You may assume k is always valid, 1 ≤ k ≤ array's length.

##  solution
```java
class Solution {
    /*
     * @param k an integer
     * @param nums an integer array
     * @return kth smallest element
     */
    public int kthSmallest(int k, int[] nums) {
        return quickSelect(nums, 0, nums.length - 1, k - 1);
    }

    public int quickSelect(int[] A, int start, int end , int k) {
        if (start == end)
            return A[start];
        
        int left = start, right = end;
        int pivot = A[(start + end) / 2];

        while (left <= right) {
            while (left <= right && A[left] < pivot) {
                left++;
            }
            while (left <= right && A[right] > pivot) {
                right--;
            }
            if (left <= right) {
                int temp = A[left];
                A[left] = A[right];
                A[right] = temp;
                
                left++;
                right--;
            }
        }

        if (right >= k && start <= right)
            return quickSelect(A, start, right, k);
        else if (left <= k && left <= end)
            return quickSelect(A, left, end, k);
        else
            return A[k];
    }
};
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200210054752721.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
##  例题 75. Sort Colors
Given an array with n objects colored red, white or blue, sort them in-place so that objects of the same color are adjacent, with the colors in the order red, white and blue.
Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.
Note: You are not suppose to use the library's sort function for this problem.

Example:
Input: [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]

Follow up:
A rather straight forward solution is a two-pass algorithm using counting sort.
First, iterate the array counting number of 0's, 1's, and 2's, then overwrite array with total number of 0's, then 1's and followed by 2's.
Could you come up with a one-pass algorithm using only constant space?

##  solution 1
首先将 array 分成 0,(1, 2) 两个部分，然后再在 （1，2）里面再分成 1，2 两部分。

##  solution 2
定义左右指针，左指针左边都是 0， 右指针右边都是 2，指针中间都是 1。
然后定义一个 i 指针从左向右遍历数组，直到遇到右指针为止
```java
class Solution {
    public void sortColors(int[] nums) {
        if (nums == null || nums.length == 0) {
            return;
        }
        
        int left = 0, right = nums.length - 1;
        int  i = 0;
        while (i <= right) {
            if (nums[i] == 0) {
                swap(nums, left, i);
                i++;
                left++;
            } else if (nums[i] == 1) {
                i++;
            } else if (nums[i] == 2) {
                swap(nums, right, i);
                right--;
            }
        }
    }
    
    private void swap(int[] nums, int i, int j) {
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }
}
```

## 例题 Rainbow Sort (Sort colors 2)
Given an array of n objects with k different colors (number from 1 to k). Sort them so that objects of the same color are adjacent, with the colors in the order 1, 2, ...k.

Example
Given colors = [3, 2, 2, 1, 4], k = 4, you should sort colors in-place to [1, 2, 2, 3, 4].
##  solution
```java
public class Solution {
    /**
     * @param colors: A list of integer
     * @param k: An integer
     * @return: nothing
     */
    public void sortColors2(int[] colors, int k) {
        // write your code here
        //O(nlogk), the best algorithm based on comparing
        //算法时间复杂度要求到O(nlogk)，k为颜色个数
        //这种算法的思想类似与quickSort与mergeSort结合
        //quickSort的思想在于partition进行分割
        //mergeSort的思想在于直接取中间（这里表现为取中间大小的数），分为左右两个相等长度的部分
        //区别在于partition的判定条件变为了中间大小的元素而不是中间位置的元素
        //因此等号的取值可以只去一边也不会有影响
        if (colors == null || colors.length == 0) {
            return;
        }
        rainbowSort(colors, 0, colors.length - 1, 1, k);
    }
    //将colors数组的索引范围start到end位置排序
    //排序的大小范围是colorFrom到colorTo
    private void rainbowSort(int[] colors,
                            int start,
                            int end,
                            int colorFrom,
                            int colorTo) {
        if (colorFrom == colorTo) {
            return;
        }
        if (start == end) {
            return;
        }
        //区别在于partition的判定条件变为了中间大小的元素而不是中间位置的元素
        int colorMid = (colorFrom + colorTo) / 2;
        int left = start, right = end;
        while (left <= right) {
            //因此等号的取值可以只去一边也不会有影响
            while (left <= right && colors[left] <= colorMid) {
                left++;
            }
            while (left <= right && colors[right] > colorMid) {
                right--;
            }
            if (left <= right) {
                int temp = colors[left];
                colors[left] = colors[right];
                colors[right] = temp;
                left++;
                right--;
            }
        }
        rainbowSort(colors, start, right, colorFrom, colorMid);
        rainbowSort(colors, left, end, colorMid + 1, colorTo);
    }
}
```