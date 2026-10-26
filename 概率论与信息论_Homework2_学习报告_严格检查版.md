# 概率论与信息论 - Homework 2 学习报告

> 目标：按 Homework 2 的 Q1-Q5 学习，并能独立写出每一步。本文以中文为主；全文公式已通过 KaTeX 严格语法检查；阅读时请使用数学公式预览。另附已完成公式排版的离线 HTML 阅读版。数值默认保留约 4 位小数。

## 阅读说明与资料边界

**课程/作业原始内容（本报告的题目、符号和要求以此为准）**

- `Lecture 2.pdf`：本课程 Lecture 2，覆盖概率规则、概率密度、变量变换、信息论等。
- `Homework 2.pdf`：本报告中的 Q1-Q5 知识检验及其给定数据均来自该作业。

**外部补充（只用于补足直觉、背景和标准表述，不改变课程符号或题意）**

- MIT 6.041SC 的条件概率、Bayes 与随机变量课程资源。
- Stanford Statistics 311 的信息论讲义（entropy、KL、mutual information）。
- David J. C. MacKay 的 *Information Theory, Inference, and Learning Algorithms*（信息论与机器学习连接）。

**符号约定。** 离散变量用 $p(x)$ ，连续变量用密度 $p_X(x)$ 。 $\log_2$ 的单位是 **bit**， $\ln$ 的单位是 **nat**。必须跟随题目指定的底数；不同底数下的数值不能直接混比。

---

## Q1 - 条件概率、全概率与 Bayes 定理

### 本题要会什么

给定疾病状态 $C\in\{0,1\}$ 与检测结果 $T\in\{0,1\}$ ，从 $P(T\mid C)$ 和 $P(C)$ 推出 $P(T)$ 与 $P(C\mid T)$ 。这是“从原因预测观测”与“看到观测反推原因”的区别。

### 1. 核心概念与公式

**联合概率** $P(A,B)$ 表示 $A$ 和 $B$ 同时发生。条件概率的定义是

$$
P(A\mid B)=\frac{P(A,B)}{P(B)},\qquad P(B)>0.
$$

移项得到**乘法公式**：

$$
P(A,B)=P(A\mid B)P(B)=P(B\mid A)P(A).
$$

若 $\{B_i\}$ 是互斥且完备的一组情形，则对它们求和得到**全概率公式**：

$$
P(A)=\sum_iP(A\mid B_i)P(B_i).
$$

把乘法公式的两个写法相等，得到 **Bayes 定理**：

$$
P(B\mid A)=\frac{P(A\mid B)P(B)}{P(A)}.
$$

在疾病例子中：

$$
\underbrace{P(C\mid T)}_{\text{posterior}}
=\frac{\overbrace{P(T\mid C)}^{\text{likelihood}}\underbrace{P(C)}_{\text{prior}}}{\underbrace{P(T)}_{\text{evidence}}}.
$$

这里的 likelihood 是把 $C$ 看作假设、 $T$ 看作观测时的 $P(T\mid C)$ 。它不是 $P(C\mid T)$ 。

若 $A,B$ 独立，则

$$
P(A,B)=P(A)P(B)\quad\Longleftrightarrow\quad P(A\mid B)=P(A)\;(P(B)>0).
$$

### 2. 直觉与一个小例子

即使一个检测有很高灵敏度，阳性者也未必大概率患病：还要看疾病是否罕见。设疾病率 $1\%$ ，灵敏度 $99\%$ ，假阳性率 $5\%$ 。在 10,000 人中，约 100 人患病，其中约 99 人阳性；9,900 人未患病，其中约 495 人却阳性。因此阳性人群中真正患病的比例约为 $99/(99+495)\approx16.7\%$ ，远不是 $99\%$ 。这就是**base-rate effect（基率效应）**。

### 3. 常见误区

- 把 $P(T=1\mid C=1)$ （灵敏度）误读成 $P(C=1\mid T=1)$ （阳性预测值）。条件交换后一般不相等。
- 算 $P(T=1)$ 时漏掉 $C=0$ 的假阳性路径。
- 把“独立”理解为“两个变量取值不同”；独立的定义是一个变量的信息不会改变另一个变量的分布。

### 4. 知识检验：Homework 2 Q1

已知疾病率为 $2\%$ ，且

$$
P(T=1\mid C=1)=0.95,\qquad P(T=1\mid C=0)=0.04.
$$

要求写出先验与所有 $P(T=t\mid C=c)$ ，求 $P(T=1)$ 、 $P(C=1\mid T=1)$ 、 $P(C=1\mid T=0)$ ，解释 prior/likelihood/posterior，并说明独立检测为何无信息。

### 5. 完整答案与步骤

**(a)**

$$
P(C=1)=0.02,\qquad P(C=0)=0.98.
$$

由每个条件分布的概率和为 1：

$$
\begin{aligned}
P(T=1\mid C=1)&=0.95,&P(T=0\mid C=1)&=0.05,\\
P(T=1\mid C=0)&=0.04,&P(T=0\mid C=0)&=0.96.
\end{aligned}
$$

**(b)** 阳性可经由“患病且阳性”或“未患病且阳性”发生：

$$
\begin{aligned}
P(T=1)&=P(T=1\mid C=1)P(C=1)+P(T=1\mid C=0)P(C=0)\\
&=0.95(0.02)+0.04(0.98)\\
&=0.019+0.0392=\boxed{0.0582}.
\end{aligned}
$$

**(c)**

$$
\begin{aligned}
P(C=1\mid T=1)
&=\frac{P(T=1\mid C=1)P(C=1)}{P(T=1)}\\
&=\frac{0.95(0.02)}{0.0582}
=\boxed{0.3265\approx32.65\%}.
\end{aligned}
$$

**(d)** 先求 $P(T=0)=1-0.0582=0.9418$ ，再用 Bayes：

$$
P(C=1\mid T=0)=\frac{0.05(0.02)}{0.9418}
=\boxed{0.001062\approx0.106\%}.
$$

**(e)** $P(T=1\mid C=1)=0.95$ 是“已患病时检出的机会”，即 **likelihood**； $P(C=1)=0.02$ 是见到检测前的 **prior**； $P(C=1\mid T=1)=0.3265$ 是见到阳性后更新的 **posterior**。后验还受疾病基率与假阳性率影响，所以通常不等于灵敏度。

**(f)** 若 $T$ 与 $C$ 独立，则 $P(T\mid C)=P(T)$ 。代入 Bayes：

$$
P(C\mid T)=\frac{P(T\mid C)P(C)}{P(T)}
=\frac{P(T)P(C)}{P(T)}=\boxed{P(C)}.
$$

因此看到 $T$ 前后，对 $C$ 的判断不变；该检测不提供疾病状态的信息。

---

## Q2 - 连续随机变量、期望/方差与变量变换

### 本题要会什么

辨认概率密度（PDF），通过积分求区间概率、期望和方差，并在 $Y=g(X)$ 时正确加入 Jacobian。

### 1. 核心概念与公式

连续型随机变量的密度 $p_X(x)$ 必须满足

$$
p_X(x)\ge0,\qquad \int_{-\infty}^{\infty}p_X(x)\,dx=1.
$$

密度在单点的函数值不是单点概率；连续变量满足 $P(X=x)=0$ 。区间概率由面积给出：

$$
P(a<X<b)=\int_a^bp_X(x)\,dx.
$$

若积分存在，

$$
E[X]=\int xp_X(x)\,dx,\qquad E[X^2]=\int x^2p_X(x)\,dx,
$$

$$
\operatorname{Var}(X)=E[(X-E[X])^2]=E[X^2]-E[X]^2.
$$

对一维、可微且单调的变换 $Y=g(X)$ ，令 $x=g^{-1}(y)$ ，则

$$
p_Y(y)=p_X\bigl(g^{-1}(y)\bigr)\left|\frac{d}{dy}g^{-1}(y)\right|.
$$

若变换不一一对应，必须将每个反解分支的密度贡献相加：

$$
p_Y(y)=\sum_{x_i:g(x_i)=y}p_X(x_i)\left|\frac{dx_i}{dy}\right|.
$$

本题 $X>0$ ，所以 $Y=X^2$ 在支持集上单调，不需要多分支求和。

### 2. 直觉与一个小例子

Jacobian 来自“概率质量守恒”：一个很短的区间 $dx$ 被映射为 $dy$ 时，应有

$$
p_X(x)|dx|=p_Y(y)|dy|.
$$

例如 $X\sim\operatorname{Unif}(0,1)$ 、 $Y=2X$ 。虽然 $p_X(x)=1$ ，但 $Y$ 的支持集是 $(0,2)$ ，且

$$
p_Y(y)=1\cdot\left|\frac{d(y/2)}{dy}\right|=\frac12.
$$

若只把 $x=y/2$ 代回 $p_X$ ，会错误得到密度 1；其在 $(0,2)$ 上的积分是 2，不是 1。

### 3. 常见误区

- 用 $p_X(x)$ 代替 $P(X=x)$ 。连续情形中前者是密度，后者为 0。
- 算方差时写成 $E[X^2]-E[X]$ ，遗漏 $E[X]$ 的平方。
- 变量变换时直接写 $p_Y(y)=p_X(g^{-1}(y))$ ，漏掉 Jacobian。
- 没有先写出 $Y$ 的支持集。密度必须在正确的 $y$ 范围外为 0。

### 4. 知识检验：Homework 2 Q2

$$
p_X(x)=\begin{cases}2x,&0<x<1,\\0,&\text{otherwise},\end{cases}
\qquad Y=X^2.
$$

验证密度、求 $P(X<1/2)$ 、 $E[X]$ 与 $\operatorname{Var}(X)$ ，再完整导出 $Y$ 的密度并解释 Jacobian。

### 5. 完整答案与步骤

**(a)** 在 $(0,1)$ 上 $2x>0$ ，且

$$
\int_{-\infty}^{\infty}p_X(x)\,dx=\int_0^1 2x\,dx=[x^2]_0^1=1.
$$

故它是合法 PDF。

**(b)**

$$
P\left(X<\frac12\right)=\int_0^{1/2}2x\,dx=[x^2]_0^{1/2}=\boxed{\frac14}.
$$

**(c)**

$$
E[X]=\int_0^1x(2x)\,dx=2\int_0^1x^2\,dx=\boxed{\frac23},
$$

$$
E[X^2]=\int_0^1x^2(2x)\,dx=2\int_0^1x^3\,dx=\frac12.
$$

所以

$$
\operatorname{Var}(X)=\frac12-\left(\frac23\right)^2=\boxed{\frac1{18}}.
$$

**(d)** $0<X<1$ 且平方在该区间严格递增，故 $\boxed{0<Y<1}$ 。

**(e)** $y=x^2$ 、 $x>0$ ，故

$$
x=\sqrt y,\qquad \left|\frac{dx}{dy}\right|=\boxed{\frac1{2\sqrt y}}.
$$

**(f)**

$$
\begin{aligned}
p_Y(y)&=p_X(\sqrt y)\left|\frac{dx}{dy}\right|\\
&=(2\sqrt y)\left(\frac1{2\sqrt y}\right)=1,
\end{aligned}
$$

所以

$$
p_Y(y)=\begin{cases}1,&0<y<1,\\0,&\text{otherwise}.\end{cases}
$$

即 $Y\sim\operatorname{Unif}(0,1)$ 。

**(g)**

$$
\int_{-\infty}^{\infty}p_Y(y)\,dy=\int_0^1 1\,dy=\boxed{1}.
$$

**(h)** 密度的含义是“每单位横轴长度的概率质量”。变换会压缩或拉伸局部长度， $\left|dx/dy\right|$ 正是补偿这一尺度变化的 Jacobian，使对应小区间的概率质量相等，并保证新密度仍积分为 1。

---

## Q3 - 信息量、熵与无前缀编码

### 本题要会什么

区分单个观测的信息量和随机变量的平均不确定性（熵），并解释为什么高概率状态适合短码字。

### 1. 核心概念与公式

当对数底为 2 时，结果单位为 bit。单个结果 $x$ 的**信息量**（surprisal）是

$$
h(x)=-\log_2p(x).
$$

罕见事件 $p(x)$ 小， $h(x)$ 大；必然事件的信息量为 0。独立事件的信息量可加，因为 $-\log[p(x)p(y)]= -\log p(x)-\log p(y)$ 。

**熵**是信息量的期望，而不是某一个结果的信息量：

$$
H(X)=E[h(X)]= -\sum_xp(x)\log_2p(x).
$$

若码字长度为 $\ell(x)$ ，平均码长为

$$
L=\sum_xp(x)\ell(x).
$$

对于二进制无前缀编码，Shannon 源编码定理给出 $H(X)\le L < H(X)+1$ （适当编码下）；理想码长的直觉是 $\ell(x)\approx-\log_2p(x)$ 。

### 2. 直觉与一个小例子

公平硬币 $H$ 或 $T$ 各以 $1/2$ 发生。每个结果的信息量是 1 bit，故熵也是 1 bit。若硬币 $99\%$ 为正面，则“正面”几乎不意外、“反面”很意外；平均看来却更容易预测，所以熵低于 1 bit。

### 3. 常见误区

- 把 $h(x)$ 当作熵：前者对应一次具体观察，后者是其平均。
- 忘记 $0\log 0$ 按极限定义为 0。
- 看见二进制编码就默认平均码长等于熵；只有特殊情形相等，通常只是接近且不低于熵。
- 用 $\ln$ 却把答案称作 bits。本题要求 $\log_2$ 。

### 4. 知识检验：Homework 2 Q3

$X$ 的八个状态 $(a,b,c,d,e,f,g,h)$ 的概率依次为

$$
\left(\frac12,\frac14,\frac18,\frac1{16},\frac1{64},\frac1{64},\frac1{64},\frac1{64}\right).
$$

给定码字 $0,10,110,1110,111100,111101,111110,111111$ 。计算信息量、熵、平均码长，并比较均匀分布情形。

### 5. 完整答案与步骤

**(a)** 概率是 $2^{-k}$ 时，信息量就是 $k$ bits。因此

| 状态 | $p(x)$ | $h(x)=-\log_2p(x)$ |
|---|---:|---:|
| $a$ | $1/2$ | 1 bit |
| $b$ | $1/4$ | 2 bits |
| $c$ | $1/8$ | 3 bits |
| $d$ | $1/16$ | 4 bits |
| $e,f,g,h$ | $1/64$ | 6 bits each |

$e,f,g,h$ 最罕见，信息量最大； $a$ 最常见，信息量最小。概率越小， $-\log_2p$ 越大。

**(b)**

$$
\begin{aligned}
H(X)&=\frac12(1)+\frac14(2)+\frac18(3)+\frac1{16}(4)+4\left(\frac1{64}\cdot6\right)\\
&=0.5+0.5+0.375+0.25+0.375\\
&=\boxed{2\text{ bits}}.
\end{aligned}
$$

每次观察具体是哪个状态尚未确定，所以熵必须按每种结果发生的概率加权平均；它不是任一指定状态（如 $a$ ）的 1 bit 或 $e$ 的 6 bits。

**(c)** 码长正好是 $(1,2,3,4,6,6,6,6)$ ，故

$$
L=\frac12(1)+\frac14(2)+\frac18(3)+\frac1{16}(4)+4\left(\frac1{64}\cdot6\right)=\boxed{2\text{ bits}}.
$$

**(d)** $\boxed{L=H(X)=2\text{ bits}}$ 。这套码字长度恰好等于 $-\log_2p(x)$ ，所以达到熵；这不是任意分布或任意可解码码都会有的巧合。

**(e)** 若八个状态等概率， $p(x)=1/8$ ：

$$
H(X)=-8\left(\frac18\log_2\frac18\right)=\boxed{3\text{ bits}}.
$$

均匀分布的 3 bits 高于原分布的 2 bits，因为每一个结果都同样难预测；原分布把大量概率集中在 $a,b$ ，不确定性更小。

**(f)** 高概率状态的 $-\log_2p(x)$ 小，携带的信息较少；给它较短码字能降低 $L=\sum p(x)\ell(x)$ 。低概率状态可使用长码字，因为它们很少出现，对平均长度影响较小。这正是压缩的核心权衡。

---

## Q4 - 多重度、Stirling 近似与最大熵原则

### 本题要会什么

从“许多微观排列对应同一宏观计数”理解熵，并用 Stirling 近似得到 Shannon 熵形式；理解在给定均值、方差时高斯分布为何是最大微分熵分布。

### 1. 核心概念与公式

把 $N$ 个**可区分**物体分入 3 个 bin，若占据数为 $(n_1,n_2,n_3)$ ，则一个 macrostate 对应的微观排列数（**多重度**）是多项式系数：

$$
W=\frac{N!}{n_1!n_2!n_3!}.
$$

- **Microstate（微观状态）**：哪个具体物体在哪个 bin。
- **Macrostate（宏观状态）**：只记录每个 bin 的数量 $(n_1,n_2,n_3)$ 。

题中定义的每个物体的对数多重度为

$$
H_N=\frac1N\ln W.
$$

令 $p_i=n_i/N$ ，并用 $\ln n!\approx n\ln n-n$ ，有大 $N$ 近似

$$
\frac1N\ln W\approx-\sum_ip_i\ln p_i.
$$

右侧是用自然对数定义的 Shannon entropy，单位为 nat。

连续型的**微分熵**为

$$
h(X)=-\int p(x)\ln p(x)\,dx.
$$

它与离散熵有重要差异：会依赖坐标尺度，甚至可为负；不要把它直接解释为“压缩所需的 bits”。在固定均值 $\mu$ 和方差 $\sigma^2$ 的连续分布中，高斯分布最大化微分熵，且

$$
h\bigl(N(\mu,\sigma^2)\bigr)=\frac12\ln(2\pi e\sigma^2).
$$

### 2. 直觉与一个小例子

若 12 个编号球放进 3 个盒子， $(4,4,4)$ 只指定数量，却允许许多种“具体哪些球在第一个盒子”的组合； $(10,1,1)$ 的限制则强得多。多重度大的宏观状态不是“每个微观状态更可能”，而是它包含的等可能微观状态更多，因此总概率更大。

### 3. 常见误区

- 忘记题目说物体可区分：若不可区分， $W$ 的计数不是该多项式系数。
- 把有限 $N$ 的 $H_N$ 与 $H(p)$ 当作严格相等；Stirling 给出的是渐近近似，有限量存在修正项。
- 把“最大熵”理解为“随机挑一个任何时候都最好的分布”。它是在**指定约束集合内**的选择。
- 忘记离散熵和微分熵不同；高斯最大的是固定均值和方差条件下的**微分熵**。

### 4. 知识检验：Homework 2 Q4

$N=12$ ，两种 macrostate 为 $A=(10,1,1)$ 、 $B=(4,4,4)$ 。计算 $W$ 、 $H_N$ 、 $H(p)$ ，说明 Stirling 极限，判断三类分布中最大熵者，并解释高斯的最大熵含义。

### 5. 完整答案与步骤

**(a)**

$$
W_A=\frac{12!}{10!1!1!}=12\times11=\boxed{132},
$$

$$
W_B=\frac{12!}{4!4!4!}=\boxed{34\,650}.
$$

因此 $B$ 对应更多微观排列。

**(b)**

$$
H_N(A)=\frac1{12}\ln132\approx\boxed{0.4069\text{ nats}},
$$

$$
H_N(B)=\frac1{12}\ln34650\approx\boxed{0.8711\text{ nats}}.
$$

故 $B$ 更大，与其较大多重度一致。

**(c)** 对 $A$ ， $p=(5/6,1/12,1/12)$ ：

$$
H(p_A)=-\left[\frac56\ln\frac56+2\left(\frac1{12}\ln\frac1{12}\right)\right]
=\boxed{0.5661\text{ nats}}.
$$

对 $B$ ， $p=(1/3,1/3,1/3)$ ：

$$
H(p_B)=-3\left(\frac13\ln\frac13\right)=\boxed{\ln3\approx1.0986\text{ nats}}.
$$

二者都高于对应的有限 $N$ $H_N$ ，但排序相同；差异来自 Stirling 的有限样本修正。

**(d)**

$$
\begin{aligned}
\ln W
&=\ln N!-\sum_i\ln n_i!\\
&\approx (N\ln N-N)-\sum_i(n_i\ln n_i-n_i)\\
&=N\ln N-\sum_i n_i\ln n_i,
\end{aligned}
$$

因为 $\sum_i n_i=N$ 。令 $n_i=Np_i$ ，则

$$
\begin{aligned}
\frac1N\ln W
&\approx\ln N-\sum_ip_i\ln(Np_i)\\
&=\ln N-\sum_ip_i(\ln N+\ln p_i)\\
&=\boxed{-\sum_ip_i\ln p_i},
\end{aligned}
$$

其中使用了 $\sum_ip_i=1$ 。误差相对于 $N$ 会消失，因此在大 $N$ 时趋近。

**(e)** 最大熵者是均匀分布：

$$
\boxed{(p_1,p_2,p_3)=(1/3,1/3,1/3)}.
$$

在只知道三项非负且和为 1 时，任何偏向都增加了额外结构/假设；均匀分布最不偏置，也最难预测。

**(f)** 最大熵原则说：在所有满足已知约束的分布中，选熵最大的那个，避免擅自引入未被数据支持的结构。只知 $\mu$ 、 $\sigma^2$ 时，固定这两个量的分布中高斯的微分熵最大。因此选择高斯不是断言真实世界必定高斯，而是说在这些信息之外不额外假定偏度、尖峰、双峰或特定尾部形状的最保守选择。

---

## Q5 - 交叉熵、KL 散度、最大似然与互信息

### 本题要会什么

把真实分布 $p$ 与模型 $q$ 区分开；掌握 $H(p,q)=H(p)+D_{KL}(p\Vert q)$ ，并由此连接最大似然训练。然后从联合分布求边缘、条件熵与互信息。

### 1. 核心概念与公式

对离散分布 $p$ ：

$$
H(p)=-\sum_xp(x)\log p(x).
$$

以真实分布 $p$ 加权、以模型分布 $q$ 编码的**交叉熵**：

$$
H(p,q)=-\sum_xp(x)\log q(x).
$$

**KL 散度**：

$$
D_{KL}(p\Vert q)=\sum_xp(x)\log\frac{p(x)}{q(x)}\ge0.
$$

代数展开立即得到

$$
\boxed{H(p,q)=H(p)+D_{KL}(p\Vert q)}.
$$

注意 $D_{KL}(p\Vert q)$ 通常不对称，且不是严格的距离。若某个 $p(x)>0$ 而 $q(x)=0$ ，则 $D_{KL}(p\Vert q)=+\infty$ 。

对数据 $x_1,\ldots,x_n$ 和模型 $q_\theta$ ，最大似然：

$$
\max_\theta\prod_{i=1}^nq_\theta(x_i)
\Longleftrightarrow
\min_\theta\left[-\frac1n\sum_{i=1}^n\ln q_\theta(x_i)\right].
$$

右侧就是对经验数据分布的交叉熵（也称平均 negative log-likelihood）。

对于联合分布：

$$
p_X(x)=\sum_yp(x,y),\qquad p(y\mid x)=\frac{p(x,y)}{p_X(x)}.
$$

条件熵与互信息（以下若用 $\log_2$ 则单位为 bit）为

$$
H(Y\mid X)=-\sum_{x,y}p(x,y)\log_2p(y\mid x),
$$

$$
I(X;Y)=H(Y)-H(Y\mid X)=H(X)-H(X\mid Y).
$$

等价地， $I(X;Y)=D_{KL}(p(x,y)\Vert p_X(x)p_Y(y))$ ，故它非负；独立时为 0。

### 2. 直觉与一个小例子

若真实标签在 70%/20%/10% 三类间分布，模型把真实常见类的概率压得很低，交叉熵会明显上升，因为 $-\ln q(x)$ 对小 $q(x)$ 惩罚很大。互信息则回答另一个问题：知道 $X$ 后， $Y$ 的不确定性减少多少。若 $X$ 是公平硬币， $Y=X$ ，则知道 $X$ 后 $Y$ 完全确定，故 $I(X;Y)=1$ bit。

### 3. 常见误区

- 写交叉熵时把外层权重误写为 $q(x)$ ；正确的是来自真实/数据分布的 $p(x)$ 。
- 以为 KL 是对称距离：一般 $D_{KL}(p\Vert q)\ne D_{KL}(q\Vert p)$ 。
- 误称 `maximum likelihood = 最大化概率`；它是最大化“观测数据在参数下的似然”，参数不是随机变量时不是其 posterior。
- 算 $H(Y\mid X)$ 时只算一个 $X$ 值；必须按 $p(x)$ 加权所有条件熵。

### 4. 知识检验：Homework 2 Q5

**Part I（用 $\ln$ ，单位 nats）**

$$
p=(0.7,0.2,0.1),\quad q_1=(0.6,0.25,0.15),\quad q_2=(0.4,0.3,0.3).
$$

计算 $H(p)$ 、两种交叉熵与 KL，验证恒等式，并说明最小交叉熵和最大似然的关系。

**Part II（用 $\log_2$ ，单位 bits）**

联合分布为

|  | $Y=0$ | $Y=1$ |
|---|---:|---:|
| $X=0$ | 0.40 | 0.10 |
| $X=1$ | 0.10 | 0.40 |

求边缘、 $H(Y)$ 、条件分布、 $H(Y\mid X)$ 、互信息，并解释独立情形。

### 5. 完整答案与步骤

#### Part I - 自然对数，单位 nats

**(a)**

$$
\begin{aligned}
H(p)&=-[0.7\ln0.7+0.2\ln0.2+0.1\ln0.1]\\
&=\boxed{0.8018\text{ nats}}.
\end{aligned}
$$

**(b)**

$$
\begin{aligned}
H(p,q_1)&=-[0.7\ln0.6+0.2\ln0.25+0.1\ln0.15]\\
&=\boxed{0.8245\text{ nats}},\\
H(p,q_2)&=-[0.7\ln0.4+0.2\ln0.3+0.1\ln0.3]\\
&=\boxed{1.0026\text{ nats}}.
\end{aligned}
$$

因此 $q_1$ 的交叉熵更低，按该准则拟合得更好。

**(c)** 用 $D_{KL}(p\Vert q)=H(p,q)-H(p)$ 计算（也可逐项计算定义式）：

$$
\begin{aligned}
D_{KL}(p\Vert q_1)&=0.8245488-0.8018186=\boxed{0.02273\text{ nats}},\\
D_{KL}(p\Vert q_2)&=1.0025954-0.8018186=\boxed{0.20078\text{ nats}}.
\end{aligned}
$$

数值验证：

$$
0.8018186+0.0227303=0.8245488=H(p,q_1),
$$

$$
0.8018186+0.2007768=1.0025954=H(p,q_2).
$$

**(d)** 在优化 $q$ 时， $p$ 是固定的：

$$
H(p,q)=\underbrace{H(p)}_{\text{constant in }q}+D_{KL}(p\Vert q).
$$

故两者只差常数，必有

$$
\boxed{\arg\min_qH(p,q)=\arg\min_qD_{KL}(p\Vert q)}.
$$

**(e)** 经验数据的平均负对数似然为

$$
-\frac1n\sum_{i=1}^n\ln q_\theta(x_i).
$$

它是经验分布 $\hat p$ 对 $q_\theta$ 的交叉熵 $H(\hat p,q_\theta)$ 。因此最大化似然等价于最小化经验交叉熵，也等价于最小化 $D_{KL}(\hat p\Vert q_\theta)$ （在 $\hat p$ 固定时）。

#### Part II - 以 2 为底的对数，单位 bits

**(f)**

$$
p_X(0)=0.40+0.10=0.5,\qquad p_X(1)=0.10+0.40=0.5.
$$

同理

$$
p_Y(0)=0.40+0.10=0.5,\qquad p_Y(1)=0.10+0.40=0.5.
$$

所以 $\boxed{p(X)=(0.5,0.5),\ p(Y)=(0.5,0.5)}$ 。

**(g)**

$$
H(Y)=-2\left(\frac12\log_2\frac12\right)=\boxed{1\text{ bit}}.
$$

**(h)**

$$
p(Y\mid X=0)=(0.40/0.5,\,0.10/0.5)=\boxed{(0.8,0.2)},
$$

$$
p(Y\mid X=1)=(0.10/0.5,\,0.40/0.5)=\boxed{(0.2,0.8)}.
$$

两个条件分布的熵相同：

$$
H(Y\mid X=0)=H(Y\mid X=1)=-[0.8\log_20.8+0.2\log_20.2]\approx0.7219.
$$

又 $P(X=0)=P(X=1)=0.5$ ，故

$$
H(Y\mid X)=0.5(0.7219)+0.5(0.7219)=\boxed{0.7219\text{ bits}}.
$$

**(i)**

$$
I(X;Y)=H(Y)-H(Y\mid X)=1-0.7219281=\boxed{0.2781\text{ bits}}.
$$

**(j)** 在未观察 $X$ 时，对 $Y$ 的不确定性是 1 bit；观察 $X$ 后尚余约 0.7219 bits。因此观察 $X$ 平均带来 $0.2781$ bits 信息：

$$
\boxed{\text{information gained}=\text{uncertainty before}-\text{uncertainty after}}.
$$

**(k)** 若独立， $p(y\mid x)=p(y)$ ，所以知道 $X$ 不改变 $Y$ 的不确定性： $H(Y\mid X)=H(Y)$ 。因而

$$
\boxed{I(X;Y)=0}.
$$

---

## 作业前速查表

| 看到的任务 | 首选公式 / 动作 |
|---|---|
| 给 $P(T\mid C)$ 、 $P(C)$ ，求 $P(T)$ | $P(T)=\sum_cP(T\mid C=c)P(C=c)$ |
| 已知观测，反推原因 | $P(C\mid T)=P(T\mid C)P(C)/P(T)$ |
| 连续变量区间概率 | $\int_a^bp_X(x)\,dx$ |
| 方差 | 先算 $E[X]$ 、 $E[X^2]$ ，再用 $E[X^2]-E[X]^2$ |
| $Y=g(X)$ | 先写 $Y$ 支持集、求反函数和 Jacobian；非一一映射则按分支求和 |
| 单个结果“多意外” | $h(x)=-\log p(x)$ |
| 平均不确定性 | $H(X)=-\sum p\log p$ |
| 模型与真实分布的编码损失 | $H(p,q)=-\sum p\log q$ |
| 比较模型贴近真实分布 | $D_{KL}(p\Vert q)=H(p,q)-H(p)$ |
| 观察 $X$ 对 $Y$ 的价值 | $I(X;Y)=H(Y)-H(Y\mid X)$ |

## 最后自检清单

- [ ] 我写清楚了条件符号 $\mid$ 左右各是谁吗？
- [ ] 我求连续变量概率时用的是积分而非密度函数值吗？
- [ ] 我做变量变换时写了支持集和 Jacobian 吗？
- [ ] 我是否按题目使用了正确的对数底（bit 或 nat）？
- [ ] 我把 $p$ （真实/经验分布）与 $q$ （模型分布）区分开了吗？
- [ ] 我求条件熵时是否对所有 $x$ 以 $p(x)$ 加权？

## 来源与延伸阅读

### 课程/作业原始资料

1. 本课程 `Lecture 2.pdf`（本报告用作课程定义、符号和讲授范围的依据）。
2. 本课程 `Homework 2.pdf`（本报告 Q1-Q5 的题目、参数与要求的依据）。

### 外部补充资料

1. [MIT OpenCourseWare, 6.041SC: Probabilistic Systems Analysis and Applied Probability - Resource Index](https://www.ocw.mit.edu/courses/6-041sc-probabilistic-systems-analysis-and-applied-probability-fall-2013/pages/resource-index/)：条件概率、Bayes、独立性、随机变量与导出分布的系统课程资源。
2. [John Duchi, *Lecture Notes on Statistics and Information Theory*（Stanford）](https://stanford.edu/class/stats311/lecture-notes.pdf)：entropy、KL divergence、mutual information 与条件版本的严谨讲义。
3. [David J. C. MacKay, *Information Theory, Inference, and Learning Algorithms*](https://www.inference.org.uk/mackay/itila/book.html)：将信息论、推断和机器学习联系起来的开放教材。

> 延伸阅读中的公式采用各自的符号与对数约定；提交本作业时优先遵从题面：Q3 与 Q5 Part II 使用 $\log_2$ ，Q4 与 Q5 Part I 使用 $\ln$ 。
