# 九章算法强化 L2 Union Find & Trie
一种用于支持集合快速合并和查找操作的数据结构。跟动态连通性有关的问题，用Union Find。跟静态连通性（图是静态的）有关的问题，用BFS

## 如何实现
使用哈希表或者数组来存储每个节点的父节点。如果节点不是连续整数，最好用哈希表来存储。最开始所有的父节点都指向自己。

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020041502170180.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020041502152630.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)

还有一种算法就是把小集合合并到大集合上面，那么就需要维护一个表示大小的数组。

## 例题 Connecting Graph
Given n nodes in a graph labeled from 1 to n. There is no edges in the graph at beginning.
You need to support the following method:
**connect(a, b)**, add an edge to connect node a and node b.
**query(a, b)**, check if two nodes are connected
Have you met this question in a real interview?  

Example 1:
Input:
ConnectingGraph(5)
query(1, 2)
connect(1, 2)
query(1, 3) 
connect(2, 4)
query(1, 4) 
Output:
[false,false,true]

Example 2:
Input:
ConnectingGraph(6)
query(1, 2)
query(2, 3)
query(1, 3)
query(5, 6)
query(1, 4)
Output:
[false,false,false,false,false]

## Solution
这题有两种写法，一种采用路径压缩算法；另一种将小集合合并到大集合中，还可以将这两种方法合到一起
1. 路径压缩写法
```java
public class ConnectingGraph {
    int[] father;
    int[] size;
    public ConnectingGraph(int n) {
        // do intialization if necessary
        father = new int[n + 1];
        size = new int[n + 1];
        for (int i = 0; i <= n; i++) {
            father[i] = i;
            size[i] = 1;
        }
    }
    
    public int find(int x) {
        int j = x;
        int tmp = x;
        while (father[j] != j) {
            j = father[j];
        }
        
        while (tmp != j) {
            int temp = father[tmp];
            father[tmp] = j;
            tmp = temp;
        }
        
        return j;
    }
    
    public void connect(int a, int b) {
        // write your code here
        int root_a = find(a);
        int root_b = find(b);
        if (root_b != root_a) {
            father[root_a] = root_b；
            size[root_b] += size[root_a];
        }
    }

    public boolean query(int a, int b) {
        // write your code here
        return find(a) == find(b);
    }
}
```
2. 小的合并到大的
```java
public class ConnectingGraph {
    int[] father;
    int[] size;
    public ConnectingGraph(int n) {
        // do intialization if necessary
        father = new int[n + 1];
        size = new int[n + 1];
        for (int i = 0; i <= n; i++) {
            father[i] = i;
            size[i] = 1;
        }
    }
    
    public int find(int x) {
        int j = x;
        int tmp = x;
        while (father[j] != j) {
            j = father[j];
        }
        return j;
    }
    
    public void connect(int a, int b) {
        // write your code here
        int root_a = find(a);
        int root_b = find(b);
        if (root_b != root_a) {
            if (size[root_a] > size[root_b]) {
                father[root_b] = root_a;
                size[root_a] += size[root_b];
            } else {
                father[root_a] = root_b;
                size[root_b] += size[root_a];   
            }
        }
    }

    public boolean query(int a, int b) {
        // write your code here
        return find(a) == find(b);
    }
}
```

##  例题 Connecting Graph ll
在前面题目的基础上，添加一个返回连通块个数的功能 (query())

##  Solution
最开始连通块个数就是 n。每当进行一次有效的连接，连通块的个数就会减一，只需要记录有效连接的个数就可以计算得到总的连通块个数。
```java
public class ConnectingGraph3 {
    /**
     * @param a: An integer
     * @param b: An integer
     * @return: nothing
     */
    int[] father;
    int[] size;
    int connect;
    public ConnectingGraph3(int n) {
        // initialize your data structure here.
        father = new int[n + 1];
        size = new int[n + 1];
        connect = n;
        for (int i = 0; i <= n; i++) {
            father[i] = i;
            size[i] = 1;
        }        
    }
    
    public int find(int x) {
        int j = x;
        int tmp = x;
        while (father[j] != j) {
            j = father[j];
        }
        return j;
    } 
    
    public void connect(int a, int b) {
        // write your code here
        int root_a = find(a);
        int root_b = find(b);
        if (root_b != root_a) {
            connect--;
            if (size[root_a] > size[root_b]) {
                father[root_b] = root_a;
                size[root_a] += size[root_b];
            } else {
                father[root_a] = root_b;
                size[root_b] += size[root_a];   
            }
        }        
    }

    /**
     * @return: An integer
     */
    public int query() {
        // write your code here
        return connect;
    }
}
```

## 例题 200. Number of Islands
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
首先定义好一个father数组用来存储每个节点的父节点。其次遍历 grid 中的所有元素当发现 $grid[i][j]$ 为1时，island数目加一，将 $grid[i][j]$ 修改为0，然后和四周的四个点union，当发生一次有效的 union 的时候，island数目减一，有效union是指father不同然后union起来。需要注意的是由于需要确保有效union，最好采用压缩路径的写法， 但是其实结果没有区别，因为 find() 最终返回的就是最上层的 father。

或者可以理解为，如果一个位置为 1，则将其与相邻四个方向上的 1 在并查集中进行合并，最终岛屿的数量就是并查集中连通分量的数目。
```java
class Solution {
    int[] father;
    int island;
    
    public int numIslands(char[][] grid) {
        if(grid == null || grid.length == 0 || grid[0].length == 0) {
            return 0;
        }
        
        island = 0;
        
        int m = grid.length;
        int n = grid[0].length;
        
        father = new int[m*n];
        for (int i = 0; i < father.length; i++) {
            father[i] = i;
        }
        
        int[] dx = {0, 0, 1, -1};
        int[] dy = {1, -1, 0, 0};
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    island++;
                    grid[i][j] = '0';
                    for (int k = 0; k < 4; k++) {
                        if (inBound(i+dx[k], j+dy[k], grid) && grid[i+dx[k]][j+dy[k]] == '1') {
                            union(i * n + j, (i + dx[k]) * n + (j + dy[k]));
                        }
                    }
                }
            }
        }
        return island;
    }
    
    public boolean inBound(int i, int j, char[][] grid) {
        return i >= 0 && i < grid.length && j >= 0 && j < grid[0].length;
    }
    
    public void union(int i, int j) {
        int f1 = find(i);
        int f2 = find(j);
        if (f1 != f2) {
            father[f1] = f2;
            island--;
        }
    }
    
    public int find(int a) {
        int i = a;
        while (i != father[i]) {
            i = father[i];
        }
        
        while (a != i) {
            int tmp = father[a];
            father[a] = i;
            a = tmp;
        }
        
        return i;
    }
}
```

##  例题 305. Number of Islands II
A 2d grid map of m rows and n columns is initially filled with water. We may perform an addLand operation which turns the water at position (row, col) into a land. Given a list of positions to operate, count the number of islands after each addLand operation. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

Example:
Input: m = 3, n = 3, positions = [[0,0], [0,1], [1,2], [2,1]]
Output: [1,1,2,3]

Explanation:
Initially, the 2d grid grid is filled with water. (Assume 0 represents water and 1 represents land).
0 0 0
0 0 0
0 0 0

Operation #1: addLand(0, 0) turns the water at grid[0][0] into a land.
1 0 0
0 0 0   Number of islands = 1
0 0 0

Operation #2: addLand(0, 1) turns the water at grid[0][1] into a land.
1 1 0
0 0 0   Number of islands = 1
0 0 0

Operation #3: addLand(1, 2) turns the water at grid[1][2] into a land.
1 1 0
0 0 1   Number of islands = 2
0 0 0

Operation #4: addLand(2, 1) turns the water at grid[2][1] into a land.
1 1 0
0 0 1   Number of islands = 3
0 1 0

## Solution
和上面一个的思路完全一致，也是统计等于 1 的元素个数，然后减去有效union次数。区别在于采用HashMap来储存父节点，同时没采用压缩路径算法。
```java
class Solution {
    HashMap<Integer, Integer> size;
    HashMap<Integer, Integer> father;
    boolean[][] isIsland;
    
    public List<Integer> numIslands2(int m, int n, int[][] positions) {
        List<Integer> res = new ArrayList<>();
        if (positions == null || positions.length == 0) {
            return res;
        }
        
        father = new HashMap<Integer, Integer>();
        size = new HashMap<Integer, Integer>();
        isIsland = new boolean[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int index = convert(i, j, n);
                father.put(index, index);
                size.put(index, 1);
                isIsland[i][j] = false;
            }
        }
        
        int count = 0;
        int[] dx = {0,0,1,-1};
        int[] dy = {1,-1,0,0};
        for (int[] pos : positions) {
            int id = convert(pos[0], pos[1], n);
            if (!isIsland[pos[0]][pos[1]]) {
                isIsland[pos[0]][pos[1]] = true;
                count++;
                for (int i = 0; i < 4; i++) {
                    int x = pos[0] + dx[i];
                    int y = pos[1] + dy[i];
                    if (inBound(x,y,m,n) && isIsland[x][y]) {
                        int newId = convert(x,y,n);
                        if (find(id) != find(newId)) {
                            connect(find(id), find(newId));
                            count--;                        
                        }
                    }
                }  
            }
            res.add(count);
        }
        return res;
    }
    
    public boolean inBound(int i, int j, int m, int n) {
        return i >= 0 && i < m && j >= 0&& j < n;
    }
    
    public void connect(int a, int b) {
        int father_a = find(a);
        int father_b = find(b);
        if (father_a != father_b) {
            if (size.get(father_a) > size.get(father_b)) {
                size.put(father_a, size.get(father_a) + size.get(father_b));
                father.put(father_b, father_a);
            } else {
                size.put(father_b, size.get(father_a) + size.get(father_b));
                father.put(father_a, father_b);              
            }
        }
    }
    
    public int find(int id) {
        int index = id;
        while (father.get(index) != index) {
            index = father.get(index);
        }
        return index;
    }
    
    public int convert(int x, int y, int n) {
        return x * n + y;
    }
}
```

##  例题 261. Graph Valid Tree
Given n nodes labeled from 0 to n-1 and a list of undirected edges (each edge is a pair of nodes), write a function to check whether these edges make up a valid tree.

Example 1:
Input: n = 5, and edges = [[0,1], [0,2], [0,3], [1,4]]
Output: true

Example 2:
Input: n = 5, and edges = [[0,1], [1,2], [2,3], [1,3], [1,4]]
Output: false

Note: you can assume that no duplicate edges will appear in edges. Since all edges are undirected, [0,1] is the same as [1,0] and thus will not appear together in edges.

##  Solution
一个树有两个条件：
1. 没有环
2. 所有点都连通 (有且仅有一个连通块)

```java
class Solution {
    int[] father;
    int[] size;
    
    public boolean validTree(int n, int[][] edges) {
        if (edges == null || edges.length != n - 1) {
            return false;
        }
        
        father = new int[n];
        size = new int[n];
        for (int i = 0; i < n; i++) {
            father[i] = i;
            size[i] = 1;
        }
        
        int count = n;
        
        for (int[] edge : edges) {
            if (find(edge[0]) != find(edge[1])) {
                connect(find(edge[0]), find(edge[1]));
                n--;
            }
        }
        return n == 1;
    }
    
    public void connect(int a, int b) {
        int father_a = find(a);
        int father_b = find(b);
        if (father_a != father_b) {
            if (size[father_a] > size[father_b]) {
                size[father_a] += size[father_b];
                father[father_b] = father_a;
            } else {
                size[father_b] += size[father_a];
                father[father_a] = father_b;
            }
        }
    }
    
    public int find(int a) {
        while (father[a] != a) {
            a = father[a];
        }
        return a;
    }
}
```

## 例题 721. Accounts Merge
Given a list accounts, each element $accounts[i]$ is a list of strings, where the first element $accounts[i][0]$ is a name, and the rest of the elements are emails representing emails of the account.

Now, we would like to merge these accounts. Two accounts definitely belong to the same person if there is some email that is common to both accounts. Note that even if two accounts have the same name, they may belong to different people as people could have the same name. A person can have any number of accounts initially, but all of their accounts definitely have the same name.

After merging the accounts, return the accounts in the following format: the first element of each account is the name, and the rest of the elements are emails in sorted order. The accounts themselves can be returned in any order.

Example 1:
Input: 
accounts = [["John", "johnsmith@mail.com", "john00@mail.com"], ["John", "johnnybravo@mail.com"], ["John", "johnsmith@mail.com", "john_newyork@mail.com"], ["Mary", "mary@mail.com"]]
Output: [["John", 'john00@mail.com', 'john_newyork@mail.com', 'johnsmith@mail.com'],  ["John", "johnnybravo@mail.com"], ["Mary", "mary@mail.com"]]

Explanation: 
The first and third John's are the same person as they have the common email "johnsmith@mail.com".
The second John and Mary are different people as none of their email addresses are used by other accounts.
We could return these lists in any order, for example the answer [['Mary', 'mary@mail.com'], ['John', 'johnnybravo@mail.com'], 
['John', 'john00@mail.com', 'john_newyork@mail.com', 'johnsmith@mail.com']] would still be accepted.
Note:

The length of accounts will be in the range [1, 1000].
The length of $accounts[i]$ will be in the range [1, 10].
The length of $accounts[i][j]$ will be in the range [1, 30].

## 例题
```java

```

#  Trie
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200415042239483.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
每一个都代表了一个前缀
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200415101221404.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3NjAyNg==,size_16,color_FFFFFF,t_70)
## 例题 208. Implement Trie (Prefix Tree)
Implement a trie with insert, search, and startsWith methods.

Example:
Trie trie = new Trie();
trie.insert("apple");
trie.search("apple");   // returns true
trie.search("app");     // returns false
trie.startsWith("app"); // returns true
trie.insert("app");   
trie.search("app");     // returns true

Note:
You may assume that all inputs are consist of lowercase letters a-z.
All inputs are guaranteed to be non-empty strings.

## Solution
Trie 数据结构由最基本的Trie Node组成，同时有一个 Root Trie Node。主要需要实现的方法就是 insert, search 和 startWith。因此实现Trie，首先要实现Trie Node

```java
class Trie {
    TrieNode head;
    
    class TrieNode {
        TrieNode[] sons;
        boolean isWord;
        String word;
        
        public TrieNode() {
            sons = new TrieNode[26];
            for (int i = 0; i < 26; i++) {
                sons[i] = null;
            }
            isWord = false;
        }
    }

    /** Initialize your data structure here. */
    public Trie() {
        head = new TrieNode();
    }
    
    /** Inserts a word into the trie. */
    public void insert(String word) {
        TrieNode tmp = head;
        for (int i = 0; i < word.length(); i++) {
            int loc = word.charAt(i) - 'a';
            if (tmp.sons[loc] == null) {
                tmp.sons[loc] = new TrieNode(); 
            }
            tmp = tmp.sons[loc];
        }
        tmp.isWord = true;
        tmp.word = word;
    }
    
    /** Returns if the word is in the trie. */
    public boolean search(String word) {
        TrieNode tmp = head;
        for (int i = 0; i < word.length(); i++) {
            int loc = word.charAt(i) - 'a';
            if (tmp.sons[loc] == null) {
                return false;
            }
            tmp = tmp.sons[loc];
        }
        return tmp.isWord;
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    public boolean startsWith(String prefix) {
        TrieNode tmp = head;
        for (int i = 0; i < prefix.length(); i++) {
            int loc = prefix.charAt(i) - 'a';
            if (tmp.sons[loc] == null) {
                return false;
            }
            tmp = tmp.sons[loc];
        }
        return true;
    }
}
```

## 例题 211. Add and Search Word - Data structure design
Design a data structure that supports the following two operations:

> void addWord(word) 
> bool search(word)

search(word) can search a literal word or a regular expression string containing only letters **a-z** or **dot (.)**.
A **dot (.)** means it can represent any one letter.

Example:
addWord("bad")
addWord("dad")
addWord("mad")
search("pad") -> false
search("bad") -> true
search(".ad") -> true
search("b..") -> true

Note:
You may assume that all words are consist of lowercase letters a-z.

## Solution
由于 dot(.) 可以代表任何字符，所以遇到dot(.)时，遍历所有的sons，于是要采用递归的写法，同时由于Trie的树特性，每次寻找字符串时都需要说明从哪个 TrieNode 开始寻找。
两种写法：思路一样，区别在于更细致的分类讨论，第一种要比第二种快，counter intuitive

需要注意的点：
> 1. 遇到 dot(.) 遍历所有 sons Trie Node, 如果其中任意一个找到了，返回 true，否则返回 false
> 2. 需要特殊讨论 search(“abc.”) 的情况，dot(.) 位于最后一位，此时 word.substring(i+1) 为空， 在第一种写法中添加了 hasWord() method 来考虑；第二种写法中，在 search() method 最前面添加了一个 if
> 来处理

```java
class WordDictionary {
    TrieNode head;
    
    class TrieNode {
        TrieNode[] sons;
        boolean isWord;
        
        public TrieNode() {
            sons = new TrieNode[26];
            for (int i = 0; i < 26; i++) {
                sons[i] = null;
            }
            isWord = false;
        }
    }

    /** Initialize your data structure here. */
    public WordDictionary() {
        head = new TrieNode();
    }
    
    /** Adds a word into the data structure. */
    public void addWord(String word) {
        TrieNode tmp = head;
        for (int i = 0; i < word.length(); i++) {
            int loc = word.charAt(i) - 'a';
            if (tmp.sons[loc] == null) {
                tmp.sons[loc] = new TrieNode();
            }
            tmp = tmp.sons[loc];
        }
        tmp.isWord = true;
    }
    
    /** Returns if the word is in the data structure. A word could contain the dot character '.' to represent any one letter. */
    public boolean search(String word) {
        return search(word, head);
    }
    
    public boolean search(String word, TrieNode node) {
        for (int i = 0; i < word.length(); i++) {
            if (i < word.length() - 1 && word.charAt(i) == '.') {
                for (int j = 0; j < 26; j++) {
                    if (node.sons[j] != null) {
                        if (search(word.substring(i+1), node.sons[j])) {
                            return true;
                        }
                    }
                }
                return false;
            } else if (i == word.length() - 1 && word.charAt(i) == '.') {
                return hasWord(node);
            } else {
                int loc = word.charAt(i) - 'a';
                if (node.sons[loc] == null) {
                    return false;
                }
                node = node.sons[loc];
            }
        }
        return node.isWord;
    }
    
    public boolean hasWord(TrieNode node) {
        for (int i = 0; i < 26; i++) {
            if (node.sons[i] != null && node.sons[i].isWord) return true;
        }
        return false;
    }
}
```

第二种写法，除了 search(String word, TrieNode node) 的写法以及去掉 hasWord()，其他和上面写法一样。
```java
class WordDictionary {
    TrieNode head;
    
    class TrieNode {}

    /** Initialize your data structure here. */
    public WordDictionary() {
        head = new TrieNode();
    }
    
    /** Adds a word into the data structure. */
    public void addWord(String word) {}
    
    /** Returns if the word is in the data structure. A word could contain the dot character '.' to represent any one letter. */
    public boolean search(String word) {
        return search(word, head);
    }
    
    public boolean search(String word, TrieNode node) {
        if (word == null) return node.isWord;
        for (int i = 0; i < word.length(); i++) {
            if (word.charAt(i) == '.') {
                for (int j = 0; j < 26; j++) {
                    if (node.sons[j] != null) {
                        if (search(word.substring(i+1), node.sons[j])) {
                            return true;
                        }
                    }
                }
                return false;
            } else {
                int loc = word.charAt(i) - 'a';
                if (node.sons[loc] == null) {
                    return false;
                }
                node = node.sons[loc];
            }
        }
        return node.isWord;
    }
}
```

## 例题 425. Word Squares
Given a set of words (without duplicates), find all word squares you can build from them.
A sequence of words forms a valid word square if the kth row and column read the exact same string, where 0 ≤ k < max(numRows, numColumns).
For example, the word sequence ["ball","area","lead","lady"] forms a word square because each word reads the same both horizontally and vertically.

b a l l
a r e a
l e a d
l a d y

Note:
There are at least 1 and at most 1000 words.
All words will have the exact same length.
Word length is at least 1 and at most 5.
Each word contains only lowercase English alphabet a-z.

Example 1:
Input:
["area","lead","wall","lady","ball"]
Output:
[
  [ "wall",
    "area",
    "lead",
    "lady"
  ],
  [ "ball",
    "area",
    "lead",
    "lady"
  ]
]
Explanation:
The output consists of two word squares. The order of output does not matter (just the order of words in each word square matters).

Example 2:
Input:
["abat","baba","atan","atal"]
Output:
[
  [ "baba",
    "abat",
    "baba",
    "atan"
  ],
  [ "baba",
    "abat",
    "baba",
    "atal"
  ]
]
Explanation:
The output consists of two word squares. The order of output does not matter (just the order of words in each word square matters).

## Solution 
剪枝法和Trie一起使用

## 例题 79. Word Search
Given a 2D board and a word, find if the word exists in the grid.
The word can be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once.

Example:
board =
[
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]
Given word = "ABCCED", return true.
Given word = "SEE", return true.
Given word = "ABCB", return false.

Constraints:
board and word consists only of lowercase and uppercase English letters.
1 <= board.length <= 200
1 <= board[i].length <= 200
1 <= word.length <= 10^3

## Solution 
采用backtracking的写法，backtracking可以用于判断是否有可行解，找到可行解。
```java
class Solution {
    
    public boolean exist(char[][] board, String word) {
        if (board == null || board.length == 0 || board[0].length == 0) {
            return false;
        }
        
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                if (backtrack(board, word, i, j, 0)) {
                    return true;
                }
            }
        }
        return false;
    }
    
    public boolean backtrack(char[][] board, String word, int x, int y, int index) {
        if (index >= word.length()) return true;
        if (!inBound(board, x, y)) return false;
        if (board[x][y] != word.charAt(index)) return false;
        
        int[] dx = {0, 0, 1, -1};
        int[] dy = {1, -1, 0, 0};
        
        boolean res = false;
        board[x][y] = '#';
        for (int i = 0; i < 4; i++) {
            if (backtrack(board, word, x + dx[i], y + dy[i], index + 1)) {
                res = true;
                break;
            }
        }
        board[x][y] = word.charAt(index);
        return res;
    }
    
    public boolean inBound(char[][] board, int x, int y) {
        return x >= 0 && x < board.length && y >= 0 && y < board[0].length;
    }
}
```

## 例题 212. Word Search II
Given a 2D board and a list of words from the dictionary, find all words in the board.
Each word must be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once in a word.

Example:
Input: 
board = [
  ['o','a','a','n'],
  ['e','t','a','e'],
  ['i','h','k','r'],
  ['i','f','l','v']
]
words = ["oath","pea","eat","rain"]
Output: ["eat","oath"]

Note:
All inputs are consist of lowercase letters a-z.
The values of words are distinct.

## Solution 1
最简单直观的解法，采用Word Search的做法判断字典中每一个单词是否在矩阵中。

## Solution 2
采用Trie，Trie可以帮助判断是否存在以 “xxxx” 为前缀的单词。所以先将字典中的所有单词构建一个 Trie，然后利用这个在board中遍历所有有效解。遍历方法：以board中每一个点为起始点搜索，搜索函数的定义是

> search(char[][] board, int x, int y, TrieNode node, List<String> res)，
> 搜索以 node为起始点，以board[x][y]为第一个字符，board中所有满足条件的word，并把结果放到res中。

以node为起始点搜索时，首先要判断node的有没有对应board[x][y]的子节点，如果没有则比不可能有解，直接return。需要注意最开始的起始点一定是 Trie 数据结构的 root。
```java
class Solution {
    class TrieNode {
        HashMap<Character, TrieNode> sons;
        String word;
        
        public TrieNode() {
            sons = new HashMap<>();
            word = null;
        }
    }
    
    class Trie {
        TrieNode head;
        
        public Trie() {
            head = new TrieNode();
        }
        public void insert(String word) {
            TrieNode node = head;
            for (int i = 0; i < word.length(); i++) {
                if (!node.sons.containsKey(word.charAt(i))) {
                    node.sons.put(word.charAt(i), new TrieNode());
                }
                node = node.sons.get(word.charAt(i));
            }
            node.word = word;
        }
    }
    
    public List<String> findWords(char[][] board, String[] words) {
        List<String> res = new ArrayList<>();
        if (words == null || words.length == 0 || board == null || board.length == 0 || board[0].length == 0) {
            return res;
        }
        Trie trie = new Trie();
        for (String word : words) {
            trie.insert(word);
        }
        
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                search(board, i , j, trie.head, res);
            }
        }
        return res;
    }
    
    public void search(char[][] board, int x, int y, TrieNode node, List<String> res) {
        if (!node.sons.containsKey(board[x][y])) return;
        TrieNode child = node.sons.get(board[x][y]);
        if (child.word != null) {
            if (!res.contains(child.word)) {
                res.add(child.word);
            }
        }
        
        int[] dx = {0, 0, 1, -1};
        int[] dy = {1, -1, 0, 0};
        
        char tmp = board[x][y];
        board[x][y] = '1';
        for (int i = 0; i < 4; i++) {
            int nx = x + dx[i];
            int ny = y + dy[i];
            if (inBound(board, nx, ny)) {
                search(board, nx, ny, child, res);
            }
        }
        board[x][y] = tmp;
    }
    
    public boolean inBound(char[][] board, int x, int y) {
        return x >= 0 && x < board.length && y >= 0 && y < board[0].length;
    }
}
```

# 总结https://www.jianshu.com/p/7332dcb978b2)