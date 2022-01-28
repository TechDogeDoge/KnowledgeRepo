#  九章算法基础 L3 Binary Tree & Divide Conquer
[TOC]

##  1

算法时间复杂度
<img src="https://img-blog.csdnimg.cn/20191227045950424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom: 50%;" />

<img src="https://img-blog.csdnimg.cn/20191227050011368.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom:50%;" />

<img src="https://img-blog.csdnimg.cn/20191227050039365.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom: 40%;" />

## 2 Binary Tree Traversal
### 2.1 Pre-order Traversal
```java
/**
 * Definition of TreeNode:
 * public class TreeNode {
 *     public int val;
 *     public TreeNode left, right;
 *     public TreeNode(int val) {
 *         this.val = val;
 *         this.left = this.right = null;
 *     }
 * }
 */
// recursive traversal
public class Solution {
    /**
     * @param root: A Tree
     * @return: Preorder in ArrayList which contains node values.
     */
    public List<Integer> preorderTraversal(TreeNode root) {
        // write your code here
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;
        traverse(root, res);
        return res;
    }
    
    // add the preorder traverse that start with root to the list
    public void traverse(TreeNode root, List<Integer> res) {
        if (root == null) return;
        res.add(root.val);
        traverse(root.left, res);
        traverse(root.right, res);
    }
}

// Stack, DFS
public class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        Stack<TreeNode> stack = new Stack<TreeNode>();
        List<Integer> preorder = new ArrayList<Integer>();
        
        if (root == null) {
            return preorder;
        }
        
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            preorder.add(node.val);
            if (node.left != null) {
                stack.push(node.left);
            }
            if (node.right != null) {
                stack.push(node.right);
            }
        }
        
        return preorder;
    }
}
// divide and conquer
public class Solution {
    public ArrayList<Integer> preorderTraversal(TreeNode root) {
        ArrayList<Integer> result = new ArrayList<Integer>();
        // null or leaf
        if (root == null) {
            return result;
        }

        // Divide
        ArrayList<Integer> left = preorderTraversal(root.left);
        ArrayList<Integer> right = preorderTraversal(root.right);

        // Conquer
        result.add(root.val);
        result.addAll(left);
        result.addAll(right);
        return result;
    }
}
```
### 2.2 In-order Traversal
```java
public class Solution {
    /**
     * @param root: A Tree
     * @return: Inorder in ArrayList which contains node values.
     */
    public List<Integer> inorderTraversal(TreeNode root) {
        // write your code here
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;
        traverse(res, root);
        return res;
    }
    
    public void traverse(List<Integer> res, TreeNode node) {
        if (node == null) return;
        traverse(res, node.left);
        res.add(node.val);
        traverse(res, node.right);
    }
}
// recursive
public class Solution {
    /**
     * @param root: A Tree
     * @return: Inorder in ArrayList which contains node values.
     */
    public ArrayList<Integer> inorderTraversal(TreeNode root) {
        ArrayList<Integer> result = new ArrayList<Integer>();
        if (root == null) return result;
        
        ArrayList<Integer> left = inorderTraversal(root.left);
        ArrayList<Integer> right = inorderTraversal(root.right);
        
        result.addAll(left);
        result.add(root.val);
        result.addAll(right);
        
        return result;
    }
}

// Stack, non-recursive
public class Solution {
    public ArrayList<Integer> inorderTraversal(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        ArrayList<Integer> result = new ArrayList<>();
        
        while (root != null) {
            stack.push(root);
            root = root.left;
        }
    
        while (!stack.isEmpty()) {
            TreeNode node = stack.peek();
            result.add(node.val);
            
            if (node.right == null) {
                node = stack.pop();
                while (!stack.isEmpty() && stack.peek().right == node) {
                    node = stack.pop();
                }
            } else {
                node = node.right;
                while (node != null) {
                    stack.push(node);
                    node = node.left;
                }
            }
        }
        return result;
    }
}

public class Solution {
    /**
     * @param root: The root of binary tree.
     * @return: Inorder in ArrayList which contains node values.
     */
    public ArrayList<Integer> inorderTraversal(TreeNode root) {
        Stack<TreeNode> stack = new Stack<TreeNode>();
        ArrayList<Integer> result = new ArrayList<Integer>();
        TreeNode curt = root;
        while (curt != null || !stack.empty()) {
            while (curt != null) {
                stack.add(curt);
                curt = curt.left;
            }
            curt = stack.pop();
            result.add(curt.val);
            curt = curt.right;
        }
        return result;
    }
}
```
### 2.3 Post-order Traversal
```java
public ArrayList<Integer> postorderTraversal(TreeNode root) {
    ArrayList<Integer> result = new ArrayList<Integer>();
    Stack<TreeNode> stack = new Stack<TreeNode>();
    TreeNode prev = null; // previously traversed node
    TreeNode curr = root;

    if (root == null) {
        return result;
    }

    stack.push(root);
    while (!stack.isEmpty()) {
        curr = stack.peek();
        if (prev == null || prev.left == curr || prev.right == curr) { // traverse down the tree
            if (curr.left != null) {
                stack.push(curr.left);
            } else if (curr.right != null) {
                stack.push(curr.right);
            }
        } else if (curr.left == prev) { // traverse up the tree from the left
            if (curr.right != null) {
                stack.push(curr.right);
            }
        } else { // traverse up the tree from the right
            result.add(curr.val);
            stack.pop();
        }
        prev = curr;
    }

    return result;
}
```

divide conquer 和 traversal的区别是什么？
举个例子：假如说要调查全国人口总数。采用divide conquer就是说把任务下发到每个省，每个省得到的结果汇总在一起得到全国的结果；而采用traversal就是派一个人到每个去调查每个省的人口，一个一个人口的调查，最后得到最终结果。
通常来说，divide conquer会采用返回值，而traversal就是把结果存在一个parameter里面
##  3 
divide conquer也分为三个步骤：递归的定义，递归的拆解，递归的终止
==Merge Sort and Quick Sort 要背下来代码以及细节==
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191227050334995.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
## 4
碰到二叉树的问题，想一想整棵树在该问题上的结果，和左右child在该问题上的结果之间的联系是什么

### 例题：104. Maximum Depth of Binary Tree
Given a binary tree, find its maximum depth.

The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

Note: A leaf is a node with no children.

Example:
Given binary tree [3,9,20,null,null,15,7],
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229043253945.png)
return its depth = 3.

###  solution 1: recursive traversal
最直接的想法，遍历整个二叉树，找到最大深度
```java
class Solution {
    // public int maxDepth(TreeNode root) {
    //     if (root == null) return 0;
    //     int res = 0;
    //     res = Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
    //     return res;
    // }
    
    private int depth;
    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        depth = 0;
        helper(root, 1);
        return depth;
    }
    
    private void helper(TreeNode root, int curDepth) {
        if (root == null) {
            return;
        }
        
        depth = Math.max(depth, curDepth);
        helper(root.left, curDepth + 1);
        helper(root.right, curDepth + 1);
    }
}
```

###  solution 2: recursive divide conquer
```java
    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        int res = 0;
        int left = maxDepth(root.left);
        int right = macDepth(root.right);
        res = Math.max(left, right) + 1;
        return res;
    }
```

### 例题 257. Binary Tree Paths
Given a binary tree, return all root-to-leaf paths.

Note: A leaf is a node with no children.
Example:
Input:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229043344405.png)
Output: ["1->2->5", "1->3"]
Explanation: All root-to-leaf paths are: 1->2->5, 1->3

##  solution 1: traversal
借助helper function遍历，同时把遇到的leaf path放入到result中
```java
class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> result = new ArrayList<String>();
        if (root == null) return result;
        StringBuffer tmp = new StringBuffer();
        helper(root, result, tmp);
        return result;
    }
    
    private void helper(TreeNode root, List<String> result, StringBuffer cur) {
        if (cur.length() == 0) {
            cur.append("" + root.val);
        } else {
            cur.append("->" + root.val);
        }
        
        if (root.left == null && root.right == null) {
            result.add(cur.toString());
            return;
        }
        
        if (root.left != null) {
            helper(root.left, result, new StringBuffer(cur));
        }
        if (root.right != null) {
            helper(root.right, result, new StringBuffer(cur));
        }
    }
}
```

##  solution 2:  divide conquer
采用divide conquer的思路，每一步返回最终结果的一部分，最后组装起来。分别有两种写法
根据题目定义，binaryTreePaths(TreeNode root)返回从 root 点开始到 leaf 的所有路径。于是拆分时可以分为 left 和 right 两部分。以 root 为起始点的结果就等于在 root.left 和 root.right 的所有结果前面加上 root 点，这就是递归的条件。
最后递归的结束有两种情况，第一种是 root 是 null，那么返回值应该是一个空的ArrayList，第二种情况，root 是 left 点,此时，只有一条path，而且这个path上只有一个点，返回这唯一的一个path。
```java    
   public List<String> binaryTreePaths(TreeNode root) {
       if (root == null) return new ArrayList<String>();
       List<String> result = new ArrayList<String>();
       
       if (root.left == null && root.right == null) {
           result.add(root.val + "");
           return result;
       }
       
       List<String> left = binaryTreePaths(root.left);
       List<String> right = binaryTreePaths(root.right);
       for (String str : left) {
           result.add(root.val + "->" + str);
       }      

       for (String str : right) {
           result.add(root.val + "->" + str);
       }
       return result;
   }
```

### 例题 Minimum Subtree
Given a binary tree, find the subtree with minimum sum. Return the root of the subtree.

##  solution
求subtree的和采用divide conquer，求最小值可以采用traverse，定义一个全局变量。

```java
    private TreeNode minNode;
    private int sum = Integer.MAX_VALUE;
    
    public TreeNode findSubtree(TreeNode root) {
        helper(root);
        return sum; 
    }
    
    private void helper(TreeNode root) {
        if (root == null) return 0;
        
        int res;
        res = helper(root.left) + helper(root.right) + root.val;
        
        if (res < sum) {
            sum = res;
            minNode = root;
        }

    }
```

#  5 result type
如何处理运用divide conquer时，返回值不止一个的情况

### 例题 110. Balanced Binary Tree
Given a binary tree, determine if it is height-balanced.

For this problem, a height-balanced binary tree is defined as: a binary tree in which the left and right subtrees of every node differ in height by no more than 1.

Example 1:
Given the following tree [3,9,20,null,null,15,7]:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229043420185.png)
Return true.

Example 2:
Given the following tree [1,2,2,3,3,null,null,4,4]:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229043430622.png)
Return false.

##  solution 
这题可以采用divide conquer，当前 root 是否balanced取决于左右两个 subtree ，当两个subtree都是balanced同时两个subtree depth之差小于等于1时，root是balanced。
但是每一步需要返回两个值，首先是否balanced，其次是最大depth。于是要求我们返回两个值，于是我们定义了新的 class 叫做resultType用来存储两个结果。
还有一种取巧的办法，就是只返回 depth 值，当不balanced时，返回 depth 值为-1，否则返回值为正的 depth。

```java
class resultType {
    public boolean isBalanced;
    public int depth;
    public resultType(boolean isBalanced, int depth) {
        this.isBalanced = isBalanced;
        this.depth = depth;
    }
}

class Solution {
    public boolean isBalanced(TreeNode root) {
        resultType res = helper(root);
        return res.isBalanced;
    }
    
    // helper function return the pre-defined resultType
    public resultType helper(TreeNode root) {
        if (root == null) return new resultType(true, 0);
        
        resultType left = helper(root.left);
        resultType right = helper(root.right);
        int length = Math.max(left.depth, right.depth) + 1;
        
        if (left.isBalanced == false || right.isBalanced == false)  {
            return new resultType(false, length);
        } 
       
        if (Math.abs(left.depth - right.depth) > 1) {
            return new resultType(false, length);
        }
        
        return new resultType(true, length);
    }
}
```


### 例题 1120. Maximum Average Subtree
Given the root of a binary tree, find the maximum average value of any subtree of that tree.
(A subtree of a tree is any node of that tree plus all its descendants. The average value of a tree is the sum of its values, divided by the number of nodes.)

 Example 1:
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20191227113725648.png)
Input: [5,6,1]
Output: 6.00000
Explanation: 
For the node with value = 5 we have an average of (5 + 6 + 1) / 3 = 4.
For the node with value = 6 we have an average of 6 / 1 = 6.
For the node with value = 1 we have an average of 1 / 1 = 1.
So the answer is 6 which is the maximum.
Note:
The number of nodes in the tree is between 1 and 5000.
Each node will have a value between 0 and 100000.
Answers will be accepted as correct if they are within 10^-5 of the correct answer.

### Solution
必须定义result type，因为返回值至少有两个，subtree节点个数以及 sum 值.
同理可以写出代码

```java
class resultType {
    public int num;
    public int sum;
    
    public resultType(int num, int sum) {
        this.sum = sum;
        this.num = num;
    }
}

class Solution {
    double ave = 0.0;
    
    public double maximumAverageSubtree(TreeNode root) {
        resultType res = helper(root);
        return ave;
    }
    
    public resultType helper(TreeNode root) {
        if (root == null) {
            return new resultType(0, 0);
        }
        
        resultType left = helper(root.left);
        resultType right = helper(root.right);
        int num_sum = left.num + right.num + 1;
        int sum_sum = left.sum + right.sum + root.val;
            
        double tmp = sum_sum/1.0/num_sum;
        ave = Math.max(tmp, ave);
        
        return new resultType(num_sum, sum_sum);
    }
}
```

### 例题 114. Flatten Binary Tree to Linked List
Given a binary tree, flatten it to a linked list in-place.
For example, given the following tree:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229043511931.png)
The flattened tree should look like:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229043536832.png)

##  solution 1 traverse
观察发现采用的是 preorder 的 traversal。


##  solution 2 divide conquer
采用 divide conquer 方法，当前 root 变成要求的样子，可以先把左右子树变成要求的样子，然后合并起来，左子树接在 root 后面，右子树接在左子树的后面
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229044746384.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
需要注意的是，把左子树接到右边时，会覆盖原本的右子树，所以需要把原本的右子树提前存起来

```java
class Solution {
    public void flatten(TreeNode root) {
        if (root == null) return;
        
        flatten(root.left);
        flatten(root.right);
        
        TreeNode tmp = root.right;
        root.right = root.left;
        root.left = null;
        TreeNode ptr = root;
        
        while(ptr.right != null) {
            ptr = ptr.right;
        }
        
        ptr.right = tmp;
    }
}
```
还有一种写法，定义一个helper function，作用和 flatten 相同，唯一的区别是返回值改成返回最后一个 node。

### 例题 236. Lowest Common Ancestor of a Binary Tree
Given a binary tree, find the lowest common ancestor (LCA) of two given nodes in the tree.
According to the definition of LCA on Wikipedia: “The lowest common ancestor is defined between two nodes p and q as the lowest node in T that has both p and q as descendants (where we allow a node to be a descendant of itself).”

Given the following binary tree:  root = [3,5,1,6,2,0,8,null,null,7,4]
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229051217562.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
Example 1:
Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
Output: 3
Explanation: The LCA of nodes 5 and 1 is 3.

Example 2:
Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
Output: 5
Explanation: The LCA of nodes 5 and 4 is 5, since a node can be a descendant of itself according to the LCA definition.

Note:
All of the nodes' values will be unique.
p and q are different and both values will exist in the binary tree.

==首先需要问考官是否有parent pointer==
如果有parent pointer，那么问题很简单，只需要向上追溯 p 和 q 的起始点，然后追溯路径上第一个出现相同的点就是 LCA。

如果没有parent pointer，相对难一些

```java
    // if lca is found, return lca
    // if find q, return q
    // if find p, return p
    // else return null
    
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) {
            return null;
        }
        
        if (root == p || root == q) {
            return root;
        }
        
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        
        if (left != null && right != null) {
            return root;
        }
        
        if (left != null) {
            return left;
        }
        
        if (right != null) {
            return right;
        }
        
        return null;
    }
```

==这个题还有一种变种方式，就是考虑可能不存在 Lowest Common Ancestor 的情况，如果不存在，返回 null。==

### 例题 298. Binary Tree Longest Consecutive Sequence
Given a binary tree, find the length of the longest consecutive sequence path.

The path refers to any sequence of nodes from some starting node to any node in the tree along the parent-child connections. The longest consecutive path need to be from parent to child (cannot be the reverse). (根据后面提供的示例可以判断这里的consecutive是指连续递增，递减的不算)
Example 1:

Input:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229060032940.png)
Output: 3
Explanation: Longest consecutive sequence path is 3-4-5, so return 3.

Example 2:

Input:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229060052962.png)
Output: 2 
Explanation: Longest consecutive sequence path is 2-3, not 3-2-1, so return 2.

##  solution
采用divide conquer 和 traverse 结合的方法
```java
class Solution {
    public int longest = 0;
    
    public int longestConsecutive(TreeNode root) {
        helper(root);
        return longest;
    }
    
    public int helper(TreeNode root) {
        if (root == null) {
            return 0;
        }
        
        int left = helper(root.left);
        int right = helper(root.right);
        
        int curLen = 1;
        
        if (root.left != null && root.val + 1 == root.left.val) {
            curLen = Math.max(left + 1, curLen);
        }
        
        if (root.right != null && root.val + 1 == root.right.val) {
            curLen = Math.max(right + 1, curLen);
        }
        
        longest = Math.max(curLen, longest);
        
        return curLen;
    }
}
```

```java
class Solution {
    //  preorder traversal
    private int maxLength = 0;
    public int longestConsecutive(TreeNode root) {
        dfs(root, null, 0);
        return maxLength;
    }

    private void dfs(TreeNode p, TreeNode parent, int length) {
        if (p == null) return;
        length = (parent != null && p.val == parent.val + 1) ? length + 1 : 1;
        maxLength = Math.max(maxLength, length);
        dfs(p.left, p, length);
        dfs(p.right, p, length);
    }
}
```

### 延申 549. Binary Tree Longest Consecutive Sequence II
Given a binary tree, you need to find the length of Longest Consecutive Path in Binary Tree.

Especially, this path can be either increasing or decreasing. For example, [1,2,3,4] and [4,3,2,1] are both considered valid, but the path [1,2,4,3] is not valid. On the other hand, the path can be in the child-Parent-child order, where not necessarily be parent-child order.

Example 1:
Input:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229094153806.png)
Output: 2
Explanation: The longest consecutive path is [1, 2] or [2, 1].


Example 2:
Input:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229094216874.png)
Output: 3
Explanation: The longest consecutive path is [1, 2, 3] or [3, 2, 1].

Note: All the values of tree nodes are in the range of [-1e7, 1e7].

###  Solution
连续序列可以分为三种情况：由左子树得到的增减序列，由右子树得到的增减序列，经过root和左右子树得到的增减序列

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    int maxval = 0;
    public int longestConsecutive(TreeNode root) {
        longestPath(root);
        return maxval;
    }
	
	// 返回值为最长增序列和最长减序列
    public int[] longestPath(TreeNode root) {
        if (root == null)
            return new int[] {0,0};
        int inr = 1, dcr = 1;
        if (root.left != null) {
            int[] l = longestPath(root.left);
            if (root.val == root.left.val + 1)
                dcr = l[1] + 1; // 更新减序列长度
            else if (root.val == root.left.val - 1)
                inr = l[0] + 1; // 更新增序列长度
        }
        if (root.right != null) {
            int[] r = longestPath(root.right);
            if (root.val == root.right.val + 1)
                dcr = Math.max(dcr, r[1] + 1); // 更新减序列长度
            else if (root.val == root.right.val - 1)
                inr = Math.max(inr, r[0] + 1); // 更新增序列长度
        }
        // 这里直接采用dcr + inr - 1计算长度是因为，inc/dcr初值为1，只有在子树和root形成序列才会被更新 
        maxval = Math.max(maxval, dcr + inr - 1); 
        return new int[] {inr, dcr};
    }
}
```

### 例题 Binary tree path sum
Given a binarytree, find all the paths that sum of the nodes in the path equals to a given number target.
A valid path is from root node to any leaf nodes.

Example:
Given a binary tree, and target = 5
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229095344787.png)
return 
```java
[
	{1, 4},
	{1, 2, 2}
]
```

##  solution : traversal
很直观的想法就是遍历每一条到 leaf 的 path 找到其中 sum 等于 target 的 path。
这题和前面做过的 subset 的解法是一样的，同样需要注意的是，遍历左右两个 subtree 时，注意 path add以后需要再删除。
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
    
    public void helper(TreeNode root, int target, ArrayList<Integer> path, List<List<Integer>> result, int sum) {
        if (root.left == null && root.right == null) {
            if (sum == target) {
                result.add(new ArrayList<Integer>(path));
            }
            return;
        }
        
        if (root.left != null) {
            path.add(root.left.val);
            helper(root.left, target, path, result, sum + root.left.val);
            path.remove(path.size - 1);
        }
        
        if (root.right != null) {
            path.add(root.right.val);
            helper(root.right, target, path, result, sum + root.right.val);
            path.remove(path.size - 1);
        }
    }
}
```

#  Binary Search Tree
Binary Search Tree 是一种特殊的 Binary Tree，它的要求是，root 左子树中的值都小于 root，root 右子树的值都大于 root。

## 例题 98. Validate Binary Search Tree
Given a binary tree, determine if it is a valid binary search tree (BST).

Assume a BST is defined as follows:

The left subtree of a node contains only nodes with keys less than the node's key.
The right subtree of a node contains only nodes with keys greater than the node's key.
Both the left and right subtrees must also be binary search trees.

Example 1:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229103003226.png)
Input: [2,1,3]
Output: true

Example 2:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229103007921.png)
Input: [5,1,4,null,null,3,6]
Output: false
Explanation: The root node's value is 5 but its right child's value is 4.

##  solution 1 divide conquer
root 是 Binary Search Tree 要求两个子树都是 Binary Search Tree，同时左子树中的最大值小于 root，右子树中的最小值大于 root
需要注意的是当 left 或者 right 为 null 时，给定的值要定义为 Double 型，因为这样可以避免 root 值恰好为 Integer.MAX_VALUE 时出现问题
```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        if (root == null) {
            return true;
        }
        
        if (!isValidBST(root.left) || !isValidBST(root.right)) {
            return false;
        }
        
        TreeNode left = root.left;
        double left_max;
        if (left == null) {
            left_max = -Double.MAX_VALUE;
        } else {
            while (left.right != null) {
            	left = left.right;
            }
            left_max = left.val;
        }
        
        TreeNode right = root.right; 
        double right_min;
        if (right == null) {
            right_min = Double.MAX_VALUE;
        } else {
            while (right.left != null) {
                right = right.left;
            }
            right_min = right.val;            
        }

        
        if (left_max < root.val && right_min > root.val) {
            return true;
        }
        
        return false;
    }
}
```

还有一种写法可以定义 helper method 返回三个值，以当前点为 root 的 subtree 是不是BST，subtree 的最大值最小值
这种写法稍微长一些，需要定义resultType

LeetCode 提供了一种写法，不需要定义resultType
```java
class Solution {
	// 以node为根节点，是否所有子节点对的值在lower到upper之间，(lower, upper)
	public boolean helper(TreeNode node, Integer lower, Integer upper) {
	  if (node == null) return true;
	
	  int val = node.val;
	  if (lower != null && val <= lower) return false; 
	  if (upper != null && val >= upper) return false;
	
	  if (! helper(node.right, val, upper)) return false;
	  if (! helper(node.left, lower, val)) return false;
	  return true;
	}

  public boolean isValidBST(TreeNode root) {
    return helper(root, null, null);
  }
}
```

### Solution 2
中序遍历时，判断当前节点是否大于中序遍历的前一个节点，如果大于，说明满足 BST，继续遍历；否则直接返回 false。
```java
class Solution {
    long pre = Long.MIN_VALUE;
    public boolean isValidBST(TreeNode root) {
        if (root == null) {
            return true;
        }
        // 访问左子树
        if (!isValidBST(root.left)) {
            return false;
        }
        // 访问当前节点：如果当前节点小于等于中序遍历的前一个节点，说明不满足BST，返回 false；否则继续遍历。
        if (root.val <= pre) {
            return false;
        }
        pre = root.val;
        // 访问右子树
        return isValidBST(root.right);
    }
}
```

## 例题 426. Convert Binary Search Tree to Sorted Doubly Linked List
Convert a Binary Search Tree to a sorted Circular Doubly-Linked List in place.

You can think of the left and right pointers as synonymous to the predecessor and successor pointers in a doubly-linked list. For a circular doubly linked list, the predecessor of the first element is the last element, and the successor of the last element is the first element.

We want to do the transformation in place. After the transformation, the left pointer of the tree node should point to its predecessor, and the right pointer should point to its successor. You should return the pointer to the smallest element of the linked list.

Example 1:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229111330890.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
Input: root = [4,2,5,1,3]
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229111347896.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
Output: [1,2,3,4,5]
Explanation: The figure below shows the transformed BST. The solid line indicates the successor relationship, while the dashed line means the predecessor relationship.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191229111426834.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
Example 2:
Input: root = [2,1,3]
Output: [1,2,3]

Example 3:
Input: root = []
Output: []
Explanation: Input is an empty tree. Output is also an empty Linked List.

Example 4:
Input: root = [1]
Output: [1]


Constraints: 
1. -1000 <= Node.val <= 1000
2. Node.left.val < Node.val < Node.right.val
3. All values of Node.val are unique.
4. 0 <= Number of Nodes <= 2000


###  Solution
```java
class Solution {
	// the smallest (first) and the largest (last) nodes
	Node first = null;
	Node last = null;

	// 完成以node为起始转换成doublely linked list
	public void helper(Node node) {
	  if (node != null) {
	    // left
	    helper(node.left);
	    // node 
	    if (last != null) {
	      // link the previous node (last)
	      // with the current one (node)
	      last.right = node;
	      node.left = last;
	    }
	    else {
	      // keep the smallest node
	      // to close DLL later on
	      first = node;
	    }
	    last = node;
	    // right
	    helper(node.right);
	  }
	}

	public Node treeToDoublyList(Node root) {
		if (root == null) return null;
		
		helper(root);
		// close DLL
		last.right = first;
		first.left = last;
		return first;
	}
}
```


### Solution 2
```java
/**
 * Definition of TreeNode:
 * public class TreeNode {
 *     public int val;
 *     public TreeNode left, right;
 *     public TreeNode(int val) {
 *         this.val = val;
 *         this.left = this.right = null;
 *     }
 * }
 * Definition for Doubly-ListNode.
 * public class DoublyListNode {
 *     int val;
 *     DoublyListNode next, prev;
 *     DoublyListNode(int val) {
 *         this.val = val;
 *         this.next = this.prev = null;
 *     }
 * }
 */ 
 
class ResultType {
    DoublyListNode first, last;
    
    public ResultType(DoublyListNode first, DoublyListNode last) {
        this.first = first;
        this.last = last;
    }
}

public class Solution {
    /**
     * @param root: The root of tree
     * @return: the head of doubly list node
     */
    public DoublyListNode bstToDoublyList(TreeNode root) {  
        if (root == null) {
            return null;
        }
        
        ResultType result = helper(root);
        return result.first;
    }
    
    //中序遍历函数 
    public ResultType helper(TreeNode root) {  
        if (root == null) {
            return null;
        }
        
        ResultType left = helper(root.left);
        ResultType right = helper(root.right);
        DoublyListNode node = new DoublyListNode(root.val);
        
        ResultType result = new ResultType(null, null);
        
        //构造单链表
        if (left == null) {
            result.first = node;
        } else {
            result.first = left.first;
            left.last.next = node;
            node.prev = left.last;
        }
        
        if (right == null) {
            result.last = node;
        } else {
            result.last = right.last;
            right.first.prev = node;
            node.next = right.first;
        }
        return result;
    }
}
```