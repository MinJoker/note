# 题目集

<link rel="stylesheet" href="/stylesheets/classic_admonitions.css">

这篇笔记收集一些值得留意的题目，以及一些补充知识点，来源：

- 课程 PPT、随堂小测
- 教材 *Elements Of The Theory Of Computation*, 2nd Edition

## 判断语言是否正则

!!! note ""

    - 判断语言是正则的：
        - 能够写出正则表达式
        - 有限语言一定是正则语言
    - 判断语言是非正则的：
        - 泵定理反证法
        - 封闭运算反证法
    - 泵定理很容易混淆存在性与任意性：
        - 反证法中，pumping length 是任意的，不能取成确定值
        - 反证法中，字符串的选取只需一个反例，但其拆分是任意的，需要证明不管如何拆分，都能找到 $xy ^ iz\notin L$

1. $\lbrace ww ^ R: w\in \lbrace a,b \rbrace ^ \* \rbrace$

    !!! success ""

        标准的泵定理反证法：假设语言 $L=\lbrace ww^R: w\in \lbrace a,b \rbrace ^* \rbrace$ 是正则的，对任意的 $p\geq 1$，取字符串 $w=a ^ pbba ^ p$，考虑拆分 $w=xyz$，$|y|>0$，$|xy|\leq p$，则 $y=a^i$，$i\geq 1$，注意到 $x y ^ 2 z=a ^ {p+i}bba ^ p\notin L$，不满足泵定理，从而导出矛盾，因此 $L$ 是非正则的。

        值得注意的是，如果语言 $L$ 是正则的，则 $L ^ R$ 也是正则的，那么 $LL ^ R$ 也是正则的。注意区别 $LL ^ R$ 和上述的 $\lbrace ww ^ R: w\in \lbrace a,b \rbrace ^ \* \rbrace$，后者拼接时使用的 $w$ 是同一个字符串，这是隐含条件，逻辑上后者是前者的子集。

2. $\lbrace a ^ m b ^ n: m,n\in \N, m+n=2024 \rbrace$

    !!! success ""

        不要先入为主地认为是非正则的，而去尝试泵定理，稍加思考不难发现这个语言其实是有限的，所以是正则的。

3. $\lbrace a ^ m b ^ n: m,n\in \N, m \not \equiv n\mod 2024 \rbrace$

    !!! success ""

        尝试写出其正则表达式，该语言的含义是若干个 $a$ 拼上若干个 $b$，且两者数量模 $2024$ 不同余。注意到，所有可能的余数组合是有限的，我们可以直接用枚举来写正则表达式：$(a ^ {2024}) ^ \* (\bigcup _{i\not =j \;\text{and}\; i,j=0,1,\cdots,2023} a ^ i b ^ j) (b ^ {2024}) ^ \*$，所以该语言是正则的。

## 基于语言划分等价类

!!! note ""

    假设 $L \sube \Sigma ^ \*$ 是一个语言，$x,y\in L$，如果对于 $\forall z\in \Sigma ^ \*$，都有 $xz\in L \Leftrightarrow yz\in L$，则称 $x$ 和 $y$ 基于语言 $L$ 是等价的，记为 $x \approx _ L y$，我们可以通过这种方式，将 $\Sigma ^ \*$ 划分成若干个等价类。

1. 根据上述定义，将 $\Sigma ^ \* = \lbrace a,b \rbrace ^ \*$ 基于语言 $L = (ab \cup aab) ^ \*$ 划分等价类。

    !!! success ""

        答案：

        - $[e] = L$
        - $[a] = La$
        - $[aa] = Laa$
        - $[aaa] = [bb] = \Sigma ^ \* (aaa \cup bb) \Sigma ^ \*$

        具体解答过程：

        - $[e] = \lbrace y\in \Sigma ^ \*: \forall z\in \Sigma ^ \*, z\in L \Leftrightarrow yz\in L \rbrace = L$
            - 注意到 $L$ 的一个重要运算是 star，不难发现 $yz\in L \Leftrightarrow y\in L$
        - $[a] = \lbrace y\in \Sigma ^ \*: \forall z\in \Sigma ^ \*, az\in L \Leftrightarrow yz\in L \rbrace = La$
            - 注意到 $az\in L \Leftrightarrow z\in bL$ 或 $z\in aL$，即 $z$ 以 $a$ 或 $b$ 开头
                - 如果 $z\in bL$，那么当 $yz\in L$ 时我们有 $y\in La$ 或 $y\in Laa$，即 $y$ 以 $a$ 或 $aa$ 结尾，经检验 $y\in La$ 满足，而 $y\in Laa$ 不满足
                - 如果 $z\in aL$，需要 $y\in La$ 才能满足条件
            - 综上，$yz\in L \Leftrightarrow y\in La$
        - $[aa] = \lbrace y\in \Sigma ^ \*: \forall z\in \Sigma ^ \*, aaz\in L \Leftrightarrow yz\in L \rbrace = Laa$
            - 注意到 $azz\in L \Leftrightarrow z\in bL$，即 $z$ 以 $b$ 开头，那么当 $yz\in L$ 时我们有 $y\in La$ 或 $y\in Laa$，即 $y$ 以 $a$ 或 $aa$ 结尾，经检验 $y\in Laa$ 满足，而 $y\in La$ 不满足
        - 最后，注意到 $aaa\notin L$，$bb\notin L$，而且 $L$ 中的所有字符串都不包含 $aaa$ 或 $bb$
            - 此时，$aaaz\in L$ 和 $bbz\in L$ 对所有 $z$ 均不成立，我们需要找到 $y$ 使得 $yz\in L$ 也对所有 $z$ 均不成立，不难发现 $y\in \Sigma ^ \* (aaa \cup bb) \Sigma ^ \*$

2. 根据上述定义，将 $\Sigma ^ \* = \lbrace a,b \rbrace ^ \*$ 基于语言 $L = (ab \cup ba) ^ \*$ 划分等价类。

    !!! success ""

        答案：

        - $[e] = L$
        - $[a] = La$
        - $[b] = Lb$
        - $[aa] = \Sigma ^ \* (aa \cup bb) \Sigma ^ \*$

        具体解答过程同理于上一题。