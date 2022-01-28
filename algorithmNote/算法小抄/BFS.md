# BFS



例题

leetcode 111 752



## BFS算法框架

```java
int BFS(Node start, Node end) {
    Queue<Node> q; // 核⼼数据结构
	Set<Node> visited; // 避免⾛回头路
    
    q.offer(start);
    visited.add(start);
    int distance = 1;
    
    while (!q.isEmpty()) {
        int size = q.size();
        // 取出同一层的节点，分别向下一层扩散
        for (int i = 0; i < size; i++) {
            Node curr = q.poll();
            if (curr == end) {
                return distance;
            }
            for (Node next : curr.children()) {
                if (!visited.contains(next) && isValid(next)) {
                    q.offer(next);
                    visited.add(next);
                }
            }
        }
        distance++;
    }
}
```

