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

光的偏振是二维复向量空间的一个范例，或者说光的偏振是量子比特 qubit 的一个范例，可以用一个二维复向量来表示一个偏振光。

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

## 自旋、量子比特与线性算子

### 自旋与线性算子

孤立的 [1/2 自旋](https://en.wikipedia.org/wiki/Spin-1/2)是量子比特 qubit 的一个范例，可以用一个二维复向量来表示一个 1/2 自旋状态。

- 引入正交基 $\ket{0} \equiv \ket{\uparrow} = \begin{pmatrix} 1 \cr 0 \end{pmatrix} , \ket{1} \equiv \ket{\downarrow} = \begin{pmatrix} 0 \cr 1 \end{pmatrix}$
- 任意自旋状态都可以表示为 $\ket{\uparrow}$ 和 $\ket{\downarrow}$ 这两种自旋状态的叠加 $\ket{\psi} = \alpha \ket{\uparrow} + \beta \ket{\downarrow}$ 且 $|\alpha| ^ 2 + |\beta| ^ 2 = 1$

对系统的自旋状态的观测用一个[厄米的（自伴的）](https://en.wikipedia.org/wiki/Self-adjoint_operator)线性算子表示。

- 考虑其特征方程 $M \ket{\lambda} = \lambda \ket{\lambda}$，每个特征值 $\lambda$ 都是实的
- 考虑其特征分解 $M = \sum _ {\lambda \lambda ^ \prime} \ket{\lambda} \bra{\lambda} M \ket{\lambda ^ \prime} \bra{\lambda ^ \prime} = \sum _ {\lambda} \lambda \ket{\lambda} \bra{\lambda} = \sum _{\lambda} \lambda P _ {\lambda}$
- 对于自旋状态 $\ket{\psi}$ 和观测 $M$，观测到特征向量 $\ket{\lambda}$ 表示的自旋状态的概率为 $\text{P} _ {\lambda} = \braket{\psi | \lambda} \braket{\lambda | \psi}$
- 观测是不可逆的，经过观测的系统会坍缩到其一特征向量表示的自旋状态（经典的 [Stern-Gerlach 实验](https://en.wikipedia.org/wiki/Stern%E2%80%93Gerlach_experiment)反映了观测如何影响 1/2 系统的自旋状态）；我们无法预测系统会坍缩到哪种状态，但我们能分析系统坍缩到这些状态的概率

<div style="text-align: center;">
<img src="/assets/images/cs/information/qi/2.jpg" style="width: 40%;">
</div>

### 泡利矩阵

[泡利矩阵](https://en.wikipedia.org/wiki/Pauli_matrices)是三个厄米的 $2 \times 2$ 复向量矩阵，它们本身即可表示三种观测；更为重要的是，它们的线性组合可以构造出沿着任意自旋方向的观测。

三个泡利矩阵及其特征向量如下（注意它们的特征值都是 $\pm 1$，分别对应两种正交的自旋状态）：

- $\sigma _ z = \begin{pmatrix} 1 & 0 \cr 0 & -1 \end{pmatrix}$，$+1$ 对应 $\ket{0}$，$-1$ 对应 $\ket{1}$
- $\sigma _ x = \begin{pmatrix} 0 & 1 \cr 1 & 0 \end{pmatrix}$，$+1$ 对应 $\ket{r} = \frac{1}{\sqrt{2}} \ket{0} + \frac{1}{\sqrt{2}} \ket{1}$，$-1$ 对应 $\ket{l} = \frac{1}{\sqrt{2}} \ket{0} - \frac{1}{\sqrt{2}} \ket{1}$
- $\sigma _ y = \begin{pmatrix} 0 & -i \cr i & 0 \end{pmatrix}$，$+1$ 对应 $\ket{i} = \frac{1}{\sqrt{2}} \ket{0} + \frac{i}{\sqrt{2}} \ket{1}$，$-1$ 对应 $\ket{o} = \frac{1}{\sqrt{2}} \ket{0} - \frac{i}{\sqrt{2}} \ket{1}$

下面展示如何通过泡利矩阵构造出沿着任意自旋方向的观测矩阵：

- 在现实中，自旋方向是由一个三维实向量表示的；而在量子力学中，自旋状态是由一个二维复向量表示的
- 定义泡利向量 $\vec{\sigma} = \sigma _ x \hat{x} + \sigma _ y \hat{y} + \sigma _ z \hat{z}$，对于自旋方向 $\hat{n} = (n_x, n_y, n_z)$，沿着这个方向的观测可以表示成矩阵 $\sigma _ n = \vec{\sigma} \cdot \hat{n} = \sigma _ x n _ x + \sigma _ y n _ y + \sigma _ z n _ z = \begin{pmatrix} n _ z & n _ x - i n _ y \cr n _ x + i n _ y & - n _ z \end{pmatrix}$，注意到这个矩阵的特征值也为 $\pm 1$，分别对应沿着这个自旋方向的两种正交的自旋状态
- 借助泡利向量，我们可以根据任意由三维实向量表示的自旋方向来构造相应的由二维复厄米矩阵表示的观测

### Bloch&thinsp;球

[Bloch 球](https://en.wikipedia.org/wiki/Bloch_sphere)是 qubit 的一种几何表示：

- Bloch 球表面上每一个点都表示一种量子状态（自旋状态）
- Bloch 球中每条穿过球心的轴的两极都表示一对正交的量子状态
- Bloch 球的 $x,y,z$ 轴分别表示三个泡利矩阵对应的正交量子状态
- 量子状态记为 $\ket{\psi} = \cos (\theta / 2) \ket{0} + e ^ {i \phi} \sin (\theta / 2) \ket{1}$

<div style="text-align: center;">
<img src="/assets/images/cs/information/qi/1.jpg" style="width: 40%;">
</div>

关于“为什么 Bloch 球要这样定义”，下面给出一种解释：

首先取基状态 $\ket{0}$ 和 $\ket{1}$，任意量子状态可以表示为基状态的叠加 $\ket{\psi} = \alpha \ket{0} + \beta \ket{1}$，其中 $|\alpha| ^ 2 + |\beta| ^ 2 = 1$；然后考虑以这个量子状态及其正交态 $\ket{\bar{\psi}} = \beta ^ * \ket{0} - \alpha ^ * \ket{1}$ 作为特征向量，利用特征分解构造一个观测：

$$
M = \sum _ {\lambda = \pm 1} \lambda P _ \lambda = \ket{\psi} \bra{\psi} - \ket{\bar{\psi}} \bra{\bar{\psi}} = \begin{pmatrix} |\alpha| ^ 2 - |\beta| ^ 2 & 2\alpha \beta ^ * \cr 2\alpha ^ * \beta & |\beta| ^ 2 - |\alpha| ^ 2 \end{pmatrix}
$$

注意到沿着自旋方向 $\hat{n} = (n_x, n_y, n_z)$ 构造的观测可以表示成矩阵 $\sigma _ n = \vec{\sigma} \cdot \hat{n} = \sigma _ x n _ x + \sigma _ y n _ y + \sigma _ z n _ z = \begin{pmatrix} n _ z & n _ x - i n _ y \cr n _ x + i n _ y & - n _ z \end{pmatrix}$；从而我们可以找到观测 $M$ 对应的自旋方向 $\hat{\psi} = (\psi _ x, \psi _ y, \psi _ z)$，其中 $\psi _ z = |\alpha| ^ 2 - |\beta| ^ 2 ,\; \psi _ x + i\psi _ y = 2\alpha ^ * \beta$。

这时候如果我们取 $\alpha = \cos (\theta / 2) ,\; \beta = \sin (\theta / 2) e ^ {i\phi}$（正如 Bloch 球定义的那样），观测对应的自旋方向可以记为 $\hat{\psi} = (\sin {\theta} \cos {\phi} , \sin {\theta} \sin {\phi} , \cos {\theta})$；注意到这时候自旋方向具有了极坐标的形式，也就是说，Bloch 球一方面可视化了由二维复向量表示的 qubit，另一方面可视化了由三维实向量表示的自旋方向，而且这两者在 Bloch 球中是完全重合的。
