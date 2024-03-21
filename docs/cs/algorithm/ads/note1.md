# 高级数据结构

## AVL&thinsp;树

- AVL 树是一种自平衡二叉搜索树，其中每个节点的左右子树高度差不超过 1
- AVL 树的高度 $h = \Omicron(\log n)$
- AVL 树的搜索、插入和删除操作的时间复杂度均为 $\Omicron(\log n)$

### 再平衡

AVL 树的再平衡通过所谓的“旋转”操作实现，其精髓在于把节点在竖直方向上进行升降，而不在水平方向上做移动，从而保证二叉搜索树性质不被破坏。

旋转操作分为以下四种情况，这里记平衡因子临时为 $\pm 2$ 的节点为 X，记其较高子树的根节点为 Z，平衡因子记为 BF = 右子树高度 - 左子树高度：

- RR：Z 是 X 的右儿子，BF(Z) >= 0 => rotateLeft
- LL：Z 是 X 的左儿子，BF(Z) <= 0 => rotateRight
- RL：Z 是 X 的右儿子，BF(Z) < 0 => rotateRightLeft
- LR：Z 是 X 的左儿子，BF(Z) > 0 => rotateLeftRight

下面展示各种情况下的 rotate 操作，以 RR 和 RL 为例（LL 和 LR 对称即可），素材源自 [Wikipedia](https://en.wikipedia.org/wiki/AVL_tree#Rebalancing)：

=== "RR: rotateLeft"

    !!! quote inline end ""

        ![](/assets/images/cs/algorithms/27.png)

    这种情况可能发生在：

    1. 插入操作导致 $t_4$ 高度增加 1
    2. 删除操作导致 $t_1$ 高度减少 1

    ```c
    node *rotateLeft(node *X, node *Z) {
        // rotate
        t23 = Z->left;
        X->right = t23;
        if (t23 != NULL) t23->parent = X;
        Z->left = X;
        X->parent = Z;
        // update BF
        if (BF(Z) == 0) {
            // 1st case, only happens with deletion
            BF(X) = +1;
            BF(Z) = -1;
        } else {
            // 2nd case
            BF(X) = 0;
            BF(Z) = 0;
        }
        return Z; // return new root of rotated subtree
    }
    ```

=== "RL: rotateRightLeft"

    !!! quote inline end ""

        ![](/assets/images/cs/algorithms/28.png)

    这种情况可能发生在：

    1. 插入 $Y$ 本身，或者插入操作导致其子树 $t_2$ 或 $t_3$ 高度增加 1
    2. 删除操作导致 $t_1$ 高度减少 1

    ```c
    node *rotateRightLeft(node *X, node *Z) {
        // rotate
        Y = Z->left;
        t3 = Y->right;
        Z->left = t3;
        if (t3 != NULL) t3->parent = Z;
        Y->right = Z;
        Z->parent = Y;
        t2 = Y->left;
        X->right = t2;
        if (t2 != NULL) t2->parent = X;
        Y->left = X;
        X->parent = Y;
        // update BF
        if (BF(Y) == 0) { // 1st case
            BF(X) = 0;
            BF(Z) = 0;
        } else if (BF(Y) > 0) { // 2nd case
            BF(X) = -1;
            BF(Z) = 0;
        } else { // 3nd case
            BF(X) = 0;
            BF(Z) = +1;
        }
        BF(Y) = 0;
        return Y; // return new root of rotated subtree
    }
    ```

### 插入与删除

AVL 树的插入和删除操作的思路都是首先进行插入和删除，然后自底向上回溯以更新 BF 值并维护平衡性，但其具体实现相当繁琐。结论是，插入后最多只需要一次 rotate 即可实现再平衡，而删除后可能需要多次 rotate 才能实现再平衡（最坏情况需要 $\log n$ 次）。

=== "插入操作"

    1. 用二叉搜索树的插入操作插入新节点
    2. 从新插入的节点开始向上回溯，并不断更新节点的 BF 值：
        - 如果 BF: $\pm 1$ -> 0，停止回溯
        - 如果 BF: 0 -> $\pm 1$，继续回溯
        - 如果 BF: $\pm 1$ -> $\pm 2$，执行相应的 rotate 操作，并停止回溯（原因：注意到这时候 rotate 返回的子树具有与插入前相同的高度，且其根节点 BF = 0）

    具体代码实现可以参考[这里](https://en.wikipedia.org/wiki/AVL_tree#Insert)。

=== "删除操作"

    1. 用二叉搜索树的删除操作删除目标节点
    2. 从被删除的节点开始向上回溯，并不断更新节点的 BF 值：
        - 如果 BF: 0 -> $\pm 1$，停止回溯
        - 如果 BF: $\pm 1$ -> 0，继续回溯
        - 如果 BF: $\pm 1$ -> $\pm 2$，执行相应的 rotate 操作，并视情况判断是否需要继续回溯（若 rotate 返回的子树具有与删除前相同的高度，则停止回溯；若 rotate 返回的子树高度比删除前小 1，则继续回溯）

    具体代码实现可以参考[这里](https://en.wikipedia.org/wiki/AVL_tree#Delete)。

## Splay&thinsp;树

- Splay 树通过将所有访问操作与所谓“展开”操作相结合，从而实现均摊对数时间复杂度
- Splay 树使得最近访问的节点被移动到根节点附近，从而可以快速再次访问，具有较好的访问局部性
- Splay 树不具备严格的自平衡能力，最坏情况下高度可能是线性的（例如以非降序访问所有节点后就会出现这种情况）
- Splay 树的搜索、插入和删除操作的摊还时间复杂度均为 $\Omicron(\log n)$

### 展开

展开操作分为以下三种情况，这里记待移动到根节点的节点为 X，记其父节点为 P，如果父节点的父节点存在，则记为 G；素材源自 [Wikipedia](https://en.wikipedia.org/wiki/Splay_tree#Splaying)：

=== "zig"

    !!! quote inline ""

        ![](/assets/images/cs/algorithms/29.png)

    当 G 不存在时（即 P 为根节点）执行此步骤。

    直接 rotate 即可，与 AVL 树的 rotateRight 操作是一致的。

=== "zig-zig"

    !!! quote inline ""

        ![](/assets/images/cs/algorithms/30.gif)

    当 G 存在且 X 和 P 都是左儿子或右儿子时执行此步骤。

    具体来说是首先对 P 和 G 进行 rotate，然后再对 X 和 P 进行 rotate；注意这里的顺序是重要的，如果反过来操作的话，则是一种由  [Allen & Munro](https://en.wikipedia.org/wiki/Splay_tree#cite_note-AllenMunro-5) 提出的 naive 的实现方式，会导致无法实现均摊对数时间复杂度。

=== "zig-zag"

    !!! quote inline ""

        ![](/assets/images/cs/algorithms/31.gif)

    当 G 存在且 X 和 P 一个是左儿子一个是右儿子时执行此步骤。

    进行两次 rotate，与 AVL 树的 rotateLeftRight 操作是一致的。

---

所有的访问操作（搜索、插入和删除）都会引发展开操作：

- 搜索：直接找到目标节点并对其进行展开即可
- 插入：用二叉搜索树的插入操作插入新节点，并对其进行展开
- 删除：用二叉搜索树的删除操作删除目标节点，并对其父节点进行展开
    - 或者，将目标节点展开到树的根节点位置，然后删除根节点并合并左右子树

### 摊还分析

摊还分析（amortized analysis）从空的结构开始，考虑可能出现的操作序列中的最差序列。

摊还分析是一种有用的工具，可以补充最差情况和平均情况分析：一方面摊还分析排除不可能出现的操作序列，因而不像简单粗暴的最差情况分析那么悲观；另一方面摊还分析比平均情况分析更加容易，毕竟后者涉及“如何定义平均情况”等概率问题。

摊还分析常见方法有以下三种：

- 聚合分析：寻找可能出现的操作序列中的最差序列并分析其成本 $T(n)$，直接用 $T(n)/n$ 表示每个操作的摊还成本
- 核算法：使用“截长补短”思想，为每一种操作估计摊还成本 $\hat{c _ i} = c _ i + \varDelta _ i$ 且 $\sum _ {i=1} ^ n \hat{c _ i} \geq \sum _ {i=1} ^ n c _ i$
- 势能法：使用“截长补短”思想，设计势能函数 $\varPhi (D _ i)$，为每一步操作估计摊还成本 $\hat{c _ i} = c _ i + (\varPhi (D _ i) - \varPhi (D _ {i-1}))$ 且 $\varPhi (D _ n) \geq \varPhi (D _ 0)$
    - Splay 树的势能法摊还分析可以参考[修佬的笔记](https://note.isshikih.top/cour_note/D2CX_AdvancedDataStructure/Lec01/#%E6%91%8A%E8%BF%98%E5%88%86%E6%9E%90)

## 红黑树

!!! quote inline end ""

    === "显式 NIL"

        ![](/assets/images/cs/algorithms/rd_with_nil.png)

    === "隐式 NIL"

        ![](/assets/images/cs/algorithms/rd_without_nil.png)

    叶节点 NIL 是红黑树特有的定义，用于辅助黑高平衡的定义。

- 红黑树是满足如下五条性质的二叉搜索树：
    1. 每个节点或者是红色的，或者是黑色的；
    2. 根节点是黑色的（optional）
    3. 叶节点（NIL）是黑色的；
    4. 红色节点的子节点都是黑色的；
    5. 对于每个节点，从该节点到其所有后代 NIL 的简单路径上，包含的黑色节点个数（称为该节点的黑高）相同
- 红黑树的高度至多为 $2\log (n+1)$，其中 $n$ 为节点总数（不含 NIL）
- 红黑树的搜索、插入和删除操作的时间复杂度均为 $\Omicron(\log n)$

### 插入

为了尽量不破坏平衡性质，每次插入的新节点默认染成红色。如果新节点插入空树则只需染黑即可，如果新节点插入在黑色节点下面则无需调整，而如果新节点插入在红色节点下面则有可能破坏平衡，具体可以分为以下三种情况：

!!! quote inline end ""

    === "1"

        <div style="text-align: center;">
        <img src="/assets/images/cs/algorithms/rd_ins_case1.png" style="width: 60%;">
        </div>

    === "2"

        <div style="text-align: center;">
        <img src="/assets/images/cs/algorithms/rd_ins_case2.png" style="width: 60%;">
        </div>

    === "3"

        <div style="text-align: center;">
        <img src="/assets/images/cs/algorithms/rd_ins_case3.png" style="width: 60%;">
        </div>

以新节点 N 插入在祖父 G 左侧为例（右侧对称即可），素材源自 [Wikipedia](https://en.wikipedia.org/wiki/Red%E2%80%93black_tree#Insertion)：

- case 1：N 的叔叔 U 是红色的，无论 N 是左儿子还是右儿子
    - 将父亲 P 和叔叔 U 染黑，并把祖父 G 染红
    - 此时祖父 G 有可能引发新的问题（如果 G 的父亲是红色的）并向上递归做调整
- case 2：N 的叔叔 U 是黑色的，且 N 是右儿子
    - 对 N 和父亲 P 进行 rotate，与 AVL 树的 rotateLeft 操作是一致的
    - 此时问题还没有解决，但是转变为 case 3
- case 3：N 的叔叔 U 是黑色的，且 N 是左儿子
    - 对 P 和祖父 G 进行 rotate，与 AVL 树的 rotateRight 操作是一致的
    - rotate 操作后交换 P 和 G 的颜色（事实上所有的 rotate 操作都伴随着颜色互换）

<div style="text-align: center;">
<img src="/assets/images/cs/algorithms/rd_ins_graph.png" style="width: 50%;">
</div>

### 删除

回顾二叉搜索树的[删除操作](https://note.minjoker.top/cs/algorithm/fds/note2/#_10)不难发现，第三种情况可以通过一步交换（注意这里只交换键值不交换颜色）直接转化为第一或第二种情况，因此我们只需要关注第一和第二种情况。第一种情况中替代被删除节点的是 NIL，第二种情况替代被删除节点的是其子节点。如果被删除的节点是红色的则无需调整，如果被删除的节点是黑色的且替代上来的节点是红色则只需染黑即可。

而如果被删除的节点是黑色的且替代上来的节点也是黑色的（注意 NIL 也是黑色的），则问题会变得相当棘手。比较巧妙的思路是将替代上来的黑色节点临时染成“双黑”，从而保证第五条性质不被破坏，然后我们要做的就是通过调整来把这个“双黑”节点多出来的一重黑色去除，从而保证第一条性质不被破坏。具体可以分为以下四种情况：

!!! quote inline end ""

    === "1"

        <div style="text-align: center;">
        <img src="/assets/images/cs/algorithms/rd_del_case1.png" style="width: 60%;">
        </div>

    === "2.1"

        <div style="text-align: center;">
        <img src="/assets/images/cs/algorithms/rd_del_case2_1.png" style="width: 60%;">
        </div>

    === "2.2"

        <div style="text-align: center;">
        <img src="/assets/images/cs/algorithms/rd_del_case2_2.png" style="width: 60%;">
        </div>

    === "3"

        <div style="text-align: center;">
        <img src="/assets/images/cs/algorithms/rd_del_case3.png" style="width: 60%;">
        </div>

    === "4"

        <div style="text-align: center;">
        <img src="/assets/images/cs/algorithms/rd_del_case4.png" style="width: 60%;">
        </div>

以被删除的节点 N 位于父亲 P 左侧为例（右侧对称即可），素材源自 [Wikipedia](https://en.wikipedia.org/wiki/Red%E2%80%93black_tree#Removal)：

- case 1：N 的兄弟 S 是红色的
    - 对 S 和父亲 P 进行 rotate，与 AVL 树的 rotateLeft 操作是一致的；并交换颜色
    - 此时“双黑”还没有被解决，转变为其他 case
- case 2：N 的兄弟 S 是黑色的，且 S 的两个儿子也都是黑色的
    - 问题可以根据 P 的颜色进一步细分成 2.1 和 2.2
    - 2.1 首先将 S 染红并将 N 去掉一重黑色，然后将 P 染黑，问题解决
    - 2.2 首先将 S 染红并将 N 去掉一重黑色，然后将 P 染成“双黑”，问题向上递归
- case 3：N 的兄弟 S 是黑色的，且近侄子 C 是红色的，远侄子 D 是黑色的
    - 对 C 和 S 进行 rotate，再对 C 和 P 进行 rotate，与 AVL 树的 rotateRightLeft 操作是一致的；并交换颜色
    - 此时“双黑”还没有被解决，转变为 case 4
- case 4：N 的兄弟 S 是黑色的，且远侄子 D 是红色的，近侄子随意
    - 对 S 和 P 进行 rotate，与 AVL 树的 rotateLeft 操作是一致的；并交换颜色
    - 然后将 N 去掉一重黑色，问题解决

<div style="text-align: center;">
<img src="/assets/images/cs/algorithms/rd_del_graph.png" style="width: 60%;">
</div>

### 比较&thinsp;AVL&thinsp;树与红黑树

- 总的来说 AVL 树是严格平衡的，而红黑树只是严格的黑高平衡，其中红色节点是少量不平衡的因素；除此之外，两者都是通过旋转实现再平衡，没有很大的差别；但有趣的是，很多库函数在选择平衡搜索树实现功能时会更常用红黑树（例如 C++ 的 std::map 和 Java 的 HashMap 等）
- AVL 树最差高度约为 $1.44 \log n$，红黑树最差高度约为 $2 \log n$，从而如果对一棵树的搜索操作居多，则 AVL 树可能会是更好的选择
- AVL 树虽然插入只需要常数次旋转，但在删除时可能需要 $\Omicron(\log n)$ 次旋转，而红黑树的插入和删除都最多只需要常数次旋转；很多人认为代码实现时旋转是插入和删除中开销最大的操作，从而如果对一棵树的插入和删除操作很多，AVL 树不如红黑树快
- AVL 树需要维护平衡因子，而红黑树只需要 1 bit 来表示红与黑，更节省空间
- 红黑树是可持久化的数据结构，因此在函数式编程中容易实现；而且红黑树支持分裂、合并等操作，这使得它可以做批量并行的插入、删除

## B+&thinsp;树

- $M$ 阶 B+ 树是满足如下性质的树：
    1. 根节点或者是叶子，或者有 2 到 $M$ 个儿子
    2. 非叶节点（根节点除外）索引 $\lceil M/2 \rceil$ 到 $M$ 个儿子
    3. 叶子节点（根节点除外）存储 $\lceil M/2 \rceil$ 到 $M$ 个数据
- 2-3-4 树（4 阶）和 2-3 树（3 阶）这种别称表达了节点可能含有的儿子个数
- 不同地方对 B+ 树的定义并不完全相同，这里给出一些版本（前两张来自这门课程的 PPT，第三章来自数据库教材 Database System Concepts 7th）

!!! quote ""

    === "2-3-4 tree"

        <div style="text-align: center;">
        <img src="/assets/images/cs/algorithms/ads_btree_1.png" style="width: 70%;">
        </div>

    === "2-3 tree"

        <div style="text-align: center;">
        <img src="/assets/images/cs/algorithms/ads_btree_2.png" style="width: 60%;">
        </div>

    === "db tree"

        <div style="text-align: center;">
        <img src="/assets/images/cs/algorithms/db_btree.png" style="width: 100%;">
        </div>

### 搜索、插入与删除

- 搜索：搜索方法是很显然的
    - 一方面，树的高度不超过 $\Omicron(\log _ {\lceil M/2 \rceil} N)$；另一方面，由于每层的键值是有序的，可以通过二分查找判断搜索方向，复杂度为 $\Omicron(\log _ 2 M)$；综上可得搜索的时间复杂度为 $\Omicron(\log _ {\lceil M/2 \rceil} N \cdot \log _ 2 M) = \Omicron(\log N)$
- 插入：插入方法也是很显然的，当节点溢出时，分裂节点并向上传递
    - 一方面，树的高度不超过 $\Omicron(\log _ {\lceil M/2 \rceil} N)$；另一方面，每层的操作最多是 $\Omicron(M)$ 的；综上可得插入的时间复杂度为 $\Omicron(\log _ {\lceil M/2 \rceil} N \cdot M) = \Omicron(\frac{M}{\log M} \log N)$
- 删除：当节点向下溢出时，尝试重新分配键值（与相邻的兄弟节点）或直接合并节点（当重新分配失败时）
    - 删除的时间复杂度分析与插入类似，也为 $\Omicron(\frac{M}{\log M} \log N)$

### 平衡二叉搜索树&thinsp;B+&thinsp;树

- B+ 树和 AVL 树、红黑树等平衡二叉搜索树的目的不完全一致：前者服务于数据库系统或文件系统，搜索存储在硬盘中的海量数据，而后者服务于一般的内存运算
    - B+ 树具有极小的树高（$M$ 很大时）从而磁盘寻路次数大幅减少，适合于以页为单位读写的存储系统
- 事实上红黑树的提出正是为了更方便的实现 B 树，我们学习的红黑树对应于 2-3-4 树，而 Sedgewick 提出的一种变体[左倾红黑树](https://note.minjoker.top/cs/algorithm/princeton/note1/#_36)则对应于 2-3 树


## 倒排索引

- 倒排索引是一种数据库索引，存储从内容到其在文档中位置的映射
- 倒排索引的目的是支持快速全文搜索，代价是将新文档添加进数据库时开销较大

---

倒排索引相关内容比较简单，这里仅列出一些值得注意的细节：

- 倒排索引中保存单词的出现频次是因为当搜索包含多个关键字时，从频次较少的词开始搜索会更快
- 倒排索引的构建有很多技巧，比如 stemming、stop words 等分词策略，访问单词时选择使用搜索树还是哈希，分布式外存选择 term-partitioned 还是
document-partitioned
- 搜索引擎的评价：
    - 区分 data retrieval 和 information retrieval，前者搜索的是结构化的数据，后者搜索的是非结构化的文章、网页等
    - 响应时间和索引空间都是评价这两种搜索的指标，而答案集的相关度则只针对后者
    - 答案集的相关度可以通过准确度、回调率等指标量化
