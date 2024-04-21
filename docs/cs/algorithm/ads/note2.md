# 算法分析

## 回溯

- 回溯法用于寻找某些计算问题（尤其是约束满足问题）的解决方案，是一种更高效的暴力枚举
- 回溯法在隐式的搜索树上执行深度优先搜索，对每个节点进行评估，当没有必要继续向下搜索时果断放弃该节点及其子树（剪枝）
- 回溯法遍历的实际树只是整棵搜索树的一部分，回溯法的效率与数据规模、剪枝策略、实际树的大小与形态有关，复杂度分析通常较难完成

---

回溯法有许多经典案例，比如八皇后问题和下面用伪代码展示的收费公路重建问题：

=== "回溯法模版"

    ```c linenums="1"
    bool backtracking(int i) {
        bool found = false;
        if (i > N) return true; // solved with X[1]...X[N]
        for (each X[i] in S[i]) {
            // check if satisfies the restriction R
            ok = check(X, R); // pruning
            if (ok) {
                count X[i] in;
                found = backtracking(i+1);
                if (!found) undo(i); // backtrack to X[1]...X[i-1]
            }
            if (found) break;
        }
        return found;
    }
    ```

=== "收费公路重建问题"

    ```c linenums="1"
    bool reconstruct(DistType[] X, DistSet D, int N, int left, int right) {
        /* X[1]...X[left-1] and X[right+1]...X[N] are solved */
        bool found = false;
        if (isEmpty(D)) return true;
        maxD = findMax(D);
        /* option 1: X[right] = maxD */
        ok = check(maxD, N, left, right); // check if every |maxD-X[i]| is in D for all X[i]'s that have been solved
        if (ok) { // add X[right] and update D
            X[right] = maxD;
            for (i = 1; i < left; i++)     delete(|X[right]-X[i]|, D);
            for (i = right+1; i <= N; i++) delete(|X[right]-X[i]|, D);
            found = reconstruct(X, D, N, left, right-1);
            if (!found) { // if does not work, backtrack
                for (i = 1; i < left; i++)     insert(|X[right]-X[i]|, D);
                for (i = right+1; i <= N; i++) insert(|X[right]-X[i]|, D);
            }
        }
        /* option 2: X[left] = X[N] - maxD */
        if (!found) { // if option 1 does not work
            ok = check(X[N]-maxD, N, left, right); // check if every |(X[N]-maxD)-X[i]| is in D for all X[i]'s that have been solved
            if (ok) { // add X[left] and update D
                X[left] = X[N] - maxD;
                for (i = 1; i < left; i++)     delete(|X[left]-X[i]|, D);
                for (i = right+1; i <= N; i++) delete(|X[left]-X[i]|, D);
                found = reconstruct(X, D, N, left+1, right);
                if (!found) { // if does not work, backtrack
                    for (i = 1; i < left; i++)     insert(|X[left]-X[i]|, D);
                    for (i = right+1; i <= N; i++) insert(|X[left]-X[i]|, D);
                }
            }
        }
        return found;
    }
    ```

### $\alpha$-$\beta$&thinsp;剪枝

!!! quote inline end ""

    ![](/assets/images/cs/algorithms/abpruning.png)

- $\alpha$-$\beta$ 剪枝是一种对抗性搜索算法，常用于双人对弈游戏
- 右图的博弈树可以代表一个双人零和游戏，每个节点都对应一个效用函数
- $\alpha$ 剪枝就是最大化玩家总是要最大化对手最小化的效用，$\beta$ 剪枝就是最小化玩家总是要最小化对手最大化的效用

## 分治

## 动态规划
