# Algorithms II

<style>
    .md-sidebar--secondary .md-nav > .md-nav__list > li > a + .md-nav {
      display: none;
    }
    .md-sidebar--secondary .md-nav > .md-nav__list > li > a.is-active + .md-nav {
      display: block;
    }
</style>

## 无向图

### 实现

- 无向图可以用二维数组实现，也可以用邻接链表实现
- 现实应用中，由于大部分图都是稀疏的，邻接链表实现更优

### DFS&thinsp;与&thinsp;BFS

- 深度优先搜索（DFS）的复杂度：
    - DFS 访问所有与源点连通的节点所需时间为 $\Omicron(E+V)$
    - DFS 处理后，可以在常数时间内判断某个节点是否与源点连通，可以在线性时间内找到从源点到某个节点的一条路径（时间开销正比于该路径长度）
- 广度优先搜索（BFS）的复杂度：
    - BFS 计算从源点到其他所有节点的最短路径（覆盖的边的数量最少的路径）所需时间为 $\Omicron(E+V)$
    - BFS 按照离源点的距离从近到远访问所有节点
- 所需数据结构的比较：
    - DFS 将未处理的节点存储在栈中；经典的 DFS 是递归实现的，同样可以视作使用了栈（函数调用栈）
    - BFS 将未处理的节点存储在队列中
- 两种搜索方式的选择：
    - 通常不根据时间复杂度来选择使用 DFS 还是 BFS，而是根据两者的搜索特性来选择
    - DFS 常用于遍历整张图，多数情况下比 BFS 更容易实现且节省空间开销
    - BFS 常用于搜索距离源点较近的节点，即仅遍历源点周围的部分图

### 连通分量

- 连通分量是极大的连通子图，连通子图是一些相互连通的节点组成的子图
- 将一张给定的图划分成若干个连通分量，可以使用 DFS 实现

### 图相关问题

- 问题一：判断一张给定的图是否是[二部图](https://en.wikipedia.org/wiki/Bipartite_graph)
    - DFS 实现，较为简单，代码参考[这里](https://algs4.cs.princeton.edu/41graph/Bipartite.java.html)
- 问题二：寻找环（cycle）
    - DFS 实现，较为简单，代码参考[这里](https://algs4.cs.princeton.edu/41graph/Cycle.java.html)
- 问题三：寻找欧拉回路（Eulerian tour），即经过每条边恰好一次的回路
    - 起源于[柯尼斯堡七桥问题](https://en.wikipedia.org/wiki/Seven_Bridges_of_K%C3%B6nigsberg)
    - 一个连通图是欧拉图（即存在欧拉回路）等价于所有节点的度数都是偶数
        - 证明：对于回路中每个节点，进出节点的边都是成对出现的
    - DFS 实现，思路参考[这里](../fds/note2.md/#_19)，具体代码见下文
- 问题四：寻找哈密顿回路（Hamiltonian cycle），即经过每个节点恰好一次的回路
    - 这是一个经典的 NP 完全问题，相当棘手
- 问题五：判断两张给定的图是否是[同构](https://en.wikipedia.org/wiki/Graph_isomorphism)的
    - 至今没有找到解决这个问题的有效算法
- 问题六：图的[平面性](https://en.wikipedia.org/wiki/Planarity_testing)问题，即一张图如何能被绘制在一个平面上而且没有任何边是交叉的
    - 目前已经找到基于 DFS 的线性时间平面图算法，相当复杂

### 代码

??? quote "Connected Components: Java Implementation"

    ```java linenums="1" title="Connected Components with Depth-First Search"
    package edu.princeton.cs.algs4;

    public class CC {
        private boolean[] marked;   // marked[v] = has vertex v been marked?
        private int[] id;           // id[v] = id of connected component containing v
        private int[] size;         // size[id] = number of vertices in given component
        private int count;          // number of connected components

        public CC(Graph G) {
            marked = new boolean[G.V()];
            id = new int[G.V()];
            size = new int[G.V()];
            for (int v = 0; v < G.V(); v++) {
                if (!marked[v]) {
                    dfs(G, v);
                    count++;
                }
            }
        }

        // depth-first search for a Graph
        private void dfs(Graph G, int v) {
            marked[v] = true;
            id[v] = count;
            size[count]++;
            for (int w : G.adj(v)) {
                if (!marked[w]) {
                    dfs(G, w);
                }
            }
        }
        
        public int id(int v) {
            return id[v];
        }

        public int size(int v) {
            return size[id[v]];
        }

        public int count() {
            return count;
        }

        // is the two vertex connected?
        public boolean connected(int v, int w) {
            validateVertex(v);
            validateVertex(w);
            return id(v) == id(w);
        }
    }
    ```

??? quote "Eulerian Tour: Implementation"

    ```java linenums="1" title="Eulerian Tour with Depth-First Search"
    package edu.princeton.cs.algs4;

    public class EulerianCycle {
        private Stack<Integer> cycle = new Stack<Integer>();  // Eulerian cycle; null if no such cycle

        // an undirected edge, with a field to indicate whether the edge has already been used
        private static class Edge {
            private final int v;
            private final int w;
            private boolean isUsed;

            public Edge(int v, int w) {
                this.v = v;
                this.w = w;
                isUsed = false;
            }

            // returns the other vertex of the edge
            public int other(int vertex) {
                if      (vertex == v) return w;
                else if (vertex == w) return v;
                else throw new IllegalArgumentException("Illegal endpoint");
            }
        }

        // computes an Eulerian cycle in the specified graph, if one exists
        public EulerianCycle(Graph G) {

            // must have at least one edge
            if (G.E() == 0) return;

            // necessary condition: all vertices have even degree
            // (this test is needed, or it might find an Eulerian path instead of cycle)
            for (int v = 0; v < G.V(); v++)
                if (G.degree(v) % 2 != 0) return;

            // create local view of adjacency lists, to iterate one vertex at a time
            // the helper Edge data type is used to avoid exploring both copies of an edge v-w
            Queue<Edge>[] adj = (Queue<Edge>[]) new Queue[G.V()];
            for (int v = 0; v < G.V(); v++)
                adj[v] = new Queue<Edge>();

            for (int v = 0; v < G.V(); v++) {
                int selfLoops = 0;
                for (int w : G.adj(v)) {
                    // careful with self loops
                    if (v == w) {
                        if (selfLoops % 2 == 0) {
                            Edge e = new Edge(v, w);
                            adj[v].enqueue(e);
                            adj[w].enqueue(e);
                        }
                        selfLoops++;
                    }
                    else if (v < w) {
                        Edge e = new Edge(v, w);
                        adj[v].enqueue(e);
                        adj[w].enqueue(e);
                    }
                }
            }

            // initialize stack with any non-isolated vertex
            int s = nonIsolatedVertex(G);
            Stack<Integer> stack = new Stack<Integer>();
            stack.push(s);

            // greedily search through edges in iterative DFS style
            cycle = new Stack<Integer>();
            while (!stack.isEmpty()) {
                int v = stack.pop();
                while (!adj[v].isEmpty()) {
                    Edge edge = adj[v].dequeue();
                    if (edge.isUsed) continue;
                    edge.isUsed = true;
                    stack.push(v);
                    v = edge.other(v);
                }
                // push vertex with no more leaving edges to cycle
                cycle.push(v);
            }

            // check if all edges are used
            if (cycle.size() != G.E() + 1)
                cycle = null;
        }

        // returns the sequence of vertices on an Eulerian cycle
        public Iterable<Integer> cycle() {
            return cycle;
        }

        // returns true if the graph has an Eulerian cycle
        public boolean hasEulerianCycle() {
            return cycle != null;
        }

        // returns any non-isolated vertex; -1 if no such vertex
        private static int nonIsolatedVertex(Graph G) {
            for (int v = 0; v < G.V(); v++)
                if (G.degree(v) > 0)
                    return v;
            return -1;
        }
    }
    ```