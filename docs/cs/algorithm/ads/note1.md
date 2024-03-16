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

    !!! note inline end ""

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

    !!! note inline end ""

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

    !!! note inline ""

        ![](/assets/images/cs/algorithms/29.png)

    当 G 不存在时（即 P 为根节点）执行此步骤。

    直接 rotate 即可，与 AVL 树的 rotateRight 操作是一致的。

=== "zig-zig"

    !!! note inline ""

        ![](/assets/images/cs/algorithms/30.gif)

    当 G 存在且 X 和 P 都是左儿子或右儿子时执行此步骤。

    具体来说是首先对 P 和 G 进行 rotate，然后再对 X 和 P 进行 rotate；注意这里的顺序是重要的，如果反过来操作的话，则是一种由  [Allen & Munro](https://en.wikipedia.org/wiki/Splay_tree#cite_note-AllenMunro-5) 提出的 naive 的实现方式，会导致无法实现均摊对数时间复杂度。

=== "zig-zag"

    !!! note inline ""

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
