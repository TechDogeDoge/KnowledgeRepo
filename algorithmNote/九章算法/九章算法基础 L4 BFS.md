#  九章算法基础 L4 BFS
[TOC]



##  什么时候使用BFS

1. 图的遍历 traversal in graph
1.1 层级遍历 level order traversal
1.2 由点及面 connected component
1.3 拓扑排序 topological sorting
2. 最短路径 shortest path in simple graph
2.1 仅限简单图求最短路径，即，途中每条边长度都是 1，且没有方向

最短路径问题可以用BFS或者DP解决
最长路径问题可以用DFS或者DP解决

BFS还有一个东西很重要就是是否需要分层遍历，如果需要分层遍历，需要添加一行代码同时添加一个 for loop。比如说例题 Zombie in Matrix， 例题 Knight shortest path

##  补充知识
BFS采用 FIFO Queue 实现，DFS采用 Stack 实现。
在JAVA中，Queue 是一个接口，具体实现可以采用LinkedList.
LinkedList API
remove(), Item, Retrieves and removes the head (first element) of this list.
size(), int, Returns the number of elements in this list
isEmpty(), boolean, Returns whether the list is empty
==offer(Item), boolean, Adds the specified element as the tail (last element) of this list.==
==poll(), Item, Retrieves and removes the head (first element) of this list.==
利用LinkedList 实现 FIFO Queue 可以使用 offer(), poll()组合，从最后面加入元素，从最前面取出元素；或者使用 add(), add 和 offer() 是一样的。
add(Item), boolean, Appends the specified element to the end of this list.
add(index, Item), void, Inserts the specified element at the specified position in this list.


#  1 BFS in binary tree
##  例题 102. Binary Tree Level Order Traversal
Given a binary tree, return the level order traversal of its nodes values. (ie, from left to right, level by level).

For example:
Given binary tree [3,9,20,null,null,15,7],
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123000032980.png)
return its level order traversal as:
[
 &nbsp; [3],
 &nbsp; [9,20],
 &nbsp; [15,7]
]

### solution

采用FIFO Queue实现BFS，需要注意的是在每一次确定每一层的大小时，int size = fifo.size();，必须放在 for 循环的外面
for 循环确保了分层输出 List 结果，如果去掉 for 循环，其实得到的结果是一样的，但是不能明确分层

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();
        if (root == null) return result;
        // fifo queue
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
}
```

还可以采用 recursion 的解法
```java
class Solution {
    List<List<Integer>> levels = new ArrayList<List<Integer>>();

    public void helper(TreeNode node, int level) {
        // start the current level
        if (levels.size() == level)
            levels.add(new ArrayList<Integer>());

         // fulfil the current level
         levels.get(level).add(node.val);

         // process child nodes for the next level
         if (node.left != null)
            helper(node.left, level + 1);
         if (node.right != null)
            helper(node.right, level + 1);
    }
    
    public List<List<Integer>> levelOrder(TreeNode root) {
        if (root == null) return levels;
        helper(root, 0);
        return levels;
    }
}
```

## 例题 297. Serialize and Deserialize Binary Tree
> 序列化就是指将内存中结构化的数据变成字符串的过程，反之，反序列化就是把字符串变成结构化数据的过程

Serialization is the process of converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted across a network connection link to be reconstructed later in the same or another computer environment.

Design an algorithm to serialize and deserialize a binary tree. There is no restriction on how your serialization/deserialization algorithm should work. You just need to ensure that a binary tree can be serialized to a string and this string can be deserialized to the original tree structure.

Example: 
You may serialize the following tree:

![img](https://img-blog.csdnimg.cn/20200410100640826.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)

as "[1,2,3,null,null,4,5]"

Clarification: The above format is the same as how LeetCode serializes a binary tree. You do not necessarily need to follow this format, so please be creative and come up with different approaches yourself.

Note: Do not use class member/global/static variables to store states. Your serialize and deserialize algorithms should be stateless.

### Solution

序列化和反序列化都采用BFS层序遍历
题目所给的二叉树会被序列化为 1,2,3,#,#,4,5,#,#,#,#,"

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

// Your Codec object will be instantiated and called as such:
// Codec ser = new Codec();
// Codec deser = new Codec();
// TreeNode ans = deser.deserialize(ser.serialize(root));
```


## 449. Serialize and Deserialize BST
Serialization is the process of converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted across a network connection link to be reconstructed later in the same or another computer environment.

Design an algorithm to serialize and deserialize a binary search tree. There is no restriction on how your serialization/deserialization algorithm should work. You just need to ensure that a binary search tree can be serialized to a string and this string can be deserialized to the original tree structure.

The encoded string should be as compact as possible.

Note: Do not use class member/global/static variables to store states. Your serialize and deserialize algorithms should be stateless.

### Solution

#  2 BFS in graph
> 和树的区别是：
> 路径上存在区别，tree 中总是从 root 到 leaf，但是 graph 中不存在 parent-child 关系。所以在遍历 graph 的时候需要存储一下当前已经 visit 的节点。

图中有 N 个点 M 条边
时间复杂度
O(M + N)，其中 M 最大为 O(N<sup>2</sup>)，所以最坏情况下的世家复杂度为O(N<sup>2</sup>)


##  例题 261. Graph Valid Tree
Given n nodes labeled from 0 to n-1 and a list of undirected edges (each edge is a pair of nodes), write a function to check whether these edges make up a valid tree.

Example 1:
Input: n = 5, and edges = [[0,1], [0,2], [0,3], [1,4]]
Output: true

Example 2:
Input: n = 5, and edges = [[0,1], [1,2], [2,3], [1,3], [1,4]]
Output: false

Note: you can assume that no duplicate edges will appear in edges. Since all edges are undirected, [0,1] is the same as [1,0] and thus will not appear together in edges.

tree 的条件是有 n 个节点， n-1 个边， 同时所有点连通

### solution

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
        // generate the graph using the edges info
        // Use HashMap to map neighbors to a node
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

##  例题 133. Clone Graph
Given a reference of a node in a connected undirected graph, return a deep copy (clone) of the graph. Each node in the graph contains a val (int) and a list (List[Node]) of its neighbors.
<img src="https://img-blog.csdnimg.cn/20200105044324293.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70" alt="graph clone" style="zoom: 67%;"/>

Note:

1. The number of nodes will be between 1 and 100.
2. The undirected graph is a simple graph, which means no repeated edges and no self-loops in the graph.
3. Since the graph is undirected, if node p has node q as neighbor, then node q must have node p as neighbor too.
4. You must return the copy of the given node as a reference to the cloned graph.

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
                Node newNeighbor = map.get(neighbor);
                newNode.neighbors.add(newNeighbor);
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
能用 BFS 解决的问题一定不要用 DFS。 


##  Topological Sorting
可以用 DFS 但是不建议，采用 BFS 最合适
Topological sorting 从名字上听起来像是排序问题，实际上是判断 graph 中是否存在 loop。

##  例题 topological-sorting
给定一个有向图，图节点的拓扑排序被定义为：
1. 对于每条有向边A--> B，则A必须排在B之前　　
2. 拓扑排序的第一个节点可以是任何在图中没有其他节点指向它的节点　　

找到给定图的任一拓扑排序
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200105113318547.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)

```java
class Solution {
    public ArrayList<Node> topoSort(ArrayList<Node> graph) {
        ArrayList<Node> order = new ArrayList<>();
        
        if (graph == null) {
            return order;
        }
        
        // calculate the indegree of each node
        HashMap<Node, Integer> inDegree = getIndegree(graph);
        
        // get the starting nodes that have 0 indegree
        ArrayList<Node> startNodes = getStartNodes(inDegree, graph);
        
        // use bfs to find the topological order
        order = bfs(inDegree, startNodes);
        
        // check out whether the length of topological equals to the number of nodes in the graph
        // if not, there is loop in the graph
        if (order.size() == graph.size()) {
            return order;
        }
        return null;
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


#  3 BFS in 2D grid (matrix)
2D grid (matrix) 是一种特殊的 graph， 每一个格子就相当于 graph 中的一个点。连通情况有两种： 
1 四连通：上下左右; 2 八连通：额外包括左上左下右上右下 

2D grid (matrix) 有 R 个行，C 个列，共有 $R*C$ 个点，$R*C*2$ 条边
2D grid (matrix) 的 BFS 时间复杂度为 $O(R*C + R*C*2) = O(R*C)$

##  例题 200. Number of Islands
Given a 2d grid map of '1's (land) and '0's (water), count the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

Example 1:
Input:
11110
11010
11000
00000
Output: 1

Example 2:
Input:
11000
11000
00100
00011
Output: 3

## Solution
采用 BFS 来进行搜索，每当发现一个值为 ‘1’ 的点，找到它所有相邻值为 ‘1’ 的点并将值修改为 '0'，同时将 island 个数加一。
需要注意的是将值改为 ‘0’ 的时间与将这个加入到 FIFO queue  的时间关系，应该先把值改为 ‘0’，然后再加入 FIFO queue，否则一个点可以两次被加入到FIFO queue中，导致count被多减了一次，计算出现问题。
以下面的为例
[ 1 1 1 1,
  1 1 0 0,
  0 0 1 0,
  0 0 0 1] 
位于[1, 1]的点被两次加入，作为[0,1]和[1,0]的邻接点被加入。
```java
class Solution {
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return 0;
        }
        
        int n = grid.length;
        int m = grid[0].length;
        int island  = 0;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == '1') {
                    markBFS(grid, i ,j);
                    island++;
                }
            }
        }
        return island;
    }
    
    public void markBFS(char[][] grid, int i, int j) {
        Queue<point> fifo = new LinkedList<>();
        fifo.offer(new point(i,j));
        grid[i][j] = '0';
        int[] dx = {0, 0, 1, -1};
        int[] dy = {1, -1, 0, 0};
        
        while (!fifo.isEmpty()) {
            point cur = fifo.poll();
            for (int k = 0; k < 4; k++) {
                point neighbor = new point(cur.x + dx[k], cur.y + dy[k]);
                if (!inBound(grid, neighbor)) {
                    continue;
                }
                if (grid[neighbor.x][neighbor.y] == '1') {
                    // change the value to '0', then add it to the FIFO queue
                    grid[neighbor.x][neighbor.y] = '0';
                    fifo.offer(neighbor);
                }
            }
        }
    }
    
    public boolean inBound(char[][] grid, point pt) {
        int n = grid.length;
        int m = grid[0].length;
        if (pt.x < 0 || pt.x >= n) {
            return false;
        }
        
        if (pt.y < 0 || pt.y >= m) {
            return false;
        }
        return true;
    }
    
    class point {
        int x;
        int y;
        
        public point(int x, int y) {
            this.x = x;
            this.y = y;
        }
    }
}
```

## 2D grid (matrix) 分层遍历
## 例题 Zombie in Matrix
Given a 2D grid, each cell is either a wall 2, a zombie 1 or people 0 (the number zero, one, two).Zombies can turn the nearest people(up/down/left/right) into zombies every day, but can not through wall. How long will it take to turn all people into zombies? Return -1 if can not turn all people into zombies.

Example 1:

Input:
[[0,1,2,0,0],
 [1,0,0,2,1],
 [0,1,0,0,0]]
Output: 2

Example 2:
Input:
[[0,0,0],
 [0,0,0],
 [0,0,1]]
Output: 4

```java
class Solution {
    public int PEOPLE = 0;
    public int ZOMBIE = 1;
    public int WALL = 2;
    
    public int zombie (int[][] grid) {
        if (grid == null || grid.length == 0) {
            return 0;
        }
        
        int result = 0;
        int n = grid.length;
        int m = grid[0].length;
        
        int people = 0;
        Queue<cell> fifo = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == PEOPLE) {
                    prople++;
                } else if (grid[i][j] == ZOMBIE) {
                    fifo.offer(new cell(i,j));
                }
            }
        }
        
        if (prople == 0) {
            return 0;
        }
        
        int[] dx = {0, 0, 1, -1};
        int[] dy = {1, -1, 0, 0};
        while (!fifo.isEmpty()) {
            int size= fifo.size();
            for (int i = 0; i < size; i++) {
                cell cur = fifo.poll();
                for (int j = 0; j < 4; j++) {
                    cell neighbor = new cell(cur.x + dx[j], cur.y + dy[j]);
                    if (inBound(neighbor, grid)) {
                        if (grid[neighbor.x][neighbor.y] == PEOPLE) {
                            grid[neighbor.x][neighbor.y] = ZOMBIE;
                            people--;
                            fifo.offer(neighbor);
                        }
                    }
                }
            }
            result++;
            if (people == 0) {
                return result;
            }
        }
        return -1
        
    }
    
    public boolean inBound(cell cut, int[][] grid) {
        int n = grid.length;
        int m = grid[0].length;
        if (cur.x < 0 || cur.x >= n) {
            return false;
        }
        if (cur.y < 0 || cur.y >= m) {
            return false;
        }
        return true;
    }
    
    class cell {
        public int x;
        public int y;
        
        public cell(int x, int y) {
            this.x = x;
            this.y = y;
        }
    }
}
```

##  例题 Knight Shortest Path
Given a knight in a chessboard (a binary matrix with 0 as empty and 1 as barrier) with a source position, find the shortest path to a destination position, return the length of the route. Return -1 if destination cannot be reached.

Source and destination must be empty.
Knight can not enter the barrier.
Path length refers to the number of steps the knight takes.

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

Example 1:
Input:
[[0,0,0],
 [0,0,0],
 [0,0,0]]
source = [2, 0] destination = [2, 2] 
Output: 2
Explanation:
[2,0]->[0,1]->[2,2]

Example 2:
Input:
[[0,1,0],
 [0,0,1],
 [0,0,0]]
source = [2, 0] destination = [2, 2] 
Output:-1

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
        if (grid == null) {
            return 0;
        }
        if (source.x == destination.x && source.y == destination.y) {
            return 0;
        }
        if (!isValid(source, grid) || !isValid(destination, grid)) {
            return 0;
        }
        
        Queue<Point> fifo = new LinkedList<>();
        fifo.offer(source);
        
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
                    if (!fifo.contains(neighbor)) {
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
        
        if (point.x < 0 || point.x >= m) {
            return false;
        }
        
        if (point.y < 0 || point.y >= n) {
            return false;
        }
        return !grid[point.x][point.y];
        
    }
}
```

## 例题 Build Post Office
Given a 2D grid, each cell is either an house 1 or empty 0 (the number zero, one), find the place to build a post office, the distance that post office to all the house sum is smallest. Return the smallest distance. Return -1 if it is not possible.

Example 1:
Input：[[0,1,0,0],[1,0,1,1],[0,1,0,0]]
Output： 6
Explanation：
Placing a post office at (1,1), the distance that post office to all the house sum is smallest.

Example 2:
Input：[[0,1,0],[1,0,1],[0,1,0]]
Output： 4
Explanation：
Placing a post office at (1,1), the distance that post office to all the house sum is smallest.

## Solution
方法一：

由于题中所求两点间距离表示为d(i，j)=|xi-xj|+|yi-yj|，所以可以在x方向和y方向分别计算，然后求和即可，实现一维方向的计算即可。
首先遍历网格，为1的方格，将其横纵坐标，即i和j分别存储，然后排序，方便接下来的查找计算。然后利用sumx和sumy前缀和，sumx代表起点到各个点的x方向距离和，sumy同理。
再次遍历网格，当前处为0时，首先二分查找该方向i或j的位置，如果有多个相同的，寻找最右边的，即可，设为index。
然后求和即可
sum.get(n) - sum.get(index + 1) - pos * (n - index - 1) + (index + 1) * pos - sum.get(index + 1);
sum.get(n) - sum.get(index + 1)计算出当前点右侧部分到起点的距离和
pos * (n - index - 1) 减去多余部分，因为之前是到起点的距离和，现在要求到当前点的距离和
(index + 1) * pos - sum.get(index + 1) 此处开始计算当前点左侧部分，index+1个当前点距离和减去index+1到起点的距离和，即为左侧部分的距离和。

方法二：
处理前缀：
prefixSum1记录数组 a 的前缀和，即:prefixSum1[i]=a[0]+a[1]+..+a[i].
prefixSum2记录数组 prefixSum1 前缀和，prefixSum2即为前 i 个点到第 i 个点的代价和。
处理后缀：
prefixSum1记录数组 a 的后缀和，即:prefixSum1[i]=a[n-1]+a[n-2]+..+a[i].
prefixSum2记录数组 prefixSum1 的后缀和，prefixSum2即为 i 之后的点到第 i 个点的代价和。
```java
// 方法一 
public class Solution {
    /**
     * @param grid a 2D grid
     * @return an integer
     */
    public int shortestDistance(int[][] grid) {
        // Write your code here
        int n = grid.length;
        if (n == 0)
            return -1;

        int m = grid[0].length;
        if (m == 0)
            return -1;

        List<Integer> sumx = new ArrayList<Integer>();
        List<Integer> sumy = new ArrayList<Integer>();
        List<Integer> x = new ArrayList<Integer>();
        List<Integer> y = new ArrayList<Integer>();

        int result = Integer.MAX_VALUE;
        for (int i = 0; i < n; ++i)
            for (int j = 0; j < m; ++j)
                if (grid[i][j] == 1) {
                    x.add(i);
                    y.add(j);
                }
        
        Collections.sort(x);
        Collections.sort(y);

        int total = x.size();

        sumx.add(0);
        sumy.add(0);
        for (int i = 1; i <= total; ++i) {
            sumx.add(sumx.get(i-1) + x.get(i-1));
            sumy.add(sumy.get(i-1) + y.get(i-1));
        }

        for (int i = 0; i < n; ++i)
            for (int j = 0; j < m; ++j)
                if (grid[i][j] == 0) {
                    int cost_x = get_cost(x, sumx, i, total);
                    int cost_y = get_cost(y, sumy, j, total);
                    if (cost_x + cost_y < result)
                        result = cost_x + cost_y;
                }

        return result;
    }

    public int get_cost(List<Integer> x, List<Integer> sum, int pos, int n) {
        if (n == 0)
            return 0;
        if (x.get(0) > pos)
            return sum.get(n) - pos * n;

        int l = 0, r = n - 1;
        while (l + 1 < r) {
            int mid = l + (r - l) / 2;
            if (x.get(mid) <= pos)
                l = mid;
            else
                r = mid - 1;
        }
        
        int index = 0;
        if (x.get(r) <= pos)
            index = r;
        else
            index = l;
        return sum.get(n) - sum.get(index + 1) - pos * (n - index - 1) + 
               (index + 1) * pos - sum.get(index + 1);
    }
}

// 方法二
public class Solution {
    /**
     * @param grid a 2D grid
     * @return an integer
     */
    public int shortestDistance(int[][] grid) {
        // Write your code here
        int row = grid.length, column = grid[0].length;
        if(row == 0 || column == 0 || !haveZero(grid,row,column)) {
            return -1;
        }

        int[] rowSum = new int[row];
        int[] columnSum = new int[column]; 
        for(int i = 0; i < row; i++)
        	for(int j = 0; j < column; j++)
        		if(grid[i][j] == 1) {
        			rowSum[i]++;
        			columnSum[j]++;
        		}

        int[] ansRow = new int[row];
        int[] ansColumn = new int[column];
        getSumDistance(rowSum,row,ansRow);
        getSumDistance(columnSum,column,ansColumn);

        int ans = Integer.MAX_VALUE;
        for(int i = 0; i < row; i++)
        	for(int j = 0; j < column; j++)
        		if(grid[i][j] == 0 && ans > ansRow[i] + ansColumn[j]) {
        			ans = ansRow[i] + ansColumn[j];
        		}
        return ans;
    }

    void getSumDistance(int[] a,int n,int[] ans) {
    	int[] prefixSum1 = new int[n];
    	int[] prefixSum2 = new int[n];
    	/*
    	第一阶段，处理前缀。
    	prefixSum1记录数组 a 的前缀和，即:prefixSum1[i]=a[0]+a[1]+..+a[i].
    	prefixSum2记录数组 prefixSum1 前缀和，prefixSum2即为前 i 个点到第 i 个点的代价和。
    	*/
    	prefixSum1[0] = a[0];
    	for(int i = 1; i < n; i++) {
    		prefixSum1[i] = prefixSum1[i - 1] + a[i];
    	}
    	prefixSum2[0] = 0;
    	for(int i = 1; i < n; i++) {
    		prefixSum2[i] = prefixSum2[i - 1] + prefixSum1[i - 1];
     	}

     	for(int i = 0; i < n; i++) {
     		ans[i] = prefixSum2[i];
     	}

    	/*
    	第二阶段，处理后缀。
    	prefixSum1记录数组 a 的后缀和，即:prefixSum1[i]=a[n-1]+a[n-2]+..+a[i].
    	prefixSum2记录数组 prefixSum1 的后缀和，prefixSum2即为 i 之后的点到第 i 个点的代价和。
    	*/
    	prefixSum1[n - 1] = a[n - 1];
    	for(int i = n - 2; i >= 0; i--) {
    		prefixSum1[i] = prefixSum1[i + 1] + a[i];
    	}
    	prefixSum2[n - 1] =0;
    	for(int i = n - 2; i >= 0; i--) {
    		prefixSum2[i] = prefixSum2[i + 1] + prefixSum1[i + 1];
     	}

     	for(int i = 0; i < n; i++) {
     		ans[i] += prefixSum2[i];
     	}

     	/*
     	ans[i] 即为a数组中所有点到第 i 点的代价和
     	*/
    }

    boolean haveZero(int[][] grid, int row, int column) {
    	for(int i = 0; i < row; i++) {
    		for(int j = 0; j < column; j++){
    			if(grid[i][j] == 0) {
    				return true;
    			}
    		}
    	}
    	return false;
    }
}
```
## 例题 Build Post Office
Given a 2D grid, each cell is either a wall 2, an house 1 or empty 0 (the number zero, one, two), find a place to build a post office so that the sum of the distance from the post office to all the houses is smallest.
Return the smallest sum of distance. Return -1 if it is not possible.

Example 1:
Input：[[0,1,0,0,0],[1,0,0,2,1],[0,1,0,0,0]]
Output：8
Explanation： Placing a post office at (1,1), the distance that post office to all the house sum is smallest.

Example 2:
Input：[[0,1,0],[1,0,1],[0,1,0]]
Output：4
Explanation： Placing a post office at (1,1), the distance that post office to all the house sum is smallest.
## Solution
本题采用bfs，首次遍历网格，对空地处进行bfs，搜索完成后如果存在房屋没有被vis标记则改空地不可以设置房屋。
朴素的bfs搜索过程中，sun+=dist;实现当前点距离和的更新。
now.dis+1每次实现当前两点间距离的更新。
```java
class Coordinate {
    int x, y;
    public Coordinate(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

public class Solution {
    public int EMPTY = 0;
    public int HOUSE = 1;
    public int WALL = 2;
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
    
    private void setGrid(int[][] grid) {
        n = grid.length;
        m = grid[0].length;
        this.grid = grid;
    }
    
    private boolean inBound(Coordinate coor) {
        if (coor.x < 0 || coor.x >= n) {
            return false;
        }
        if (coor.y < 0 || coor.y >= m) {
            return false;
        }
        return grid[coor.x][coor.y] == EMPTY;
    }

    /**
     * @param grid a 2D grid
     * @return an integer
     */
    public int shortestDistance(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return -1;
        }
        
        // set n, m, grid
        setGrid(grid);
        
        List<Coordinate> houses = getCoordinates(HOUSE);
        int[][] distanceSum = new int[n][m];
        int[][] visitedTimes = new int[n][m];
        for (Coordinate house : houses) {
            bfs(house, distanceSum, visitedTimes);
        }
        
        int shortest = Integer.MAX_VALUE;
        List<Coordinate> empties = getCoordinates(EMPTY);
        for (Coordinate empty : empties) {
            if (visitedTimes[empty.x][empty.y] != houses.size()) {
                continue;
            }
            
            shortest = Math.min(shortest, distanceSum[empty.x][empty.y]);
        }
        
        if (shortest == Integer.MAX_VALUE) {
            return -1;
        }
        return shortest;
    }
    
    private void bfs(Coordinate start,
                     int[][] distanceSum,
                     int[][] visitedTimes) {
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
                    if (!inBound(adj)) {
                        continue;
                    }
                    if (hash[adj.x][adj.y]) {
                        continue;
                    }
                    queue.offer(adj);
                    hash[adj.x][adj.y] = true;
                    distanceSum[adj.x][adj.y] += steps;
                    visitedTimes[adj.x][adj.y]++;
                } // direction
            } // for temp
        } // while
    }
}

//version 硅谷算法班
public class Solution {
    /**
     * @param grid: a 2D grid
     * @return: An integer
     */
    public int shortestDistance(int[][] grid) {
        // write your code here
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return -1;
        }

        int ans = Integer.MAX_VALUE;

        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++) {
                if (grid[i][j] == 0) {
                    ans = Math.min(ans, bfs(grid, i, j));
                }
            }
        }
        return ans == Integer.MAX_VALUE ? -1 : ans;
    }

    private int bfs(int[][] grid, int sx, int sy) {
        Queue<Integer> qx = new LinkedList<>();
        Queue<Integer> qy = new LinkedList<>();
        boolean[][] v = new boolean[grid.length][grid[0].length];

        qx.offer(sx);
        qy.offer(sy);
        v[sx][sy] = true;

        int[] dx = {1, -1, 0, 0};
        int[] dy = {0, 0, 1, -1};

        int dist = 0;
        int sum = 0;

        while (!qx.isEmpty()) {
            dist++;
            int size = qx.size();
            for (int i = 0; i < size; i++) {
                int cx = qx.poll();
                int cy = qy.poll();
                for (int k = 0; k < 4; k++) {
                    int nx = cx + dx[k];
                    int ny = cy + dy[k];
                    if (0 <= nx && nx < grid.length && 0 <= ny && ny < grid[0].length && !v[nx][ny]) {
                        v[nx][ny] = true;

                        if (grid[nx][ny] == 1) {
                            sum += dist;
                        }
                        if (grid[nx][ny] == 0) {
                            qx.offer(nx);
                            qy.offer(ny);
                        }
                    }
                }
            }
        }

        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++) {
                if (grid[i][j] == 1 && !v[i][j]) {
                    return Integer.MAX_VALUE;
                }
            }
        }
        return sum;
    }
}
```