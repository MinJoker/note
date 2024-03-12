# 高级数据结构

## AVL&thinsp;树

Take-Home Message:

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

=== "插入操作概述"

    1. 用二叉搜索树的插入操作插入新节点
    2. 从新插入的节点开始向上回溯，并不断更新节点的 BF 值：
        - 如果 BF: $\pm 1$ -> 0，停止回溯
        - 如果 BF: 0 -> $\pm 1$，继续回溯
        - 如果 BF: $\pm 1$ -> $\pm 2$，执行相应的 rotate 操作，并停止回溯（原因：注意到这时候 rotate 返回的子树具有与插入前相同的高度，且其根节点 BF = 0）

    具体代码实现可以参考[这里](https://en.wikipedia.org/wiki/AVL_tree#Insert)。

=== "删除操作概述"

    1. 用二叉搜索树的删除操作删除目标节点
    2. 从被删除的节点开始向上回溯，并不断更新节点的 BF 值：
        - 如果 BF: 0 -> $\pm 1$，停止回溯
        - 如果 BF: $\pm 1$ -> 0，继续回溯
        - 如果 BF: $\pm 1$ -> $\pm 2$，执行相应的 rotate 操作，并视情况判断是否需要继续回溯（若 rotate 返回的子树具有与删除前相同的高度，则停止回溯；若 rotate 返回的子树高度比删除前小 1，则继续回溯）

    具体代码实现可以参考[这里](https://en.wikipedia.org/wiki/AVL_tree#Delete)。
