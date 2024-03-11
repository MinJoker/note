# 课程笔记

## 信息、熵与计算复杂度

### 香农熵

消息的信息量取决于消息内容令人惊讶的程度，被定义为一个随着事件发生概率 $p$ 减小而增大的函数 $I(p)$。

> 可以这样去理解信息量与概率的关系：知道某个特定数字不会成为彩票中奖号码所提供的信息量非常少，因为任何选定的数字几乎都不会中奖；但是知道某个特定数字会中奖所提供的信息量则是巨大的，因为它传递了极低概率事件的结果。

信息量函数 $I(p)$ 满足如下性质：

- 连续性：$I(p)$ 是关于 $p$ 的连续函数
- 可加性：$I(p_x, p_y) = I(p_x) + I(p_y)$ 对于独立事件 $x, y$ 均成立

根据这两条性质可以推导出：

- 由可加性，对于任意实数 $r$，有 $I(p_x ^ r) = rI(p_x)$
- 进一步地，由连续性，有 $I(p_x) = I((1/2) ^ {-\log _2 (p_x)}) = -\log _2 (p_x) I(1/2)$

常数 $I(1/2)$ 可以被视作信息量的单位，从而可以定义[香农熵](https://en.wikipedia.org/wiki/Entropy_(information_theory))：

- 发生概率为 $p_x$ 的事件 $x$ 的香农熵为 $-\log _2 (p_x)$
- 一系列发生概率分别为 $p_1, p_2, ..., p_n$ 的事件的平均信息量为 $H \sim -\sum _{i=1} ^ n p_i \log _2 p_i$

---

香农熵的一个重要应用是[香农源编码理论](https://en.wikipedia.org/wiki/Entropy_(information_theory))：对于源为独立同分布随机变量的数据，香农熵提供了一个数据压缩的下界。具体而言，当数据量趋于无穷时，不可能压缩这些数据以使得码率（每个符号的平均 bit 位数）小于源的香农熵 $H$。

### 计算复杂度

- P：代表所有可在多项式时间内解决的决策问题，这类问题往往是易于解决的
- NP：代表所有可在多项式时间内验证的决策问题，即对于任一给定的猜测，都可以在多项式时间内判断猜测是否正确
    - 显然 P $\sube$ NP，而著名的 P = NP? 问题至今未被解决
    - NP 完全问题是 NP 中最难的一些问题，在多项式时间内解决此类问题的算法也能在多项式时间内解决任何其他 NP 问题
- BPP：如果存在满足以下性质的算法，则决策问题属于 BPP
    - 允许抛硬币并做出随机决定；保证在多项式时间内运行；对于算法的任何一次运行，给出错误答案的概率不超过 1/3
    - 显然 P $\sube$ BPP，随着越来越多 BPP 问题被证明属于 P，人们推测 P = BPP（去随机化）
- BQP：代表所有可由量子计算机在多项式时间内以高概率解决的决策问题，可以视作 BPP 问题的量子推广
    - 有 BPP $\sube$ BQP，而 BPP = BQP? 是未知的

## 复向量空间下的光子及其量子态

### 狄拉克记号

[狄拉克记号](https://en.wikipedia.org/wiki/Bra%E2%80%93ket_notation)广泛应用于量子信息领域，其基本记法如下：

- 复向量空间 $V$ 中的向量记为 $\ket{v}$，线性泛函 $f: V \rightarrow \Complex$ 记为 $\bra{f}$，线性泛函作用于向量记为 $\braket{f|v} \in \Complex$
- 复向量内积空间 $V$ 中有内积 $(\cdot , \cdot)$ 且第一位是共轭线性的，对于向量 $\phi$ 可以定义 $(\phi , \cdot) \equiv \bra{\phi}$，其中线性泛函 $\bra{\phi}$ 是向量 $\ket{\phi}$ 的伴随（矩阵形式的共轭转置），从而内积记为 $(\phi , \psi) \equiv \braket{\phi | \psi}$
    - 内积 $\braket{\phi | \psi}$ 表示 $\ket{\psi}$ 投影到 $\ket{\phi}$ 上，注意 $\braket{\phi | \psi} = \braket{\psi | \phi} ^ *$
- 线性泛函 $\bra{\phi}$ 和向量 $\ket{\psi}$ 可以通过外积结合成算子 $\ket{\psi} \bra{\phi}$，定义为 $\ket{\psi} \bra{\phi} : \ket{\xi} \mapsto \ket{\psi} \braket{\phi | \xi}$
    - 外积 $P_k \equiv \ket{k} \bra{k}$ 表示 $\ket{k}$ 方向的投影算子，$P_k \ket{v} = \ket{k} \braket{k | v}$

从矩阵形式思考，狄拉克记号下线性泛函与向量的结合遵循矩阵乘法：

- $\braket{\phi | \psi} \doteq \begin{pmatrix} \phi _1 ^ * & \phi _2 ^ * & \cdots & \phi _N ^ * \end{pmatrix} \begin{pmatrix} \psi _1 \cr \psi _2 \cr \vdots \cr \psi _N \end{pmatrix} = \sum _ {i=1} ^ N \phi _i ^ * \psi _i$
- $\ket{\phi} \bra{\psi} \doteq \begin{pmatrix} \phi _1 \cr \phi _2 \cr \vdots \cr \phi _N \end{pmatrix} \begin{pmatrix} \psi _1 ^ * & \psi _2 ^ * & \cdots & \psi _N ^ * \end{pmatrix} = \begin{pmatrix} \phi _1 \psi _1 ^ * & \phi _1 \psi _2 ^ * & \cdots & \phi _1 \psi _N ^ * \cr \phi _2 \psi _1 ^ * & \phi _2 \psi _2 ^ * & \cdots & \phi _2 \psi _N ^ * \cr \vdots & \vdots & \ddots & \vdots \cr \phi _N \psi _1 ^ * & \phi _N \psi _2 ^ * & \cdots & \phi _N \psi _N ^ * \end{pmatrix}$

### 光的偏振

光的偏振是二维复向量空间的一个范例，或者说光的偏振是量子比特 qubit 的一个范例，可以用一个复向量来表示一个偏振光。

- 线偏振光：$\begin{cases} E_x = E_0 \cos \theta \cos \omega t \cr E_y = E_0 \sin \theta \cos \omega t \end{cases}$
    - 可以用单位复向量 $\ket{p} = (\cos \theta , \sin \theta) ^ T$ 表示这个线偏振光
    - [Malus 定律](https://en.wikipedia.org/wiki/Polarizer#Malus's_law_and_other_properties) $I ^ {\prime} = I \cos ^ 2 \theta$ 的统计学描述：偏振方向为 $\ket{p} = (\cos \theta , \sin \theta) ^ T$ 的 $N$ 个光子透过偏振方向为 $\ket{n} = (1, 0) ^ T$ 的偏振片，透过率约为 $N_x / N \simeq \cos ^ 2 \theta = \braket{p | n}$
- 椭圆偏振光：$\begin{cases} E_x = E_0 \cos \theta \cos (\omega t - \delta _x) = E_0 \text{Re} (\mu e ^ {-i\omega t}) & ,\; \mu = \cos \theta e ^ {i\delta _x} \cr E_y = E_0 \sin \theta \cos (\omega t - \delta _y) = E_0 \text{Re} (\nu e ^ {-i\omega t}) & ,\; \nu = \sin \theta e ^ {i\delta _y} \end{cases}$
    - 可以用单位复向量 $\ket{p} = (\mu, \nu) ^ T$ 表示这个椭圆偏振光，由于物理上相位差 $\delta = \delta _y - \delta _x$ 才是有意义的，因此 $\ket{p}$ 可由两个实参数 $\theta , \delta$ 唯一确定
    - 圆偏振光的 $\theta = \pi / 4$ 且 $\delta = \pm \pi / 2$，可以用单位复向量 $\frac{1}{\sqrt{2}} (1 , \pm i) ^ T$ 表示

---

下面考虑用狄拉克记号表达光的偏振（以线偏振光为例）：

- 引入正交基 $\ket{h} = \begin{pmatrix} 1 \cr 0 \end{pmatrix} , \ket{v} = \begin{pmatrix} 0 \cr 1 \end{pmatrix}$，以及相应的投影算子 $P_h = \ket{h} \bra{h} = \begin{pmatrix} 1 & 0 \cr 0 & 0 \end{pmatrix} , P_v = \ket{v} \bra{v} = \begin{pmatrix} 0 & 0 \cr 0 & 1 \end{pmatrix}$，注意到 $P_h + P_v = I _ {2\times 2}$
- 从而线偏振光可以表示为 $\ket{p} = I _ {2\times 2} \ket{p} = \ket{h} \bra{h} \ket{p} + \ket{v} \bra{v} \ket{p} = \mu \ket{h} + \nu \ket{v}$，注意到 $|\mu| ^ 2 + |\nu| ^ 2 = 1$
    - $\mu = \braket{h | p} , \nu = \braket{v | p}$ 称为[概率幅](https://en.wikipedia.org/wiki/Probability_amplitude)，分别反映了量子态 $\ket{p}$ 坍缩到 $\ket{h}$ 和 $\ket{v}$ 的可能性
    - 概率幅的模平方表示概率，例如 $\text{P} _ h = |\mu| ^ 2 = \braket{p | h} \braket{h | p} \equiv \braket{p | P _ h | p}$，其中 $\text{P}_h$ 表示概率而 $P_h$ 表示 $\ket{h}$ 对应的投影算子
