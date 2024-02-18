# Algorithms II

<link rel="stylesheet" href="/stylesheets/fold_toc.css">

## 图论基础

### 实现

- 图可以用二维数组实现，也可以用邻接链表实现
- 现实应用中，由于大部分图都是稀疏的，邻接链表实现更优

## 无向图

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
- 将一张给定的图划分成若干个连通分量，可以使用 DFS 实现，具体代码见下文

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
        - 注意，和经典的 DFS（关注节点访问唯一性）不同，寻找欧拉回路的 DFS 关注的是边的访问唯一性
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

        // are the two vertices connected?
        public boolean connected(int v, int w) {
            return id(v) == id(w);
        }
    }
    ```

??? quote "Eulerian Tour: Java Implementation"

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

## 有向图

### DFS&thinsp;与&thinsp;BFS

- 任何无向图都可以视作有向图（每条无向边都视作两条相反的有向边）
- DFS 与 BFS 都是有向图的算法，它们关于无向图和有向图的代码实现是一样的
- DFS 可以解决图的可达性问题（reachability）
    - 应用一：程序控制流分析，寻找不可达的代码片段并进行死码删除、判断 exit 是否可达来检测死循环
    - 应用二：程序运行时通过 [mark-sweep 算法](https://en.wikipedia.org/wiki/Tracing_garbage_collection#Na%C3%AFve_mark-and-sweep)寻找不可达的对象，并进行垃圾回收
- BFS 可以解决的一些图相关问题
    - 应用一：计算多源最短路（覆盖的边的数量最少的路径），BFS 实现，初始化时将所有源点入队即可
    - 应用二：网络爬虫，从某个源点网页出发，BFS 即可；注意 DFS 通常是不可行的，因为很多网站会设计搜索陷阱，在 DFS 首次访问它时通过不断创建并链接到无用的新网页来捕获这个搜索
- 对于有向图相关问题，一个经验是 DFS 常能提供一种解决问题的方法，而且可能很难找到其他解法

### 拓扑排序

- 拓扑排序：将给定的有向无环图（DAG）重新排布，使得所有有向边都朝向上方
- 背景问题：给定一些待办任务以及它们之间的先后关系，设计一种排序使得我们能够顺利按序完成所有任务
- 拓扑排序算法可以用 DFS 实现
    - 对 DAG 进行 DFS 后序遍历，得到的序列的逆序列即为图的一个拓扑序列
    - 具体而言，当 DFS 在某个节点无路可走、即将返回递归时，将该节点入栈（返回递归时入栈代表后序遍历，用栈来存储代表逆序列）；最终栈中节点序列即为得到的拓扑序列
- 算法正确性证明：考虑一条有向边 v -> w，当 dfs(v) 被调用时
    - 情况一：dfs(w) 已经被调用且返回递归，则 w 在 v 之前入栈
    - 情况二：dfs(w) 尚未被调用，则 dfs(w) 将直接或间接地被 dfs(v) 调用并在 dfs(v) 返回之前返回递归，则 w 在 v 之前入栈
    - 情况三：dfs(w) 已经被调用但还没有返回递归，这意味着栈中包含一条从 w 到 v 的路径，所以 v -> w 成环，与 DAG 矛盾
    - 综上所述，w 一定在 v 之前入栈，最终得到的拓扑序列中 v 在 w 之前，证毕
- 一张有向图存在拓扑序列等价于这张图是无环的
    - 充分性：如果是有环的，则拓扑序列显然不存在
    - 必要性：如果是无环的，即 DAG，则 DFS 算法总能找到一个拓扑序列
- 拓扑排序算法也可以不用 DFS 实现，这里给出一种非递归的、基于队列的实现，代码思路参考[这里](../fds/note2.md/#_15)，具体实现参考[这里](https://algs4.cs.princeton.edu/42digraph/TopologicalX.java.html)

### 强连通分量

- 强连通分量是极大的强连通子图，强连通子图是一些相互可达的节点组成的子图
- 将一张给定的图划分成若干个强连通分量，可以使用单次 DFS 实现，详情参考 [wikipedia](https://en.wikipedia.org/wiki/Strongly_connected_component#DFS-based_linear-time_algorithms)，这里介绍一种非常简单的算法，使用两次 DFS 实现
- Kosaraju-Sharir 算法的核心思路：
    - 第一次 DFS：对原图的反向图进行 DFS，得到后序遍历逆序列（即拓扑序列）
    - 第二次 DFS：对原图进行 DFS，按照第一次 DFS 得到的反向图拓扑序列的顺序访问未被访问的节点
- Kosaraju-Sharir 算法的具体解释：
    - 概念：反向图是由原图将所有有向边反向得到的，反向图与原图的强连通分量是一样的
    - 第一次 DFS 相当于得到了原图的逆拓扑序列
    - 第二次 DFS 相当于将逆拓扑序列分割，得到了原图的强连通分量
    - 概念：核 DAG（kernel DAG）
        - 指将原图的每个强连通分量分别收缩成一个节点，从而忽略强连通分量内部的有向边而只关心强连通分量之间的有向边
        - DAG 的拓扑序列不一定是唯一的，但核 DAG 的拓扑序列是唯一的（将强连通分量内的所有节点同视作一个节点）
        - 核 DAG 和原图本质上是完全一样的，只是一种不同的理解视角而已
    - 可以尝试用核 DAG 帮助理解 Kosaraju-Sharir 算法
- Kosaraju-Sharir 算法所需时间为 $\Omicron(E+V)$
    - 证明：关键在于两次 DFS 和求解反向图

### 代码

??? quote "Topological Sort: Java Implementation"

    ```java linenums="1" title="Topological Sort with Depth-First Search"
    package edu.princeton.cs.algs4;

    public class Topological {
        private Iterable<Integer> order;  // topological order
        private int[] rank;               // rank[v] = rank of vertex v in order

        /***************************************************************************
         * actually {@code DepthFirstOrder} is a class in edu.princeton.cs.algs4
         * here modifies and includes it as follows, just for learning
         ***************************************************************************/
        private class DepthFirstOrder {
            private boolean[] marked;
            private Stack<Integer> reversePost;

            private DepthFirstOrder(Digraph G) {
                reversePost = new Stack<Integer>();
                marked = new boolean[G.V()];
                for (int v = 0; v < G.V(); v++)
                    if (!marked[v]) dfs(G, v);
            }

            private void dfs(Digraph G, int v) {
                marked[v] = true;
                for (int w : G.adj(v))
                    if (!marked[w]) dfs(G, w);
                reversePost.push(v);
            }

            private Iterable<Integer> reversePost() {
                return reversePost;
            }
        }

        // determines whether the digraph {@code G} has a topological order and,
        // if so, finds such a topological order.
        public Topological(Digraph G) {
            DirectedCycle finder = new DirectedCycle(G);
            if (!finder.hasCycle()) {
                DepthFirstOrder dfs = new DepthFirstOrder(G);
                order = dfs.reversePost();
                rank = new int[G.V()];
                int i = 0;
                for (int v : order)
                    rank[v] = i++;
            }
        }

        // returns a topological order if the digraph has a topological order,
        // and {@code null} otherwise.
        public Iterable<Integer> order() {
            return order;
        }

        // does the digraph have a topological order?
        public boolean hasOrder() {
            return order != null;
        }

        // the rank of vertex {@code v} in the topological order;
        // -1 if the digraph is not a DAG
        public int rank(int v) {
            if (hasOrder()) return rank[v];
            else            return -1;
        }
    }
    ```

??? quote "Strong Components: Java Implementation"

    ```java linenums="1" title="Strong Components with Twice Depth-First Search"
    package edu.princeton.cs.algs4;

    public class KosarajuSharirSCC {
        private boolean[] marked;     // marked[v] = has vertex v been visited?
        private int[] id;             // id[v] = id of strong component containing v
        private int count;            // number of strongly-connected components

        /***************************************************************************
         * actually {@code DepthFirstOrder} is a class in edu.princeton.cs.algs4
         * here modifies and includes it as follows, just for learning
         ***************************************************************************/
        private class DepthFirstOrder {
            private boolean[] marked;
            private Stack<Integer> reversePost;

            private DepthFirstOrder(Digraph G) {
                reversePost = new Stack<Integer>();
                marked = new boolean[G.V()];
                for (int v = 0; v < G.V(); v++)
                    if (!marked[v]) dfs(G, v);
            }

            private void dfs(Digraph G, int v) {
                marked[v] = true;
                for (int w : G.adj(v))
                    if (!marked[w]) dfs(G, w);
                reversePost.push(v);
            }

            private Iterable<Integer> reversePost() {
                return reversePost;
            }
        }

        public KosarajuSharirSCC(Digraph G) {

            // compute reverse postorder of reverse graph
            DepthFirstOrder dfs = new DepthFirstOrder(G.reverse());

            // run DFS on G, using reverse postorder to guide calculation
            marked = new boolean[G.V()];
            id = new int[G.V()];
            for (int v : dfs.reversePost()) {
                if (!marked[v]) {
                    dfs(G, v);
                    count++;
                }
            }
        }

        // DFS on graph G
        private void dfs(Digraph G, int v) {
            marked[v] = true;
            id[v] = count;
            for (int w : G.adj(v)) {
                if (!marked[w]) dfs(G, w);
            }
        }

        public int id(int v) {
            return id[v];
        }

        public int count() {
            return count;
        }

        // are the two vertices strongly connected?
        public boolean stronglyConnected(int v, int w) {
            validateVertex(v);
            validateVertex(w);
            return id[v] == id[w];
        }
    }
    ```

## 最小生成树

- 图的生成树是一个子图，满足：
    - 是一棵树，即连通且无环
    - 是生成的，即包含原图所有节点
- [最小生成树](https://en.wikipedia.org/wiki/Minimum_spanning_tree)问题：给定一张正边权连通无向图，求解图的生成树，使得树的总边权最小

### 贪心

- 假设：图是连通的，且所有边权是相异的
    - 假设下，给定一张正边权无向图，最小生成树存在且唯一
- 割（cut）是图的节点的一种划分，它将所有节点分成两个非空集合
- 交叉边（crossing edge）是连接割分得到的两个集合的边，它的两端节点分别属于两个集合
- 割定理：对于任意的割，边权最小的交叉边一定属于最小生成树
    - 证明：反证法；假设对于某个割，最小交叉边不属于最小生成树，则一定存在某个交叉边属于最小生成树；注意到最小交叉边可以替代这个交叉边形成更小的生成树，导出矛盾
- 最小生成树的贪心算法：
    - 初始时将所有边标记为未访问
    - 找一个割，使得其交叉边都是未访问的；将其最小交叉边标记为已访问（属于最小生成树）
    - 不断重复，直到找到 V-1 条边
    - 贪心算法的正确性基于割定理
- 贪心算法的实现：
    - Kruskal 算法
    - Prim 算法
    - Boruvka 算法
- 移除假设：
    - 如果图不是连通的，则计算每个连通分量的最小生成树，得到原图的最小生成森林
    - 如果边权不是相异的，则贪心算法仍然正确，但每次只能找到一种可能的最小生成树（相异假设下能找到唯一的最小生成树）

### Kruskal&thinsp;算法

- 最小生成树的 Kruskal 算法：
    - 按边权从小到大向森林 T 中添加边，直到加满 V-1 条边为止（森林 T 成为 MST）
    - 添加边时，总是选择加入 T 后不会形成环的最小权边
- 算法的正确性证明：
    - Kruskal 算法是 MST 贪心算法的特殊情形
    - 假设算法某次添加的边是 e = (v, w)，取 cut 使得所有与 v 在 T 中连通的节点为一个集合，此时 e 恰好是最小交叉边
- 算法的具体实现：
    - 用优先队列维护边，每次删除最小权边
    - 用并查集维护 T 的连通分量，用 find 操作检查是否成环，添加边时用 union 操作更新并查集
- 算法的复杂度分析：
    - Kruskal 算法在最坏情况下求解最小生成树所需时间为 $\Omicron(E\log E)$
    - 每次删除最小权边操作为 $\Omicron(\log E)$，最坏情况下需要删除所有边才能得到结果，即删除次数为 $E$；可以通过堆排序在最坏情况下的时间复杂度来理解
    - 每次 find 操作为 $\Omicron(\lg ^ * V)$，最坏情况下需要尝试所有边才能得到结果，即 find 操作次数为 $E$；如果边已经是有序的，则算法所需时间降为 $\Omicron(E\lg ^ * V)$

### Prim&thinsp;算法

- 最小生成树的 Prim 算法：
    - 从某个节点开始，不断向树 T 中添加边，直到加满 V-1 条边为止（树 T 成为 MST）
    - 添加边时，总是选择有且仅有一端节点属于 T 的最小权边
- 算法的正确性证明：
    - Prim 算法是 MST 贪心算法的特殊情形
    - 假设算法某次添加的边是 e，取 cut 使得所有 T 中的节点为一个集合，此时 e 恰好是最小交叉边
- 算法的 lazy 实现：
    - 用优先队列维护至少有一端节点属于 T 的边，每次删除最小权边
    - 如果删除的最小权边两端节点都属于 T，则忽略它（这条边已经过时了），继续下一次删除
    - 如果删除的最小权边一端节点不属于 T，则用该节点更新优先队列，将与之相连且另一端不在 T 中的边压入优先队列
    - 最坏情况下（lazy 实现使得优先队列中可能存在大量过时的边）时间复杂度为 $\Omicron(E\log E)$ 且空间复杂度为 $\Omicron(E)$
- 算法的 eager 实现：
    - 用优先队列维护节点，其中节点与 T 至少通过一条边直接相连，权值为连接该节点与 T 的最小边权，每次删除具有最小边权的节点
    - 每次删除节点 v 后，用该节点更新优先队列，扫描所有与 v 相连的边 e = (v, w)
        - 如果 w 属于 T，则忽略它（这条边已经过时了）
        - 否则，如果 e 成为连接 w 与 T 的最小权边，则更新 w 的权值，并将 w 压入优先队列（若 w 不在优先队列中）或更新 w 在优先队列中的位置（若 w 在优先队列中）
    - 最坏情况下（eager 实现使得优先队列中至多存储 V 个节点）时间复杂度为 $\Omicron(E\log V)$ 且空间复杂度为 $\Omicron(V)$
    - 更进一步地，d 叉堆可将时间复杂度优化到 $\Omicron(E\log _{E/V} V)$，斐波那契堆可将时间复杂度优化到 $\Omicron(E+V\log V)$
- Kruskal 算法与 Prim 算法比较
    - Prim 算法具有更好的时间复杂度和空间复杂度
    - Kruskal 算法容易实现，代码紧凑，不适用于稠密图
- 最小生成树问题的 $\Omicron(E)$ 线性算法至今没有被找到，而且是否存在线性算法也尚未被证明

### 应用

- [欧几里得最小生成树](https://en.wikipedia.org/wiki/Euclidean_minimum_spanning_tree)
    - 给定 N 个节点，每个节点之间都隐式地存在一条边，边权为两点间的欧几里得距离；相当于是完全图的最小生成树问题
    - 暴力算法：用 $\sim N^ 2/2$ 时间计算每个节点之间的欧几里得距离，然后执行 Prim 算法
    - 较优算法：利用几何知识（如 [Delaunay 三角剖分](https://en.wikipedia.org/wiki/Delaunay_triangulation)）可以将时间减少到 $\sim cN\log N$
- [聚类分析（cluster analysis）](https://en.wikipedia.org/wiki/Cluster_analysis)
    - 给定常数整数 k，将集合划分成 k 个部分，使得每个部分尽可能聚集，聚集程度通过距离函数来定义
    - 单链聚类（single-link clustering）：距离函数定义为分别属于两个聚类的两点之间的距离
    - 单链聚类的经典算法：初始时把集合划分为 V 个聚类，不断将距离函数值最小的两个聚类合并，直到恰好有 k 个聚类；注意到这个算法实际上就是 Kruskal 算法（当恰好有 k 个连通分量时停止）
    - 单链聚类的另一算法：执行 Prim 算法，然后删除边权最大的前 k-1 条边

### 代码

??? quote "Kruskal: Java Implementation"

    ```java linenums="1" title="Kruskal's Algorithm for Minimum-Spanning-Tree"
    package edu.princeton.cs.algs4;

    public class KruskalMST {
        private double weight;                        // weight of MST
        private Queue<Edge> mst = new Queue<Edge>();  // edges in MST

        // compute a minimum spanning tree (or forest) of an edge-weighted graph.
        public KruskalMST(EdgeWeightedGraph G) {

            // build priority queue
            MinPQ<Edge> pq = new MinPQ<Edge>();
            for (Edge e : G.edges())
                pq.insert(e);

            // run greedy algorithm
            UF uf = new UF(G.V());
            while (!pq.isEmpty() && mst.size() < G.V() - 1) {
                Edge e = delMin();
                int v = e.either();
                int w = e.other(v);

                // v-w does not create a cycle
                if (uf.find(v) != uf.find(w)) {
                    uf.union(v, w);     // merge v and w components
                    mst.enqueue(e);     // add edge e to mst
                    weight += e.weight();
                }
            }
        }

        public Iterable<Edge> edges() {
            return mst;
        }

        public double weight() {
            return weight;
        }
    }
    ```

??? quote "Lazy Prim: Java Implementation"

    ```java linenums="1" title="Lazy Prim's Algorithm for Minimum-Spanning-Tree"
    package edu.princeton.cs.algs4;

    public class LazyPrimMST {
        private double weight;       // total weight of MST
        private Queue<Edge> mst;     // edges in the MST
        private boolean[] marked;    // marked[v] = true iff v on tree
        private MinPQ<Edge> pq;      // edges with one endpoint in tree

        // compute a minimum spanning tree (or forest) of an edge-weighted graph.
        public LazyPrimMST(EdgeWeightedGraph G) {
            mst = new Queue<Edge>();
            pq = new MinPQ<Edge>();
            marked = new boolean[G.V()];
            for (int v = 0; v < G.V(); v++)     // run Prim from all vertices to
                if (!marked[v]) prim(G, v);     // get a minimum spanning forest
        }

        // run Prim's algorithm
        private void prim(EdgeWeightedGraph G, int s) {
            scan(G, s);
            while (!pq.isEmpty()) {                     // better to stop when mst has V-1 edges
                Edge e = pq.delMin();
                int v = e.either(), w = e.other(v);
                if (marked[v] && marked[w]) continue;   // lazy, both v and w already scanned
                mst.enqueue(e);                         // add e to MST
                weight += e.weight();
                if (!marked[v]) scan(G, v);
                if (!marked[w]) scan(G, w);
            }
        }

        // add all edges e incident to v onto pq if the other endpoint has not yet been scanned
        private void scan(EdgeWeightedGraph G, int v) {
            marked[v] = true;
            for (Edge e : G.adj(v))
                if (!marked[e.other(v)]) pq.insert(e);
        }

        public Iterable<Edge> edges() {
            return mst;
        }

        public double weight() {
            return weight;
        }
    }
    ```

??? quote "Eager Prim: Java Implementation"

    ```java linenums="1" title="Eager Prim's Algorithm for Minimum-Spanning-Tree"
    package edu.princeton.cs.algs4;

    public class PrimMST {
        private Edge[] edgeTo;        // edgeTo[v] = shortest edge from tree vertex to non-tree vertex
        private double[] distTo;      // distTo[v] = weight of shortest such edge
        private boolean[] marked;     // marked[v] = true if v on tree, false otherwise
        private IndexMinPQ<Double> pq;

        // compute a minimum spanning tree (or forest) of an edge-weighted graph.
        public PrimMST(EdgeWeightedGraph G) {
            edgeTo = new Edge[G.V()];
            distTo = new double[G.V()];
            marked = new boolean[G.V()];
            pq = new IndexMinPQ<Double>(G.V());
            for (int v = 0; v < G.V(); v++)
                distTo[v] = Double.POSITIVE_INFINITY;

            for (int v = 0; v < G.V(); v++)      // run from each vertex to find
                if (!marked[v]) prim(G, v);      // minimum spanning forest
        }

        // run Prim's algorithm in graph G, starting from vertex s
        private void prim(EdgeWeightedGraph G, int s) {
            distTo[s] = 0.0;
            pq.insert(s, distTo[s]);
            while (!pq.isEmpty()) {
                int v = pq.delMin();
                scan(G, v);
            }
        }

        // scan vertex v
        private void scan(EdgeWeightedGraph G, int v) {
            marked[v] = true;
            for (Edge e : G.adj(v)) {
                int w = e.other(v);
                if (marked[w]) continue;         // v-w is obsolete edge
                if (e.weight() < distTo[w]) {
                    distTo[w] = e.weight();
                    edgeTo[w] = e;
                    if (pq.contains(w)) pq.decreaseKey(w, distTo[w]);
                    else                pq.insert(w, distTo[w]);
                }
            }
        }

        public Iterable<Edge> edges() {
            Queue<Edge> mst = new Queue<Edge>();
            for (int v = 0; v < edgeTo.length; v++) {
                Edge e = edgeTo[v];
                if (e != null) {
                    mst.enqueue(e);
                }
            }
            return mst;
        }

        public double weight() {
            double weight = 0.0;
            for (Edge e : edges())
                weight += e.weight();
            return weight;
        }
    }
    ```