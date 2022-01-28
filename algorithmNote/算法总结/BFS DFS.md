# BFS DFS

[TOC]

## BFS

**BFS**就是广度优先算法

### 使用场景

1. 图的遍历 traversal in graph
    1.1 层级遍历 level order traversal
    1.2 由点及面 connected component
    1.3 拓扑排序 topological sorting
2. 最短路径 shortest path in simple graph
    2.1 仅限简单图求最短路径，即途中每条边长度都是 1且没有方向

### BFS模板

```java
void BFS(Node root， result) {
    Queue<Node> q;
    Set<Node> visited;
    q.offer(root);
    visited.add(root);
    int step = 0;
    
    while (!q.isEmpty()) {
        int size = q.size();
        step++;
        for (int i = 0; i < size; i++) {
            Node node = q.poll();
            /*
            	对 node 进行各种判断操作等
            */
            for (Node next : node.next()) {
                if (!visited.contains(next)) {
                    q.offer(next);
                }
            }
        }
    } 
}
```

### 补充知识

`BFS`采用 `FIFO Queue` 实现

在`Java`中，`Queue` 是一个接口，具体实现可以采用`LinkedList`

> LinkedList API

| 函数名           | 返回值  | 说明                                                         |
| ---------------- | ------- | ------------------------------------------------------------ |
| remove()         | Item E  | Retrieves and removes the head (first element) of this list  |
| size()           | int     | Returns the number of elements in this list                  |
| isEmpty()        | boolean | Adds the specified element as the tail (last element) of this list |
| offer(Item)      | boolean | Adds the specified element as the tail (last element) of this list |
| poll()           | Item    | Retrieves and removes the head (first element) of this list  |
| add(Item)        | boolean | Appends the specified element to the end of this list        |
| add(index, Item) | void    | Inserts the specified element at the specified position in this list |

> 利用LinkedList 实现 FIFO Queue 可以使用 offer(), poll()组合，从最后面加入元素，从最前面取出元素；或者使用 add(), add 和 offer() 是一样的。

## DFS

**DFS**就是深度优先算法

事实上，DFS和回溯法并没有什么区别。两者都是在遍历的过程中搜索解。

<img src="C:\Users\jiaoy\AppData\Roaming\Typora\typora-user-images\image-20211107114221369.png" alt="image-20211107114221369" style="zoom: 40%;" />

### 使用场景

DFS通常使用在需要求所有可行解的题目，且为排列或者组合.

**排列需要考虑取出物品的排列顺序；而组合则不关心取出物品的排列顺序，只关心取出了哪些物品。**

### DFS模板

```java
void DFS(当前路径, 分支列表，result) {
    if (到达终点或者满足结束条件) {
        result.add(当前路径);
        return;
    }
       
    for (分支i : 所有分支) {
        选择分支i并更新当前路径
        backtrack(当前路径，路径列表，result)
        撤销选择i并更新路径
    }
        
}
// 对于只需要找到一个可行解的问题，找到一个可行解就返回，阻断后面的遍历
bool backtrack(当前路径，分支选择列表，result) {
    if (到达终点或者满足结束条件) {
        result.add(当前路径)；
        return true;
    }
    
    for (分支i : 分支选择列表) {
        选择分支i并更新当前路径
        if (backtrack(当前路径，分支列表，result)) {
            return true;
        }
        撤销选择分支i并更新路径
    }
}
```

## 例题

### BFS in Binary Tree

#### 102. Binary Tree Level Order Traversal

Given the `root` of a binary tree, return *the level order traversal of its nodes' values*. (i.e., from left to right, level by level).

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/02/19/tree1.jpg" alt="img" style="zoom:67%;" />

```
Input: root = [3,9,20,null,null,15,7]
Output: [[3],[9,20],[15,7]]
```

**Example 2:**

```
Input: root = [1]
Output: [[1]]
```

> 解法

    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();
        if (root == null) return result;
        Queue<TreeNode> fifo = new LinkedList<>();
        fifo.offer(root);
        
        while (!fifo.isEmpty()) {
            int size = fifo.size();
            ArrayList<Integer> list = new ArrayList<>();
            for (int i = 0; i < size; i++) {
                TreeNode cur = fifo.poll();
                if (cur.left != null) {
                    fifo.offer(cur.left);
                }
                if (cur.right != null) {
                    fifo.offer(cur.right);
                }
                list.add(cur.val);
            }
            result.add(list);
        }
        return result;
    }

还可以采用递归的写法，但是不推荐

```java
class Solution {
    List<List<Integer>> levels = new ArrayList<List<Integer>>();

    public void helper(TreeNode node, int level) {
        // start the current level
        if (levels.size() == level)
            levels.add(new ArrayList<Integer>());

         levels.get(level).add(node.val);

         if (node.left != null) helper(node.left, level + 1);
         if (node.right != null) helper(node.right, level + 1);
    }
    
    public List<List<Integer>> levelOrder(TreeNode root) {
        if (root == null) return levels;
        helper(root, 0);
        return levels;
    }
}
```

#### 297. Serialize and Deserialize Binary Tree

Serialization is the process of converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted across a network connection link to be reconstructed later in the same or another computer environment.

Design an algorithm to serialize and deserialize a binary tree. There is no restriction on how your serialization/deserialization algorithm should work. You just need to ensure that a binary tree can be serialized to a string and this string can be deserialized to the original tree structure.

**Clarification:** The input/output format is the same as [how LeetCode serializes a binary tree](https://leetcode.com/faq/#binary-tree). You do not necessarily need to follow this format, so please be creative and come up with different approaches yourself.

 

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2020/09/15/serdeser.jpg" alt="img" style="zoom:67%;" />

```
Input: root = [1,2,3,null,null,4,5]
Output: [1,2,3,null,null,4,5]
```

> 解法

序列化和反序列化都采用`BFS`层序遍历
题目所给的二叉树会被序列化为 `1,2,3,#,#,4,5,#,#,#,#,`

```java
public class Codec {
    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuffer string = new StringBuffer();
        if (root == null) return string.toString();
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                if (node == null) {
                    string.append("#,");
                } else {
                    string.append(node.val + ",");
                    queue.add(node.left);
                    queue.add(node.right);
                }
            }
        }
        return string.toString();
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if (data.length() == 0) return null;
        String[] list = data.split(",");
        TreeNode root = new TreeNode(Integer.parseInt(list[0]));
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        int i = 1;
        while (i < list.length) {
            TreeNode node = queue.poll();
            String left = list[i];
            String right = list[i+1];
            if (!left.equals("#")) {
                TreeNode leftNode = new TreeNode(Integer.parseInt(left));
                node.left = leftNode;
                queue.add(leftNode);
            }
            if (!right.equals("#")) {
                TreeNode rightNode = new TreeNode(Integer.parseInt(right));
                node.right = rightNode;
                queue.add(rightNode);
            }
            i += 2;
        }
        return root;
    }
}
```

#### 449. Serialize and Deserialize BST

Serialization is converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted across a network connection link to be reconstructed later in the same or another computer environment.

Design an algorithm to serialize and deserialize a **binary search tree**. There is no restriction on how your serialization/deserialization algorithm should work. You need to ensure that a binary search tree can be serialized to a string, and this string can be deserialized to the original tree structure.

**The encoded string should be as compact as possible.**

 

**Example 1:**

```
Input: root = [2,1,3]
Output: [2,1,3]
```

**Example 2:**

```
Input: root = []
Output: []
```

> 解法

题目有两种解法：

1. 采用前面一题的`BFS`层序遍历；
2. 采用二叉树的先序遍历；

> 虽然二叉树有三种遍历方式，但是只有采用先序遍历才可以通过遍历得到的序列反过来构建二叉树

第一种解法和前面的写法一模一样，不再赘述。

```java
```

### BFS in graph

#### 261. Graph Valid Tree

Given `n` nodes labeled from `0` to `n-1` and a list of undirected edges (each edge is a pair of nodes), write a function to check whether these edges make up a valid tree.

**Example 1:**

```
Input: n = 5, and edges = [[0,1], [0,2], [0,3], [1,4]]
Output: true
```

**Example 2:**

```
Input: n = 5, and edges = [[0,1], [1,2], [2,3], [1,3], [1,4]]
Output: false
```

Note: you can assume that no duplicate edges will appear in edges. Since all edges are undirected, `[0,1]` is the same as `[1,0]` and thus will not appear together in edges.

> 解法

==Tree 的条件是有 n 个节点， n-1 个边， 同时所有点连通==

题目给的是边的列表，需要转换成图的邻接表的形式，然后进行判断

```java
class Solution {
    public boolean validTree(int n, int[][] edges) {
        if (n != edges.length + 1) {
            return false;
        }
        Queue<Integer> fifo = new LinkedList<Integer>();
        // set stores the nodes that are connected
        HashSet<Integer> set = new HashSet<>();
        Map<Integer, Set<Integer>> graph = graphGen(n, edges);
        
        fifo.add(0);
        set.add(0);
        while(!fifo.isEmpty()) {
            int node = fifo.poll();
            for (Integer neighbor : graph.get(node)) {
                // if the node, neighbor, is already added to the set, continue
                if (set.contains(neighbor)) {
                    continue;
                }
                fifo.offer(neighbor);
                set.add(neighbor);
            }
        }
        return (set.size() == n);
    }
    
    // 使用Map创建一个graph，采用邻接表的形式
    public Map<Integer, Set<Integer>> graphGen(int n, int[][] edges) {
        Map<Integer, Set<Integer>> graph = new HashMap<>();
        for (int i = 0; i < n; i++) {
            graph.put(i, new HashSet<Integer>());
        }
        for (int i = 0; i < edges.length; i++) {
            graph.get(edges[i][0]).add(edges[i][1]);
            graph.get(edges[i][1]).add(edges[i][0]);
        }
        return graph;
    }
}
```

#### 133. Clone Graph

Given a reference of a node in a **[connected](https://en.wikipedia.org/wiki/Connectivity_(graph_theory)#Connected_graph)** undirected graph.

Return a [**deep copy**](https://en.wikipedia.org/wiki/Object_copying#Deep_copy) (clone) of the graph.

Each node in the graph contains a value (`int`) and a list (`List[Node]`) of its neighbors.

```
class Node {
    public int val;
    public List<Node> neighbors;
}
```

**Test case format:**

For simplicity, each node's value is the same as the node's index (1-indexed). For example, the first node with `val == 1`, the second node with `val == 2`, and so on. The graph is represented in the test case using an adjacency list.

**An adjacency list** is a collection of unordered **lists** used to represent a finite graph. Each list describes the set of neighbors of a node in the graph.

The given node will always be the first node with `val = 1`. You must return the **copy of the given node** as a reference to the cloned graph.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2019/11/04/133_clone_graph_question.png" alt="img" style="zoom: 25%;" />

```
Input: adjList = [[2,4],[1,3],[2,4],[1,3]]
Output: [[2,4],[1,3],[2,4],[1,3]]
Explanation: There are 4 nodes in the graph.
1st node (val = 1)'s neighbors are 2nd node (val = 2) and 4th node (val = 4).
2nd node (val = 2)'s neighbors are 1st node (val = 1) and 3rd node (val = 3).
3rd node (val = 3)'s neighbors are 2nd node (val = 2) and 4th node (val = 4).
4th node (val = 4)'s neighbors are 1st node (val = 1) and 3rd node (val = 3).
```

> 解法

解题分为两步，首先利用`BFS`得到整个图中的所有节点；然后创建一个`Map`用来映射旧节点和对应的新节点，并更新新节点的邻接节点

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> neighbors;

    public Node() {}
    public Node(int _val,List<Node> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
};
*/
class Solution {
    public Node cloneGraph(Node node) {
        if (node == null) {
            return node;
        }
        ArrayList<Node> nodes = getNodes(node);
        // HashMap is used to store the old nodes and their according new nodes.
        HashMap<Node, Node> map = new HashMap<>();
        
        for (Node tmp : nodes) {
            map.put(tmp, new Node(tmp.val, new ArrayList<Node>()));
        }
        for (Node tmp : nodes) {
            Node newNode = map.get(tmp);
            for (Node neighbor : tmp.neighbors) {
                newNode.neighbors.add(map.get(neighbor));
            }
        }
        return map.get(node);
    }
    
    // return all the nodes in the graph
    public ArrayList<Node> getNodes(Node node) {
        Queue<Node> queue = new LinkedList<>();
        HashSet<Node> set = new HashSet<>();
        queue.offer(node);
        set.add(node);
        
        while(!queue.isEmpty()) {
            Node cur = queue.poll();
            for (Node tmp : cur.neighbors) {
                if (!set.contains(tmp)) {
                    set.add(tmp);
                    queue.offer(tmp);
                }
            }
        }
        return new ArrayList<Node>(set);
    }
}
```

#### Topological Sorting

可以用 DFS 但是不建议，采用 BFS 最合适
Topological sorting 就是给定一个有向图，将图中的节点进行排序，使得没有一个节点会指向它前面的节点，最后返回一个排好序的节点链表

1. 计算所有节点的入度
2. 找到入度为0的节点，即为拓扑排序的起始点，并将其放入拓扑排序链表中
3. 将入度为0的节点的邻接节点的入度全部减一
4. 重复步骤2和3，直到最后所有节点都被添加到链表中

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200105113318547.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)

```java
class Solution {
    public ArrayList<Node> topoSort(ArrayList<Node> graph) {
        ArrayList<Node> order = new ArrayList<>();
        if (graph == null) return order;
        
        // calculate the indegree of each node
        HashMap<Node, Integer> inDegree = getIndegree(graph);
        // get the starting nodes that have 0 indegree
        ArrayList<Node> startNodes = getStartNodes(inDegree, graph);
        // use bfs to find the topological order
        order = bfs(inDegree, startNodes);
        // check out whether the length of topological equals to the number of nodes
        // if not, there is loop in the graph
        return order.size() == graph.size() ? order : null;
    }
    
    public ArrayList<Node> getStartNodes(HashMap<Node, Integer> inDegree, ArrayList<Node> graph) {
        ArrayList<Node> nodes = new ArrayList<>();
        for (Node node : graph) {
            if (inDegree.get(node) == 0) {
                nodes.add(node);
            }
        }
        return nodes;
    }
    
    public HashMap<Node, Integer> getIndegree(ArrayList<Node> graph) {
        HashMap<Node, Integer> inDegree = new HashMap<>();
        for (Node node : graph) {
            inDegree.put(node, 0);
        }
        for (Node node : graph) {
            for (Node neighbor : node.neighbors) {
                inDegree.put(neighbor, inDegree.get(neighbor) + 1);
            }
        }
        return inDegree;
    }
    
    public ArrayList<Node> bfs(HashMap<Node, Integer> inDegree, ArrayList<Node> startNodes) {
        ArrayList<Node> order = new ArrayList<>();
        Queue<Node> fifo = new LinkedList<>();
        // add the start nodes into the fifo queue
        for (Node node : startNodes) {
            order.add(node);
            fifo.offer(node);
        }
        while (!fifo.isEmpty()) {
            Node ndoe = fifo.poll();
            for (Node neighbor : node.neighbors) {
                inDegree.put(neighbor, inDegree.get(neighbor) - 1);
                if (inDegree.get(neighbor) == 0) {
                    fifo.offer(neighbor);
                    order.add(neighbor);
                }
            }
        }
        return order;
    }
}
```

### BFS in 2D grid (matrix)

`2D grid (matrix)` 是一种特殊的 `graph`， 每一个格子就相当于 `graph` 中的一个点。连通情况有两种： 

1. 四连通：上下左右; 
2. 八连通：额外包括左上左下右上右下 

`2D grid (matrix)` 有 `R` 个行，`C `个列，共有 $R*C$ 个点，$R*C*2$ 条边
`2D grid (matrix)` 的 `BFS` 时间复杂度为 $O(R*C + R*C*2) = O(R*C)$

#### 200. Number of Islands

Given an `m x n` 2D binary grid `grid` which represents a map of `'1'`s (land) and `'0'`s (water), return *the number of islands*.

An **island** is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

**Example 1:**

```
Input: grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
Output: 1
```

**Example 2:**

```
Input: grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
Output: 3
```

> 解法

采用BFS的解法，首先遍历整个二维数组，当遇到一个`'1'`时，将小岛数加一，同时将所有邻近的`'1'`都标记为`'0'`，然后继续遍历直到遍历完整个二维数组

```java
class Solution {
    const int[] dx = {1, 0, -1, 0};
    const int[] dy = {0, 1, 0, -1};
    class Point {
        int x;
        int y;
        
        public Point(int a, int b) {
            x = a;
            y = b;
        }
    }
    
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) return 0;
        int n = grid.length;
        int m = grid[0].length;
        int island = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == '1') {
                    island++;
                    bfs(grid, i, j);
                }
            }
        }
        return island;
    }
    
    public void bfs(char[][] grid, int i, int j) {
        grid[i][j] = '0';
        Queue<Point> queue = new LinkedList<>();
        queue.offer(new Point(i, j));
        while (!queue.isEmpty()) {
            Point p = queue.poll();
            for (int k = 0 ; k < 4; k++) {
                int x = dx[k] + p.x;
                int y = dy[k] + p.y;
                if (x >= 0 && x < grid.length && y >= 0 && y < grid[0].length) {
                    if (grid[x][y] == '1') {
                        queue.offer(new Point(x, y));
        				// 必须要将字符改为'0'是为了避免同一个点重复加入到队列中，
                        // 如当(3,2),(4,3)都为'1'时，(4,2)就会被重复加入
                        grid[x][y] = '0';
                    }
                }
            }
        }
    }
}
```

#### Zombie in Matrix

Given a 2D grid, each cell is either a wall `2`, a zombie `1` or people `0` (the number zero, one, two). Zombies can turn the nearest people(up/down/left/right) into zombies every day, but can not through wall. How long will it take to turn all people into zombies? Return -1 if can not turn all people into zombies.

**Example 1:**

```
Input:
[[0,1,2,0,0],
 [1,0,0,2,1],
 [0,1,0,0,0]]
Output: 2
```

**Example 2:**

```
Input:
[[0,0,0],
 [0,0,0],
 [0,0,1]]
Output: 4
```

> 解法

需要多少天传染所有人可以理解为二维数组中的点到僵尸点的距离，采用BFS进行求解

```java
class Solution {
    class Point {
        int x;
        int y;
        
        public Point(int a, int b) {
            x = a;
            y = b;
        }
    }
    
    public int zombie (int[][] grid) {
        if (grid == null || grid.length == 0) return 0;
        int n = grid.length;
        int m = grid[0].length;
        Queue<Point> queue = new LinkedList<>();
        Set<Point> set = new HashSet<>();
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == '1') {
                    Point p = new Point(i, j);
                    queue.offer(p);
                    set.add(p);
                }
            }
        }
        dfs(queue, grid, set);
    }

    public int dfs(Queue<Point> queue, int[][] grid, Set<Point> set) {
    	int[] dx = {1, 0, -1, 0};
        int[] dy = {0, 1, 0, -1};
        int res = 0;
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                Point p = queue.poll();
                for (int j = 0; j < 4; j++) {
                    if (x < 0 || x > grid.length || y < 0 || y > grid[0].length) {
                        continue;
                    }
                    int x = dx[j] + p.x;
                    int y = dy[j] + p.y;
                    Point curr = new Point(x, y);
                    if (!set.contains(curr) && grid[x][y] == '0') {
                        set.add(curr);
                        queue.offer(curr);
                    }
                }
            }
            res++;
        }
        return res;
    }
}
```

> 前面的写法采用了set，用来记录遍历的位置，为了避免重复遍历。也可以采用及时将被感染的人标记为僵尸的方法来避免重复

#### Knight Shortest Path

Given a knight in a chessboard (a binary matrix with `0` as empty and `1` as barrier) with a source position, find the shortest path to a destination position, return the length of the route. Return -1 if destination cannot be reached.

Source and destination must be empty. Knight can not enter the barrier. Path length refers to the number of steps the knight takes.

Clarification
If the knight is at (x, y), he can get to the following positions in one step:
(x + 1, y + 2)
(x + 1, y - 2)
(x - 1, y + 2)
(x - 1, y - 2)
(x + 2, y + 1)
(x + 2, y - 1)
(x - 2, y + 1)
(x - 2, y - 1)

**Example 1:**

```
Input:
[[0,0,0],
 [0,0,0],
 [0,0,0]]
source = [2, 0] destination = [2, 2] 
Output: 2
Explanation:
[2,0]->[0,1]->[2,2]
```

**Example 2:**

```
Input:
[[0,1,0],
 [0,0,1],
 [0,0,0]]
source = [2, 0] destination = [2, 2] 
Output:-1
```

> 解法

采用BFS

```java
/*
class Point {
    public int x;
    public int y;
    
    public Point() {
        this.x = 0;
        this.y = 0;
    }
    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
*/

class Solution {
    public int shortestPath(boolean[][] grid, Point source, Point destination) {
        if (grid == null) return 0;
        if (source.x == destination.x && source.y == destination.y) return 0;
        if (!isValid(source, grid) || !isValid(destination, grid)) return 0;
        
        Queue<Point> fifo = new LinkedList<>();
        Set<Point> visited = new HashSet<>();
        fifo.offer(source);
        visited.add(source);
        int[] dx = {1, 1, 2, 2, -1, -1, -2, -2};
        int[] dy = {2, -1, 1, -1, 2, -2, 1, -1};
        int step = 0;
        while (!fifo.isEmpty()) {
            int size = fifo.size(); 
            step++;
            for (int i = 0; i < size; i++) {
                Point cur = fifo.poll();
                for (int j = 0; j < 8; j++) {
                    Point neighbor = new Point(cur.x + dx[j], cur.y + dy[j]);
                    if (!isValid(neighbor, grid)) {
                        continue;
                    }
                    if (neighbor.x == destination.x && neighbor.y == destination.y) {
                        return step;
                    }
                    if (!visited.contains(neighbor)) {
                        fifo.offer(neighbor);
                    }
                }      
            }
        }
        return -1;
    }
    
    public boolean isValid(Point point, boolean[][] grid) {
        // a point is valid if it is within the bound and it is empty
        int m = grid.length;
        int n = grid[0].length;
        if (point.x < 0 || point.x >= m || point.y < 0 || point.y >= n) return false;
        return !grid[point.x][point.y];
    }
}
```

#### Build Post Office

Given a 2D grid, each cell is either an house `1` or empty `0` (the number zero, one), find the place to build a post office, the distance that post office to all the house sum is smallest. Return the smallest distance. Return -1 if it is not possible.

**Example 1:**

```
Input：[[0,1,0,0],[1,0,1,1],[0,1,0,0]]
Output： 6
Explanation：
Placing a post office at (1,1), the distance that post office to all the house sum is smallest.
```

**Example 2:**

```
Input：[[0,1,0],[1,0,1],[0,1,0]]
Output： 4
Explanation：
Placing a post office at (1,1), the distance that post office to all the house sum is smallest.
```

> 解法

首先从每个`House`点出发，计算`House`到每个空地的距离，也即空地到`House`点的距离，然后将其维护到一个全局二维数组上面，每次对一个`House`点进行`BFS`时都将得到的数值加到上面，最终就得到了每一个空地到`House`的距离总和

```java
class Coordinate {
    int x, y;
    public Coordinate(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

public class Solution {
    public int EMPTY = 0, HOUSE = 1, WALL = 2;
    public int[][] grid;
    public int n, m;
    public int[] deltaX = {0, 1, -1, 0};
    public int[] deltaY = {1, 0, 0, -1};
    
    private List<Coordinate> getCoordinates(int type) {
        List<Coordinate> coordinates = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == type) {
                    coordinates.add(new Coordinate(i, j));
                }
            }
        }
        return coordinates;
    }
    
    private boolean inBound(Coordinate coor) {
        if (coor.x < 0 || coor.x >= n || coor.y < 0 || coor.y >= m) {
            return false;
        }
        return grid[coor.x][coor.y] == EMPTY;
    }

    public int shortestDistance(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return -1;
        }
        n = grid.length;
        m = grid[0].length;
        this.grid = grid;
        
        List<Coordinate> houses = getCoordinates(HOUSE);
        int[][] distanceSum = new int[n][m];
        int[][] visitedTimes = new int[n][m]; // 用来记录一个空地被访问的次数
        for (Coordinate house : houses) {
            bfs(house, distanceSum, visitedTimes);
        }
        
        int shortest = Integer.MAX_VALUE;
        List<Coordinate> empties = getCoordinates(EMPTY);
        for (Coordinate empty : empties) {
            if (visitedTimes[empty.x][empty.y] != houses.size()) {
                continue; // 如果一个空地被访问的次数不等于house个数，说明这个空地不能到达所有house
            }
            shortest = Math.min(shortest, distanceSum[empty.x][empty.y]);
        }
        return shortest == Integer.MAX_VALUE ? -1 : shortest;
    }
    
    private void bfs(Coordinate start, int[][] distanceSum, int[][] visitedTimes) {
        Queue<Coordinate> queue = new LinkedList<>();
        boolean[][] hash = new boolean[n][m];
        queue.offer(start);
        hash[start.x][start.y] = true;
        int steps = 0;
        
        while (!queue.isEmpty()) {
            steps++;
            int size = queue.size();
            for (int temp = 0; temp < size; temp++) {
                Coordinate coor = queue.poll();
                for (int i = 0; i < 4; i++) {
                    Coordinate adj = new Coordinate(
                        coor.x + deltaX[i],
                        coor.y + deltaY[i]
                    );
                    if (!inBound(adj) || hash[adj.x][adj.y]) {
                        continue;
                    }
                    queue.offer(adj);
                    hash[adj.x][adj.y] = true;
                    distanceSum[adj.x][adj.y] += steps; // 叠加计算空地到多个house的距离总和
                    visitedTimes[adj.x][adj.y]++;
                }
            }
        }
    }
}
```

> 上面的解法是从`House`出发计算，`House`到每一个空地的距离，最后把所有`House`到空地的距离求和。还有一种方法就是，从空地出发计算，空地到每一个点的距离（包括House），然后求和得到这个点到所有`House`的距离总和。

### 其他BFS

#### 127. Word Ladder

A **transformation sequence** from word `beginWord` to word `endWord` using a dictionary `wordList` is a sequence of words `beginWord -> s1 -> s2 -> ... -> sk` such that:

- Every adjacent pair of words differs by a single letter.
- Every `si` for `1 <= i <= k` is in `wordList`. Note that `beginWord` does not need to be in `wordList`.
- `sk == endWord`

Given two words, `beginWord` and `endWord`, and a dictionary `wordList`, return *the **number of words** in the **shortest transformation sequence** from* `beginWord` *to* `endWord`*, or* `0` *if no such sequence exists.*

**Example 1:**

```
Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]
Output: 5
Explanation: One shortest transformation sequence is "hit" -> "hot" -> "dot" -> "dog" -> cog", which is 5 words long.
```

**Example 2:**

```
Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log"]
Output: 0
Explanation: The endWord "cog" is not in wordList, therefore there is no valid transformation sequence.
```

> 解法

这个题目要求解经过多少步可以从`beginWord`到`endWord`，采用`BFS`求解，每一个字符都可以进行变换，然后判断变化后的字符串是否在`word`中

```java
public int ladderLength(String beginWord, String endWord, List<String> wordList) {
    if (!wordList.contains(endWord)) return 0;
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
                if (nextWord.equals(endWord)) return len;
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
            if (word.charAt(i) == j) continue;
            String next = setChar(word, i, j);
            if (dict.contains(next)) nextWords.add(next);
        }
    }
    return nextWords;
}

public String setChar(String word, int i, char ch) {
    char[] chars = word.toCharArray();
    chars[i] = ch;
    return new String(chars);
}

```





### DFS Combination组合

#### 78. Subsets

Given an integer array `nums` of **unique** elements, return *all possible subsets (the power set)*.

The solution set **must not** contain duplicate subsets. Return the solution in **any order**.

**Example 1:**

```
Input: nums = [1,2,3]
Output: [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

**Example 2:**

```
Input: nums = [0]
Output: [[],[0]]
```

> 解法

典型的组合问题，采用DFS或者回溯法的写法

```java
public List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> res = new ArrayList<>();
    if (nums == null || nums.length == 0) return res;
    List<Integer> curr = new ArrayList<>();
    backtrack(nums, res, curr, 0);
    return res;
}
// 当前list为curr，当前数组位置为idx，两种选择：list中加入或不加入nums[idx]
public void backtrack(int[] nums, List<List<Integer>> res, List<Integer> curr, int idx) {
    if (idx == nums.length) {
        res.add(new ArrayList<Integer>(curr));
        return;
    }
    curr.add(nums[idx]);
    backtrack(nums, res, curr, idx + 1);
    curr.remove(curr.size() - 1);
    backtrack(nums, res, curr, idx + 1);
}
```

> 下面提供另一种写法，backtrack函数的定义略有不同

```java
public List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> res = new ArrayList<>();
    if (nums == null || nums.length == 0) return res;
    List<Integer> curr = new ArrayList<>();
    backtrack(nums, res, curr, 0);
    return res;
}
// 当前list为curr，从数组位置为idx开始，所能得到的subset
public void backtrack(int[] nums, List<List<Integer>> res, List<Integer> curr, int idx) {
    res.add(new ArrayList<Integer>(curr));
    for (int i = idx; i < nums.length; i++) {
        curr.add(nums[i]);
        backtrack(nums, res, curr, i + 1);
        curr.remove(curr.size() - 1);
    }
}
```

#### 39. Combination Sum

Given an array of **distinct** integers `candidates` and a target integer `target`, return *a list of all **unique combinations** of* `candidates` *where the chosen numbers sum to* `target`*.* You may return the combinations in **any order**.

The **same** number may be chosen from `candidates` an **unlimited number of times**. Two combinations are unique if the frequency of at least one of the chosen numbers is different.

It is **guaranteed** that the number of unique combinations that sum up to `target` is less than `150` combinations for the given input.

**Example 1:**

```
Input: candidates = [2,3,6,7], target = 7
Output: [[2,2,3],[7]]
Explanation:
2 and 3 are candidates, and 2 + 2 + 3 = 7. Note that 2 can be used multiple times.
7 is a candidate, and 7 = 7.
These are the only two combinations.
```

**Example 2:**

```
Input: candidates = [2,3,5], target = 8
Output: [[2,2,2,2],[2,3,3],[3,5]]
```

> 解法

和前面的题目一样，唯一的区别在于一个数可以使用次数不限

```java
public List<List<Integer>> combinationSum(int[] candidates, int target) {
    List<List<Integer>> res = new ArrayList<>();
    if (candidates == null || candidates.length == 0) return res;
    List<Integer> curr = new ArrayList<>();
    backtrack(candidates, res, curr, 0, 0, target);
    return res;
}

public void backtrack(int[] nums, List<List<Integer>> res, List<Integer> curr, int sum, int idx, int target) {
    if (sum == target) {
        res.add(new ArrayList<Integer>(curr));
    }
    if (sum > target) return;
    for (int i = idx; i < nums.length; i++) {
        curr.add(nums[i]);
        backtrack(nums, res, curr, sum + nums[i], i, target);
        curr.remove(curr.size() - 1);
    }
}
```

#### 40. Combination Sum II

Given a collection of candidate numbers (`candidates`) and a target number (`target`), find all unique combinations in `candidates` where the candidate numbers sum to `target`.

Each number in `candidates` may only be used **once** in the combination.

**Note:** The solution set must not contain duplicate combinations.

**Example 1:**

```
Input: candidates = [10,1,2,7,6,1,5], target = 8
Output: 
[
[1,1,6],
[1,2,5],
[1,7],
[2,6]
]
```

**Example 2:**

```
Input: candidates = [2,5,2,1,2], target = 5
Output: 
[
[1,2,2],
[5]
]
```

> 解法

和前面一题思路一样，有两点区别：1 数组中可能含有重复的数；2 一个数只可以使用一次

```java
public List<List<Integer>> combinationSum2(int[] candidates, int target) {
    List<List<Integer>> res = new ArrayList<>();
    if (candidates == null || candidates.length == 0) return res;
    List<Integer> curr = new ArrayList<>();
    Arrays.sort(candidates);
    backtrack(candidates, res, curr, 0, 0, target);
    return res;
}

public void backtrack(int[] nums, List<List<Integer>> res, List<Integer> curr, int sum, int idx, int target) {
    if (sum == target) {
        res.add(new ArrayList<Integer>(curr));
    }
    if (sum > target) return;
    for (int i = idx; i < nums.length; i++) {
        if (i != idx && nums[i] == nums[i-1]) {
            continue;
        }
        curr.add(nums[i]);
        backtrack(nums, res, curr, sum + nums[i], i+1, target);
        curr.remove(curr.size() - 1);
    }
}
```

#### 131. Palindrome Partitioning

Given a string `s`, partition `s` such that every substring of the partition is a **palindrome**. Return all possible palindrome partitioning of `s`.

A **palindrome** string is a string that reads the same backward as forward.

**Example 1:**

```
Input: s = "aab"
Output: [["a","a","b"],["aa","b"]]
```

**Example 2:**

```
Input: s = "a"
Output: [["a"]]
```

> 解法

这道题目实际上需要用到两种算法相结合，首先用DP求出任意的子字符串是否为回文串，然后采用DFS(backtrack)计算所有可行解

```java
public List<List<String>> partition(String s) {
    List<List<String>> res = new ArrayList<>();
    if (s == null || s.length() == 0) return res;
    // DP，dp[i][j]子字符串(i,j)，是否为回文串
    boolean[][] dp = new boolean[s.length()][s.length()];
    for (int i = 0; i < dp.length; i++) {
        dp[i][i] = true;
        if (i + 1 < dp.length) {
            dp[i][i+1] = (s.charAt(i) == s.charAt(i+1));
        }
    }
    for (int d = 2; d < dp.length; d++) {
        for (int i = 0; i + d < dp.length; i++) {
            dp[i][i + d] = (dp[i+1][i + d - 1] && s.charAt(i) == s.charAt(i + d));
        }
    }
    List<String> curr = new ArrayList<>();
    backtrack(res, curr, 0, s, dp);
    return res;
}

// 可行解
public void backtrack(List<List<String>> res, List<String> curr, int idx, String s, boolean[][] dp) {
    if (idx == s.length()) {
        res.add(new ArrayList<String> (curr));
    }
    for (int i = idx; i < s.length(); i++) {
        if (!dp[idx][i]) {
            continue;
        }
        curr.add(s.substring(idx, i+1));
        backtrack(res, curr, i + 1, s, dp);
        curr.remove(curr.size() - 1);
    }
}
```

### DFS Permutation排列

#### 46. Permutations

Given an array `nums` of distinct integers, return *all the possible permutations*. You can return the answer in **any order**.

**Example 1:**

```
Input: nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

**Example 2:**

```
Input: nums = [0,1]
Output: [[0,1],[1,0]]
```

> 解法

可以把题目的组合看做向一个空集里面不断添加元素，直到长度和`nums`相同，同时添加的元素不可以重复

```java
public List<List<Integer>> permute(int[] nums) {
    List<List<Integer>> result = new ArrayList<>();
    if (nums == null || nums.length == 0) return result;
    ArrayList<Integer> permu = new ArrayList<>();
    backtrack(result, nums, permu);
    return result;
}
    
public void backtrack(List<List<Integer>> result, int[] nums, ArrayList<Integer> permu) {
    if (permu.size() == nums.length) {
        result.add(new ArrayList<Integer>(permu));
        return;
    }
    for (int i = 0; i < nums.length; i++) {
        if (permu.contains(nums[i])) continue;
        permu.add(nums[i]);
        helper(result, nums, permu);
        permu.remove(permu.size() - 1);
    }
}
```

#### 47. Permutations II

Given a collection of numbers, `nums`, that might contain duplicates, return *all possible unique permutations **in any order**.*

**Example 1:**

```
Input: nums = [1,1,2]
Output:
[[1,1,2],
 [1,2,1],
 [2,1,1]]
```

**Example 2:**

```
Input: nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

> 解法

题目和上面一题的区别在于`nums`可能包含重复的数字

```java
public List<List<Integer>> permuteUnique(int[] nums) {
    List<List<Integer>> result = new ArrayList<>();
    if (nums == null || nums.length == 0) return result;
    ArrayList<Integer> permu = new ArrayList<>();
    Arrays.sort(nums);
    Set<Integer> set = new HashSet<>();
    backtrack(result, nums, permu, set);
    return result;
}
    
public void backtrack(List<List<Integer>> result, int[] nums, ArrayList<Integer> permu, Set<Integer> set) {
    if (permu.size() == nums.length) {
        result.add(new ArrayList<Integer>(permu));
        return;
    }
    for (int i = 0; i < nums.length; i++) {
        if (set.contains(i)) continue;
        if ( i != 0 && nums[i] == nums[i-1] && !set.contains(i-1)) continue;
        permu.add(nums[i]);
        set.add(i);
        backtrack(result, nums, permu, set);
        permu.remove(permu.size() - 1);
        set.remove(i);
    }
}
```

#### 51. N-Queens

The **n-queens** puzzle is the problem of placing `n` queens on an `n x n` chessboard such that no two queens attack each other.

Given an integer `n`, return *all distinct solutions to the **n-queens puzzle***. You may return the answer in **any order**.

Each solution contains a distinct board configuration of the n-queens' placement, where `'Q'` and `'.'` both indicate a queen and an empty space, respectively.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/11/13/queens.jpg)

```
Input: n = 4
Output: [[".Q..","...Q","Q...","..Q."],["..Q.","Q...","...Q",".Q.."]]
Explanation: There exist two distinct solutions to the 4-queens puzzle as shown above
```

> 解法

将棋盘的排布记录到一个长度为`n`的数组，其中`n[i]`代表第`i`行棋盘的位置。

```java
public List<List<String>> solveNQueens(int n) {
    if (n <= 0) return new ArrayList<>();
    List<List<Integer>> res = new ArrayList<>();
    List<String> curr = new ArrayList<>();
    boolean[] added = new boolean[n];
    backtrack(res, curr, added, n);
    return transfer(res);
}

public void backtrack(List<List<Integer>> res, List<String> curr, boolean[] added, int n) {
    if (curr.size() == n) {
        res.add(new ArrayList<Integer>(curr));
        return;
    }
    for (int i = 0; i < n; i++) {
        if (added[i]) continue;
        if (!isValid(curr)) continue;
        curr.add(i);
        added[i] = true;
        backtrack(res, curr, added, n);
        curr.remove(curr.size() - 1);
        added[i] = false;
    }
}
// 判断指定当前数组是否满足要求
public boolean isValid(List<String> curr) {
    for (int i = 0; i < curr.size(); i++) {
        for (int j = i; j < curr.size(); j++) {
            if (j == i) continue;
            if (Math.abs(curr.get(i) - curr.get(j)) == Math.abs(i - j)) return false;
        }
    }
    return true;
}

public List<List<String>> transfer(List<List<Integer>> res) {
    List<List<String>> result = new ArrayList<>();
    for (int i = 0; i < result.size(); i++) {
        ArrayList<String> temp = new ArrayList<>();
        List<Integer> nums = res.get(i);
        for (int j = 0; j < nums.size(); j++) {
            StringBuffer str = new StringBuffer();
            for (int k = 0; i k < nums.size(); k++) {
                if (nums.get(j) == k) {
                    str.append("Q");
                } else {
                    str.append(".");
                }
            }
            temp.add(str.toString());
        }
        result.add(temp);
    }
    return res;
}
```

> 当前解法是在过程中判断当前棋盘放置是否可行，还有一种写法是在最后向`res`中添加结果时判断。两个写法哪种更优无法确定。

#### 113. Path Sum II

Given the `root` of a binary tree and an integer `targetSum`, return *all **root-to-leaf** paths where the sum of the node values in the path equals* `targetSum`*. Each path should be returned as a list of the node **values**, not node references*.

A **root-to-leaf** path is a path starting from the root and ending at any leaf node. A **leaf** is a node with no children.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/01/18/pathsumii1.jpg" alt="img" style="zoom:50%;" />

```
Input: root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
Output: [[5,4,11,2],[5,8,4,5]]
Explanation: There are two paths whose sum equals targetSum:
5 + 4 + 11 + 2 = 22
5 + 8 + 4 + 5 = 22
```

> 解法

由于需要得到所有可行解，明显要采用回溯法(DFS)

```java
public List<List<Integer>> pathSum(TreeNode root, int target) {
    List<List<Integer>> result = new ArrayList<>();
    if (root == null) return result;
    List<Integer> path = new ArrayList<>();
    path.add(root.val);
    backtrack(root, target, path, result, root.val);
    return result;
}

public void backtrack(TreeNode root, int target, List<Integer> path, List<List<Integer>> result, int currSum) {
    if (currSum > target) return;
    if (root.left == null && root.right == null) {
        if (currSum == target) {
            result.add(new ArrayList<Integer>(path));
        }
        return;
    }
    if (root.left != null) {
        path.add(root.left.val);
        backtrack(root.left, target, path, result, currSum + root.left.val);
        path.remove(path.size - 1);
    }
    if (root.right != null) {
        path.add(root.right.val);
        backtrack(root.right, target, path, result, currSum + root.right.val);
        path.remove(path.size - 1);
    }
}
```

