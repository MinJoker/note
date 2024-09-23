# 自动机理论

## 前言

### 问题

- 四类问题：优化问题、搜索问题、判定问题、计数问题
    - 其中，判定问题（decision problem）是最简单的
- 如何证明一个问题是难的，是计算理论的一个核心问题
    - 如果能证明判定问题是难的，那么相应的其他问题也就都是难的，所以本门课程主要关注判定问题
- 判定问题的答案分为 yes-instance 和 no-instance
- 判定问题的定义是：给定一个字符串 $w$，判断 $w$ 是否属于集合 {encoding of yes-instance}

### 语言

- 字符集（alphabet）是一个有限的符号集合，字符串（string）是字符集上的有限的序列（sequence）
    - 字符集和字符串都可以是空的，空串用保留字 $e$ 表示
    - 字符串的长度用 $|w|$ 表示，$|e|=0$
    - 字符集 $\Sigma$ 上所有长度为 $i$ 的字符串的集合记为 $\Sigma ^ i$
    - 字符串 $\Sigma$ 上所有字符串的集合记为 $\Sigma ^ * = \bigcup _ {i\geq 0} \Sigma ^ i$，所有非空字符串的集合记为 $\Sigma ^ + = \bigcup _ {i\geq 1} \Sigma ^ i$
    - 字符串操作
        - concatenation：$|w _ 1 w _ 2| = |w _ 1| + |w _ 2|$
        - exponentiation：$|w ^ i| = i|w|$
        - reversal：$|w ^ R| = |w|$
- 语言（language）over $\Sigma$ 的定义是：任意 $\Sigma ^ *$ 的子集
- 判定问题和语言是等价的
    - 判定问题 $\Rightarrow$ 语言：给定一个判定问题，可以用其 {encoding of yes-instance} 定义一个语言
    - 判定问题 $\Leftarrow$ 语言：给定一个语言 $L$，可以定义一个判定问题为字符串 $w$ 是否属于集合 $L$

## 有限自动机

### DFA

<div style="text-align: center;">
<img src="/assets/images/cs/toc/dfa.png" style="width: 40%;">
</div>

- 如图是一个确定性有限自动机（deterministic finite automata）
    - 其中，初始状态是唯一的，用单箭头指入表示，如 $q_0$；终止状态是若干的，用双圈表示，如 $q_2$
- DFA 的定义：$M = (K, \Sigma, \delta, s, F)$
    - $K$ 为有限的状态集合，$\Sigma$ 为字符集，$s$ 为初始状态，$F$ 为终止状态集合
    - $\delta$ 为转移函数，$\delta: K \times \Sigma \to K$
- 对于如图所示 DFA，考虑输入 $1010$，有 $(q _ 0, 1010) \vdash _ M (q _ 1, 010) \vdash _ M (q _ 2, 10) \vdash _ M (q _ 1, 0) \vdash _ M (q _ 2, e)$
    - 其中，记 configuration 为 $(q, w)$，表示当前状态 $q$ 和 剩余输入字符串 $w$
    - 其中，记 yield 为 $\vdash _ M$，表示单步转移，若 $w = aw ^ {\prime}$，$a \in \Sigma$ 且 $\delta(q, a)=q ^ {\prime}$，则 $(q, w) \vdash _ M (q ^ {\prime}, w ^ {\prime})$
    - 此外，可以用 $\vdash _ M ^ *$ 表示若干次的单步转移
- 接受逻辑
    - 自动机 $M$ 接受（accept）字符串 $w \in \Sigma ^ *$：$(s, w) \vdash _ M ^ * (q, e)$ 且 $q \in F$
    - 自动机 $M$ 接受（accept）语言 $L$：$M$ 接受所有属于 $L$ 的字符串，拒绝所有不属于 $L$ 的字符串
    - 注意，自动机 $M$ 接受的语言有且仅有一个，称为 the language of $M$，记为 $L(M) = \lbrace w \in \Sigma ^ *: M \; \text{accepts} \; w \rbrace$

### NFA

<div style="text-align: center;">
<img src="/assets/images/cs/toc/nfa.png" style="width: 40%;">
</div>

- 如图是一个非确定性有限自动机（non-deterministic finite automata），其与 DFA 的区别在于：
    - 一个状态在同一条件下可以有多种转移方案
    - 可以存在不消耗输入字符串的转移，即 $e$-transition
- NFA 的定义：$M = (K, \Sigma, \Delta, s, F)$
    - 其与 DFA 的区别在于转移方程不用函数描述，而是用更一般的关系（relation）描述
    - $\Delta$ 为转移关系，$\Delta \sube K \times (\Sigma \cup \lbrace e \rbrace) \times K$
- 接受逻辑与 DFA 同理
    - 注意，对于一个输入，NFA 可能存在多种路线，但只要其中一条路线被接受，就认为 NFA 接受该输入
    - 理解一：并行计算，出现多种转移方案时拷贝进程，只要其中一条进程走通，则程序走通
    - 理解二：NFA always makes the right guess
- 对于上图所示 NFA，考虑输入 $abb$，转移过程可以用树状的图来描述，如下图所示

<div style="text-align: center;">
<img src="/assets/images/cs/toc/nfa_tree.png" style="width: 40%;">
</div>

- DFA 和 NFA 是等价的
    - 对于任意 DFA $M$，存在 NFA $M ^ {\prime}$，使得 $L(M)=L(M ^ {\prime})$
    - 对于任意 NFA $M$，存在 DFA $M ^ {\prime}$，使得 $L(M)=L(M ^ {\prime})$
- 证明两者可以相互转化
    - DFA 转化为 NFA 是显然的，DFA 可以直接视作一个特殊的 NFA
    - NFA 转化为 DFA 的思路是：用 DFA 去模拟 NFA 的 tree-like 转移过程，将树的同一层状态整体视作 DFA 的一个状态
- NFA $M=(K, \Sigma, \Delta, s, F)$ 转化为 DFA $M ^ {\prime} = (K ^ {\prime} , \Sigma, \delta, s ^ {\prime} , F ^ {\prime})$
    - $K ^ {\prime} = 2 ^ K = \lbrace Q: Q \sube K \rbrace$
    - $F ^ {\prime} = \lbrace Q \sube K ^ {\prime} : Q \cap F \neq \varnothing \rbrace$
    - $s ^ {\prime} = E(s)$，其中 $E(s)=\lbrace p \in K: (s, e) \vdash _ M (p, e) \rbrace$ 表示 $s$ 通过 $e$-transition 可达的状态集合
    - $\forall Q \in K ^ {\prime}$，$\forall a \in \Sigma$，有 $\delta(Q, a) = \bigcup _ {q \in Q} \bigcup _ {p: (q, a, p)\in \Delta} E(p)$
- 严格证明两者是等价的
    - 先证明 $\forall p, q \in K$，$\forall w \in \Sigma ^ *$，有 $(p, w) \vdash _ M ^ * (q, e) \Leftrightarrow (E(p), w) \vdash _ {M ^ {\prime}} ^ * (Q, e) \;\text{for some}\; Q \ni q$
    - 再对 $|w|$ 使用数学归纳法，把 $p$ 固定到初始状态 $s$，从而根据自动机接受字符串的任意性得证
- 下图给出一个 NFA 转化为 DFA 的例子，注意到转化而来的 DFA 下半部分是冗余的，可以舍去

<div style="text-align: center;">
<img src="/assets/images/cs/toc/nfa2dfa.png" style="width: 60%;">
</div>

## 正则

### 正则语言

- 能够被某一有限自动机接受的语言是正则的（regular）
- 正则语言对于下列运算是封闭的，即正则语言的运算结果仍然是正则语言
    - union：$A \cup B = \lbrace w: w\in A \;\text{or}\; w\in B \rbrace$
    - concatenation：$A \circ B = \lbrace ab: a\in A \;\text{and}\; b\in B \rbrace$
    - star：$A ^ * = \lbrace w _ 1 w _ 2 \cdots w _ k : k \geq 0 \;\text{and each}\; w _ i \in A \rbrace$
- 证明正则运算的封闭性
    - union 的思路是并行化，做笛卡尔积
        - $K = K _ A \times K _ B$，$s = (s _ A, s _ B)$，$F = \lbrace (q _ A, q _ B): q _ A \in F _ A \;\text{or}\; q _ B \in F _ B \rbrace$
        - $\forall q _ A \in K _ A$，$\forall q _ B \in K _ B$，$\forall a \in \Sigma$，有 $\delta ((q _ A, q _ B), a) = (\delta (q _ A, a), \delta (q _ B, a))$
    - concatenation 的思路是串行化，用 NFA 的方式来做连接
        - $K = K _ A \cup K _ B$，$s = s _ A$，$F = F _ B$
        - $\Delta = \Delta _ A \cup \Delta _ B \cup \lbrace (q,e,s _ B): q\in F _ A \rbrace$
    - star 的思路是循环自身，将终止状态通过 $e$-transition 连接到初始状态，并考虑空串这一特殊情况，如下图所示
        - $K = K _ A \cup \lbrace s \rbrace$，$F = F _ A \cup \lbrace s \rbrace$
        - $\Delta = \Delta _ A \cup \lbrace (s,e,s _ A) \rbrace \cup \lbrace (q,e,s _ A): q\in K _ A \rbrace$

<div style="text-align: center;">
<img src="/assets/images/cs/toc/regular_star.png" style="width: 35%;">
</div>