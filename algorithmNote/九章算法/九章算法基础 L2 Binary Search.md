# 九章算法基础 L2 Binary Search



#  1 二分法模板

## 1.1 九章模板

两种问题：1. 在一个排序好的数组中寻找target第一次出现的index; 2. 在一个排序好的数组中寻找target最后一次出现的index;

对于第一个问题可以采用
while(lo <= hi)
mid = lo + (hi - lo) / 2;
的写法，
但是对于第二个问题，不能这样写，因为除法是靠左的，while(lo <= hi)可能使算法陷入死循环

二分法模板可以兼容这两个问题
```java
while(lo + 1 < hi)  {
    int mid = lo + (hi - lo) / 2;
    if (target > nums[mid]) {
        lo = mid;
    } else if (target < nums[mid]) {
        hi = mid;
    } else if (target == nums[mid]) {
    	// 两种情况：找到第一次出现的坐标(左边)或者最后一次出现的坐标(右边)
    	// 第一次出现时：
    	// hi = mid;
    	// 最后一次出现时：
    	// lo = mid;
    }
}

if (nums[lo] == target) {
    return lo;
}

if (nums[hi] == target) {
    return hi;
}
```
二分法模板把二分法分成两部分，第一步先找到可能的解 lo 和 hi，第二部确定最终答案。
同时 lo = mid 或者 hi = mid 也不用加1。

## 1.2 labuladong模板
将问题分为三种：1 搜索给定值； 2 搜索最左侧给定值； 3 搜索最右侧给定值；
模板统一采用 while (left <= right) 的写法，但在更新边界以及最后是否要进行检查上面有所区别
```java
// 1 搜索给定值
public int binarySearch(int[] array, int target) {
	int left = 0, right = array.length - 1;
	while (left <= right) {
		int mid = left + (right - left) / 2;
		if (array[mid] == target) {
			return mid;
		} else if (array[mid] > target) {
			right = mid - 1;
		} else if (array[mid] < target) {
			left = mid + 1;
		}
	}
	return -1;
}

// 2 搜索最左侧给定值
public int binarySearch_left(int[] array, int target) {
	int left = 0, right = array.length - 1;
	while (left <= right) {
		int mid = left + (right - left) / 2;
		if (array[mid] == target) {
			right = mid - 1;
		} else if (array[mid] > target) {
			right = mid - 1;
		} else if (array[mid] < target) {
			left = mid + 1;
		}
	}
	if (left >= array.length || array[left] != target) {
		return -1;
	}
	return left;
}

// 3 搜索最右侧给定值
public int binarySearch_right(int[] array, int target) {
	int left = 0, right = array.length - 1;
	while (left <= right) {
		int mid = left + (right - left) / 2;
		if (array[mid] == target) {
			left = mid + 1;
		} else if (array[mid] > target) {
			right = mid - 1;
		} else if (array[mid] < target) {
			left = mid + 1;
		}
	}
	if (right < 0 || array[right] != target) {
		return -1;
	}
	return right;
}
```

#  2
二分法：OOOOOOXXXXX型

##  例题：Leetcode 278 first bad version

You are a product manager and currently leading a team to develop a new product. Unfortunately, the latest version of your product fails the quality check. Since each version is developed based on the previous version, all the versions after a bad version are also bad.

Suppose you have n versions [1, 2, ..., n] and you want to find out the first bad one, which causes all the following ones to be bad.

You are given an API bool isBadVersion(version) which will return whether version is bad. Implement a function to find the first bad version. You should minimize the number of calls to the API.

Example:

Given n = 5, and version = 4 is the first bad version.

call isBadVersion(3) -> false
call isBadVersion(5) -> true
call isBadVersion(4) -> true

Then 4 is the first bad version. 


```java
public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        if (n == 1) return 1; 
        int lo = 0;
        int hi = n;
        
        while (lo + 1 < hi) {
            int mid = lo + (hi - lo) / 2;
            if (isBadVersion(mid)) {
                hi = mid;
            } else {
                lo = mid;
            }
        }
        return isBadVersion(lo) ? lo:hi;
    }
}
```

#  3
二分法：二分法的变种，倍增法

举例在一个长度无限大的数组中找到一个target，同时复杂度在$O(log k)$以内，k是target第一次出现的index

## leetcode 702. Search in a Sorted Array of Unknown Size

Given an integer array sorted in ascending order, write a function to search target in nums.  If target exists, then return its index, otherwise return -1. However, the array size is unknown to you. You may only access the array using an ArrayReader interface, where ArrayReader.get(k) returns the element of the array at index k (0-indexed).

You may assume all integers in the array are less than 10000, and if you access the array out of bounds, ArrayReader.get will return 2147483647.

Example 1:
Input: array = [-1,0,3,5,9,12], target = 9
Output: 4
Explanation: 9 exists in nums and its index is 4

Example 2:
Input: array = [-1,0,3,5,9,12], target = 2
Output: -1
Explanation: 2 does not exist in nums so return -1

 Note:
You may assume that all elements in the array are unique.
The value of each element in the array will be in the range [-9999, 9999].


```java
class Solution {
    public int search(ArrayReader reader, int target) {
        if (reader == null) return -1;
        int lo = 0;
        int hi = 1;
        while (reader.get(hi) < target) {
       		lo = hi;
            hi *= 2;
        }
        
        while (lo + 1 < hi) {
            int mid = lo + (hi - lo) / 2;
            if (reader.get(mid) < target) {
                lo = mid;
            } else if (reader.get(mid) >= target) {
                hi = mid;
            }
        }
        
        if (reader.get(lo) == target) {
            return lo;
        }
        
        if (reader.get(hi) == target) {
            return hi;
        }
        return -1;
    }
}
```

#  4 Leetcode 153. Find Minimum in Rotated Sorted Array
Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e.,  [0,1,2,4,5,6,7] might become  [4,5,6,7,0,1,2]).

Find the minimum element.

You may assume no duplicate exists in the array.

Example 1:

Input: [3,4,5,1,2] 
Output: 1
Example 2:

Input: [4,5,6,7,0,1,2]
Output: 0

题目解法关键在于找到lo和hi以后如何确定pivot
举例：origi_nums = [1,3,5]
nums = [5,1,3] 很明显此时 pivot =1， 首先比较 nums[lo]和nums[hi]的大小，如果nums[lo] < nums[hi]说明整个数组都是顺序正确的，没有pivot，于是给pivot赋值0；反之如果nums[lo] > nums[hi]，说明pivot存在，且就在lo和hi之间，于是给pivot赋值hi。

```java
    public int findMin(int[] nums) {
        if (nums == null || nums.length == 0) return -1;
        if (nums.length == 1) return nums[0];
        int lo = 0;
        int hi = nums.length - 1;
        
        // find the pivot
        // [4, 5, 6, 7, | 0, 1, 2]
        while (lo + 1 < hi) {
            int mid = lo + (hi - lo) / 2;
            
            if (nums[mid] > nums[lo]) {
                lo = mid;
            } else if (nums[mid] < nums[lo]) {
                hi = mid;
            }
        }
        // (index in sorted array + hi)%nums.length = index in rotated array
        int pivot = (nums[lo] > nums[hi]) ? hi:0;
        return nums[pivot];
    }
```

还有一种分析的思路
<img src="https://img-blog.csdnimg.cn/20191222062826883.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="数据具象化" style="zoom:67%;" />

#  5
##  302. Smallest Rectangle Enclosing Black Pixels
An image is represented by a binary matrix with 0 as a white pixel and 1 as a black pixel. The black pixels are connected, i.e., there is only one black region. Pixels are connected horizontally and vertically. Given the location (x, y) of one of the black pixels, return the area of the smallest (axis-aligned) rectangle that encloses all black pixels.

Example:
Input:
[
  "0010",
  "0110",
  "0100"
]
and x = 0, y = 2
Output: 6
<img src="https://img-blog.csdnimg.cn/20191222095807442.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom: 50%;" />
<img src="https://img-blog.csdnimg.cn/20191222095839209.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom:50%;" />

```java
class Solution {
    public int minArea(char[][] image, int x, int y) {
        int m = image.length;
        int n = image[0].length;
        int l = 0;
        int r = 0;
        int t = 0;
        int b = 0;
        // vertical (column) 
        // left
        int lo = 0;
        int hi = y;
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (emptyColumn(image, mid)) {
                lo = mid+1;
            } else {
                hi = mid;
            }
        }
        l = lo;
        
        lo = y;
        hi = n - 1;
        while (lo < hi) {
            int mid = lo + (hi - lo + 1) / 2;
            if (emptyColumn(image, mid)) {
                hi = mid - 1;
            } else {
                lo = mid;
            }
        }
        r = lo;
        
        // horizontal (row)
        lo = 0;
        hi = x;
        while (lo + 1 < hi) {
            int mid = lo + (hi - lo) / 2;
            if (emptyRow(image, mid)) {
                lo = mid;
            } else {
                hi = mid;
            }
        }
        t  = (emptyRow(image, lo)) ? hi:lo;
        
        lo = x;
        hi = m - 1;
        while (lo + 1 < hi) {
            int mid = lo + (hi - lo) / 2;
            if (emptyRow(image, mid)) {
                hi = mid;
            } else {
                lo = mid;
            }
        }
        b = (emptyRow(image, hi)) ? lo:hi;
        
        return (b - t + 1) * (r - l + 1);
    }
    
    public boolean emptyRow(char[][] image, int num) {
        for (char tmp : image[num]) {
            if (tmp == '1') 
                return false;
        }
        return true;
    }
    
    public boolean emptyColumn(char[][] image, int num) {
        int m = image.length;
        for (int i = 0; i < m; i++) {
            if (image[i][num] == '1')
                return false;
        }
        return true;
    }
}
```

#  6 相关练习
![相关练习](https://img-blog.csdnimg.cn/20191222095638373.JPG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
##  74. Search a 2D Matrix
Write an efficient algorithm that searches for a value in an m x n matrix. This matrix has the following properties:

Integers in each row are sorted from left to right.
The first integer of each row is greater than the last integer of the previous row.

Example 1:
Input:
matrix = {[1,   3,  5,  7], [10, 11, 16, 20], [23, 30, 34, 50]}
target = 3
Output: true

Example 2:
Input:
matrix = {[1,   3,  5,  7], [10, 11, 16, 20], [23, 30, 34, 50]}
target = 13
Output: false

## solution 1
```java
    // since the arrays are sorteed among different arrays,
    // take the 2D matrix as a sorted array of length n*m 
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 ||matrix[0].length == 0) return false;
        int m = matrix.length;
        int n = matrix[0].length;
        int lo = 0;
        int hi = m * n - 1;
        while (lo + 1 < hi) {
            int mid = lo + (hi - lo) / 2;
            int r = mid / n;
            int col = mid % n;
            if (matrix[r][col] > target) {
                hi = mid;
            } else if (matrix[r][col] < target) {
                lo = mid;
            } else if (matrix[r][col] == target) {
                return true;
            }
        }
        
        return (matrix[lo / n][lo % n] == target 
                || matrix [hi / n][hi % n] == target);
    }
```

##  solution 2
```java
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 ||matrix[0].length == 0) return false;
        int m = matrix.length;
        int n = matrix[0].length;
        int lo = 0;
        int hi = m - 1;
        // since the 2D array is sorted in each row,
        // we first search the row
        int row = 0;
        while (lo + 1 < hi) {
            int mid = lo + (hi - lo) / 2; 
            if (matrix[mid][0] > target) {
                hi = mid;
            } else if (matrix[mid][n - 1] < target) {
                lo = mid;
            } else if (matrix[mid][0] <= target && target <= matrix[mid][n - 1]) {
                hi = mid;
            }
        }
        
        if (matrix[lo][0] <= target && target <= matrix[lo][n - 1]) {
            row = lo;
        } else if (matrix[hi][0] <= target && target <= matrix[hi][n - 1]) {
            row = hi;
        } else {
            return false;
        }
        // search the col
        // we have already found the row num
        // search in the specific row
        lo = 0;
        hi = n - 1;
        while (lo + 1 < hi) {
            int mid = lo + (hi - lo) / 2;
            if (matrix[row][mid] < target) {
                lo = mid;
            } else if (matrix[row][mid] > target) {
                hi = mid;
            } else if (matrix[row][mid] == target) {
                return true;
            }
        }
        return (matrix[row][hi] == target || matrix[row][lo] == target);
    }
```

##  240. Search a 2D Matrix II
Write an efficient algorithm that searches for a value in an m x n matrix. This matrix has the following properties:

Integers in each row are sorted in ascending from left to right.
Integers in each column are sorted in ascending from top to bottom.
Example:

Consider the following matrix:
{
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
}
Given target = 5, return true.
Given target = 20, return false.

## solution
```java
    private boolean binarySearch(int[][] matrix, int target, int start, boolean vertical) {
        int lo = start;
        int hi = vertical ? matrix[0].length-1 : matrix.length-1;
        while (hi >= lo) {
            int mid = (lo + hi)/2;
            if (vertical) { // searching a column
                if (matrix[start][mid] < target) {
                    lo = mid + 1;
                } else if (matrix[start][mid] > target) {
                    hi = mid - 1;
                } else {
                    return true;
                }
            } else { // searching a row
                if (matrix[mid][start] < target) {
                    lo = mid + 1;
                } else if (matrix[mid][start] > target) {
                    hi = mid - 1;
                } else {
                    return true;
                }
            }
        }

        return false;
    }

    public boolean searchMatrix(int[][] matrix, int target) {
        // an empty matrix obviously does not contain `target`
        if (matrix == null || matrix.length == 0) {
            return false;
        }
        // iterate over matrix diagonals
        int shorterDim = Math.min(matrix.length, matrix[0].length);
        for (int i = 0; i < shorterDim; i++) {
            boolean verticalFound = binarySearch(matrix, target, i, true);
            boolean horizontalFound = binarySearch(matrix, target, i, false);
            if (verticalFound || horizontalFound) {
                return true;
            }
        }
        return false; 
    }
```
## solution
```java
    public boolean searchMatrix(int[][] matrix, int target) {
        // an empty matrix obviously does not contain `target`
        if (matrix == null || matrix.length == 0) {
            return false;
        }
        
        int row = 0;
        int col = matrix[0].length - 1;
        
        while (col >= 0 && row < matrix.length) {
            if (matrix[row][col] > target) {
                col--;
            } else if(matrix[row][col] < target) {
                row++;
            } else {
                return true;
            }
        }
        return false;
    }
```

#  7 
有时候二分法并不能找到一个条件形成OOOOXXXXX模型，但是可以根据判断保留下有解的那一半，或者去掉无解的那一半

##  例题 leetcode 162. Find Peak Element
A peak element is an element that is greater than its neighbors.

Given an input array nums, where nums[i] ≠ nums[i+1], find a peak element and return its index.

The array may contain multiple peaks, in that case return the index to any one of the peaks is fine.

You may imagine that nums[-1] = nums[n] = -∞.

Example 1:
Input: nums = [1,2,3,1]
Output: 2
Explanation: 3 is a peak element and your function should return the index number 2.

Example 2:
Input: nums = [1,2,1,3,5,6,4]
Output: 1 or 5 
Explanation: Your function can return either index number 1 where the peak element is 2, or index number 5 where the peak element is 6.

Note:
**Your solution should be in logarithmic complexity.**

一段区间里面如果出现先上升后下降的情况说明区间内一定有一个peak点

##  solution 1 binary search
按照视频中的思路，mid的位置分为四种情况：上升段、下降段、低谷点，峰值点。然后分别讨论
```java
    public int findPeakElement(int[] nums) {
        if (nums.length == 1) return 0;
        if (nums.length == 2) return (nums[0] > nums[1]) ? 0 : 1;
        
        int lo = 0;
        int hi = nums.length - 1;
        
        while (lo  + 1 < hi) {
            int mid = lo + (hi - lo) / 2;
            
            if (nums[mid] < nums[mid - 1] && nums[mid] > nums[mid + 1]) {
                hi = mid; 
            } else if (nums[mid] > nums[mid - 1] && nums[mid] < nums[mid + 1]) {
                lo = mid;
            } else if (nums[mid] > nums[mid - 1] && nums[mid] > nums[mid + 1]) {
                return mid;
            } else if (nums[mid] < nums[mid - 1] && nums[mid] < nums[mid + 1]) {
                lo = mid;
            }
        }
        
        if (nums[lo] > nums[hi]) {
            return lo;
        } else return hi;
    }
```

##  solution 2 linear scan
time complexity $O(n)$
```java
public class Solution {
    public int findPeakElement(int[] nums) {
        for (int i = 0; i < nums.length - 1; i++) {
            if (nums[i] > nums[i + 1])
                return i;
        }
        return nums.length - 1;
    }
}
```
##  solution 3 binary search
```java
class Solution {
        public int findPeakElement(int[] nums) {
        int l = 0, r = nums.length - 1;
        while (l < r) {
            int mid = (l + r) / 2;
            if (nums[mid] > nums[mid + 1])
                r = mid;
            else
                l = mid + 1;
        }
        return l;
    }
}
```

#  8
##  33. Search in Rotated Sorted Array
Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., [0,1,2,4,5,6,7] might become [4,5,6,7,0,1,2]).

You are given a target value to search. If found in the array return its index, otherwise return -1.

You may assume no duplicate exists in the array.

Your algorithm's runtime complexity must be in the order of O(log n).

Example 1:

Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4
Example 2:

Input: nums = [4,5,6,7,0,1,2], target = 3
Output: -1

##  solution 1
解法分为两部分：
首先确定pivot点，然后再进行binary search
```java
class Solution {
    public int search(int[] nums, int target) {
        if (nums == null || nums.length == 0) return -1;
        if (nums.length == 1) return (nums[0] == target) ? 0:-1;
        int lo = 0;
        int hi = nums.length - 1;
        // find the pivot
        // [4, 5, 6, 7, | 0, 1, 2]
        while (lo + 1 < hi) {
            int mid = lo + (hi - lo) / 2;
            
            if (nums[mid] > nums[lo]) {
                lo = mid;
            } else if (nums[mid] < nums[lo]) {
                hi = mid;
            }
        }
        
        // (index in sorted array + hi)%nums.length = index in rotated array
        int pivot = (nums[lo] > nums[hi]) ? hi:0;
        lo = 0;
        hi = nums.length - 1;
        while(lo + 1 < hi) {
            int mid = lo + (hi - lo) / 2;
            
            if (nums[(mid + pivot) % nums.length] >= target) {
                hi = mid;
            } else if (nums[(mid + pivot) % nums.length] < target) {
                lo = mid;
            }
        }
        
        if (nums[(lo + pivot) % nums.length] == target) {
            return (lo + pivot) % nums.length;
        }

        if (nums[(hi + pivot) % nums.length] == target) {
            return (hi + pivot) % nums.length;
        }
        return -1;
    }
}
```

##  solution 2
同样也是分为两部分：
首先找到最小值和最小值坐标(pivot)，这样可以把数组切分成两个sorted subarrays。然后判断target应该在哪一边，对这个subarray进行二分法
```java
class Solution {
    public int search(int[] nums, int target) {
        if (nums == null || nums.length == 0) return -1;
        if (nums.length == 1) return (nums[0] == target) ? 0:-1;
        int lo = 0;
        int hi = nums.length - 1;
        // find the pivot
        // [4, 5, 6, 7, | 0, 1, 2]
        while (lo + 1 < hi) {
            int mid = lo + (hi - lo) / 2;
            
            if (nums[mid] > nums[lo]) {
                lo = mid;
            } else if (nums[mid] < nums[lo]) {
                hi = mid;
            }
        }
        int pivot = (nums[lo] > nums[hi]) ? hi:0;
        if (pivot == 0) {
            return binary(nums, 0, nums.length - 1, target);
        }
        if (target >= nums[0]) {
            return binary(nums, 0, pivot - 1, target);
        } else {
            return binary(nums, pivot, nums.length - 1, target);
        }
    }
    
    public int binary(int[] nums, int lo, int hi, int target) {
        while (lo + 1 < hi) {
            int mid = lo + (hi - lo) / 2;
            if (nums[mid] < target) {
                lo = mid;
            } else if (nums[mid] > target) {
                hi = mid;
            } else return mid;
        }
        if (nums[lo] == target) {
            return lo;
        }
        if (nums[hi] == target) {
            return hi;
        }
        return -1;
    }
}
```

##  solution 3
能不能只用一个二分法解决
也可以，首先判断mid的位置，在左半边还是右半边，然后分别判断target可能的位置是哪里
1. mid在左半边：条件是 nums[mid] >= nums[0]
1.1 target在mid的左边：target >= nums[0] && target < nums[mid]
1.2 target在mid的右边：target > nums[mid] || target < nums[0]
2. mid在右半边：条件是nums[mid] < nums[0]
2.1 target在mid的左边：target >= nums[0] || target < nums[mid]
2.2 target在mid的右边：target > nums[mid] && target < nums[0]
<img src="https://img-blog.csdnimg.cn/20191223105539904.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="示意图" style="zoom:67%;" />
```java
class Solution {
    public int search(int[] nums, int target) {
        if (nums == null || nums.length == 0) return -1;
        if (nums.length == 1) return (nums[0] == target) ? 0:-1;
        int lo = 0;
        int hi = nums.length - 1;
        while (lo + 1 < hi) {
            int mid = lo + (hi - lo) / 2;
            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] >= nums[0]) {
                // mid before pivot
                if (target >= nums[0] && target < nums[mid]) {
                    hi = mid;
                } else if (target > nums[mid] || target < nums[0]) {
                    lo = mid;
                }
            } else if (nums[mid] < nums[0]) {
                // mid after pivot
                if (target > nums[mid] && target < nums[0]) {
                    lo = mid;
                } else if (target >= nums[0] || target < nums[mid]) {
                    hi = mid;
                }
            }
        }
        
        if (nums[lo] == target) {
            return lo;
        }
        if (nums[hi] == target) {
            return hi;
        }
        return -1;
    }
}
```


#  9
##  189. Rotate Array
Given an array, rotate the array to the right by k steps, where k is non-negative.

Example 1:
Input: [1,2,3,4,5,6,7] and k = 3
Output: [5,6,7,1,2,3,4]
Explanation:
rotate 1 steps to the right: [7,1,2,3,4,5,6]
rotate 2 steps to the right: [6,7,1,2,3,4,5]
rotate 3 steps to the right: [5,6,7,1,2,3,4]

Example 2:
Input: [-1,-100,3,99] and k = 2
Output: [3,99,-1,-100]
Explanation: 
rotate 1 steps to the right: [99,-1,-100,3]
rotate 2 steps to the right: [3,99,-1,-100]

Note:
1. Try to come up as many solutions as you can, there are at least 3 different ways to solve this problem.
2. Could you do it in-place with O(1) extra space?


##  solution 1
```java
class Solution {
    public void rotate(int[] nums, int k) {
        int[] a = new int[nums.length];
        for (int i = 0; i < nums.length; i++) {
            a[(i + k) % nums.length] = nums[i];
        }
        for (int i = 0; i < nums.length; i++) {
            nums[i] = a[i];
        }
    }
}
```

##  solution 2 Reverse
reverse三次这个数组就可以得到最终结果

```java
    public void rotate(int[] nums, int k) {
        k = k%nums.length;
        reverse(nums, 0, nums.length - 1);
        reverse(nums, 0, k - 1);
        reverse(nums, k, nums.length - 1);
    }
    
    public void reverse(int[] nums, int from, int to) {
        while (from < to) {
            int tmp = nums[from];
            nums[from] = nums[to];
            nums[to] = tmp;
            from++;
            to--;
        }
    }
```

##   796. Rotate String
We are given two strings, A and B.
A shift on A consists of taking string A and moving the leftmost character to the rightmost position. For example, if A = 'abcde', then it will be 'bcdea' after one shift on A. Return True if and only if A can become B after some number of shifts on A.

Example 1:
Input: A = 'abcde', B = 'cdeab'
Output: true

Example 2:
Input: A = 'abcde', B = 'abced'
Output: false

Note:
A and B will have length at most 100.

##  solution
```java
class Solution {
    public boolean rotateString(String A, String B) {
        if (A.length() != B.length() || A == null || B == null) {
            return false;
        }
        if (A.length() == 0) {
            return true;
        }
        for (int i = 1; i < A.length(); i++) {
            if (A.substring(0,i).equals(B.substring(B.length() - i)) &&
                A.substring(i).equals(B.substring(0, B.length() - i))) {
                return true;
            }
        }
        return false;
    }
}
```

