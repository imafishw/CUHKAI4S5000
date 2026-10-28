# 从信息量到交叉熵与极大似然：学习报告

本报告整理信息论与监督分类之间的联系，重点回答：为什么分类损失是负对数概率，以及它为什么同时具有交叉熵和极大似然两种解释。

## 1. 记号与基本约定

- 随机变量用 $X,Y$，其取值用 $x,y$；监督数据为 $D=\{(x_i,y_i)\}_{i=1}^N$。
- $p$ 表示真实分布或指定的目标分布；$q_\theta$ 表示参数为 $\theta$ 的模型分布。
- 默认使用自然对数 $\ln$（公式中简写为 $\log$），单位是 nat；使用 $\log_2$ 时单位是 bit。更换底数只会按正常数缩放损失，不改变最优参数。
- 以下先讨论离散分布。约定 $0\log0=0$；若 $p(x)>0$ 而 $q(x)=0$，交叉熵和相应 KL 散度为无穷大。

## 2. Self-information / surprisal：单次事件的信息量

$$
\boxed{I(x)=-\log p(x)}
$$

它衡量在分布 $p$ 下观察到事件 $x$ 的意外程度：越罕见，信息量越大；概率为 1 的事件信息量为 0。

| 事件概率 $p(x)$ | $-\ln p(x)$（nat） | $-\log_2p(x)$（bit） |
|---|---:|---:|
| 1 | 0 | 0 |
| 0.5 | 0.6931 | 1 |
| 0.25 | 1.3863 | 2 |
| 0.01 | 4.6052 | 6.6439 |

对数还有可加性：若事件独立，$p(x,z)=p(x)p(z)$，则 $I(x,z)=I(x)+I(z)$。这使联合事件的信息量可以相加。

注意，公式是 $-\log p(x)$，不是 $-\log x$：取对数的对象是事件概率，而不是事件本身。

## 3. Entropy：真实分布下的平均信息量

$$
\boxed{H(p)=\mathbb E_{X\sim p}[-\log p(X)]=-\sum_xp(x)\log p(x)}
$$

自信息描述一次结果有多意外；熵描述一个分布平均有多不确定。外面的 $p(x)$ 是取平均时的权重。

公平硬币 $p=(0.5,0.5)$ 的熵为 $\ln2\approx0.6931$ nat，即 1 bit。偏向一面的硬币 $p=(0.9,0.1)$ 的熵为

$$
H(p)=-0.9\ln0.9-0.1\ln0.1\approx0.3251\text{ nat}。
$$

后者更容易预测，因此平均不确定性更低。确定分布 $(1,0)$ 的熵为 0。

## 4. Cross-entropy：用模型概率评价真实结果

$$
\boxed{H(p,q)=\mathbb E_{X\sim p}[-\log q(X)]=-\sum_xp(x)\log q(x)}
$$

两个分布承担不同角色：

- **$p$ 决定平均哪些结果，以及各占多大权重**：数据按什么规律出现，或目标标签是什么。
- **$q$ 决定每种结果的代价**：模型给该结果多少概率，其代价就是 $-\log q(x)$。

因此，交叉熵是“结果来自 $p$，但使用 $q$ 给出的概率来计算平均惊讶程度”。在理想编码解释下，它也对应使用针对 $q$ 的码长时，对来自 $p$ 的数据所需的平均码长。

例如 $p=(0.9,0.1)$，模型 $q=(0.6,0.4)$：

$$
H(p,q)=-0.9\ln0.6-0.1\ln0.4\approx0.5514\text{ nat}。
$$

它大于 $H(p)\approx0.3251$，差距来自模型与真实分布不一致。

## 5. 监督分类：为什么 one-hot 只留下真实类别一项

模型对每个输入 $x_i$ 输出条件概率：

$$
q_\theta(k\mid x_i),\qquad \sum_{k=1}^Kq_\theta(k\mid x_i)=1。
$$

若观测标签为 $y_i$，将它写成 one-hot 目标向量：

$$
t_i(k)=\mathbf1\{k=y_i\}。
$$

单样本交叉熵为

$$
\begin{aligned}
\ell_i(\theta)
&=H(t_i,q_\theta(\cdot\mid x_i))\\
&=-\sum_{k=1}^Kt_i(k)\log q_\theta(k\mid x_i)\\
&=\boxed{-\log q_\theta(y_i\mid x_i)}。
\end{aligned}
$$

因为只有 $k=y_i$ 时权重为 1，其余权重为 0，求和后只剩真实类别的一项。

例如类别为猫、狗、鸟，真实标签为猫，$t_i=(1,0,0)$，模型输出 $(0.7,0.2,0.1)$，则

$$
\ell_i=-[1\ln0.7+0\ln0.2+0\ln0.1]=-\ln0.7\approx0.3567。
$$

**one-hot 是一次观测标签的表示，并不意味着真实条件分布 $p(y\mid x)$ 必然是 one-hot。** 同一输入可能存在歧义或标签噪声。使用软标签或 label smoothing 时，多项权重非零，不能再把交叉熵简化为单个负对数。

## 6. 经验交叉熵从哪里来

总体上，我们希望最小化真实数据分布下的期望损失：

$$
\begin{aligned}
R(\theta)
&=\mathbb E_{(X,Y)\sim p}[-\log q_\theta(Y\mid X)]\\
&=\mathbb E_{X\sim p_X}\left[-\sum_y p(y\mid X)\log q_\theta(y\mid X)\right]。
\end{aligned}
$$

通常不知道真实分布 $p(x,y)$，只有训练样本。用经验分布 $\hat p_D=\frac1N\sum_i\delta_{(x_i,y_i)}$ 代替总体分布，期望就成为样本平均：

$$
\boxed{L(\theta)=-\frac1N\sum_{i=1}^N\log q_\theta(y_i\mid x_i)}。
$$

这是 **empirical cross-entropy（经验交叉熵）**，也就是平均单样本交叉熵。在 i.i.d. 等适当条件下，对固定模型，样本平均可随样本增多逼近总体期望；训练损失较低并不自动保证新数据上的表现。

例如三个样本的真实类别概率依次为 $0.8,0.6,0.9$：

$$
L=-\frac{\ln0.8+\ln0.6+\ln0.9}{3}\approx0.2798。
$$

这里不能只统计训练集中“猫占多少、狗占多少”再套无条件交叉熵，因为不同输入 $x_i$ 对应不同的预测分布。分类学习评价的是 $q_\theta(y\mid x)$。

## 7. Label、概率、log 分别做什么

“label 决定对错、概率决定置信程度、log 将其变成 loss”可以分为三步理解：

1. **Label 提供评价依据。** 硬分类通常取 $\hat y_i=\arg\max_kq_\theta(k\mid x_i)$，再与 $y_i$ 比较得到对错。
2. **概率表达模型的确信程度。** 交叉熵查看分给真实标签的概率，而不是仅查看最终类别是否预测正确。概率是否可靠还取决于模型的校准程度。
3. **负对数把真实标签概率转换为损失。** 概率越高，损失越低；当概率接近 0，损失急剧增大。这里负对数与似然、信息量直接相连，而非任意选择。

二分类中，假设真实标签是正类：

| 分给正类的概率 | 按最大概率预测 | 单样本损失 $-\ln q(y_i\mid x_i)$ |
|---:|---|---:|
| 0.99 | 正确 | 0.0101 |
| 0.51 | 正确 | 0.6733 |
| 0.49 | 错误 | 0.7133 |
| 0.01 | 错误 | 4.6052 |

前两行准确率相同，loss 却能区分确信程度；最后一行体现对确信但错误预测的较大惩罚。单样本公式中虽只显式出现真实类别概率，各类别仍通过概率归一化相互关联。

## 8. MLE：从已观察数据反推参数

### 8.1 Likelihood（似然）

极大似然估计（Maximum Likelihood Estimation, MLE）的原则是：选择一组参数，使已经观察到的数据在模型下具有尽可能大的联合概率（连续情形是联合密度）。

给定 i.i.d. 数据 $z_1,\ldots,z_N$，定义

$$
\mathcal L(\theta;D)=\prod_{i=1}^Nq_\theta(z_i),\qquad
\hat\theta_{\rm MLE}\in\arg\max_\theta\mathcal L(\theta;D)。
$$

概率与似然使用同一表达式，但变量角色不同：概率是在固定参数后比较数据；似然是在固定观测数据后比较参数。**似然不是参数的概率分布**，MLE 也不是直接寻找“最可能的参数”；后者需要先验与后验等贝叶斯概念。

### 8.2 Log-likelihood 与 negative log-likelihood

对数严格递增，因此保留最大值的位置，同时将连乘转换为求和：

$$
\log\mathcal L(\theta;D)=\sum_i\log q_\theta(z_i)。
$$

定义负对数似然（Negative Log-Likelihood, NLL）：

$$
\operatorname{NLL}(\theta)=-\log\mathcal L(\theta;D)
=-\sum_i\log q_\theta(z_i)。
$$

最大化 log-likelihood 等价于最小化 NLL。取平均再乘 $1/N$，不会改变最优参数。实践中常直接计算稳定的 log 概率，避免先连乘许多小概率造成数值下溢。

### 8.3 硬币例子

设正面概率为 $\theta$，观测到指定顺序的 10 次结果，其中 7 正 3 反：

$$
\mathcal L(\theta)=\theta^7(1-\theta)^3,
\qquad \log\mathcal L=7\ln\theta+3\ln(1-\theta)。
$$

求导并令其为 0：

$$
\frac7\theta-\frac3{1-\theta}=0
\quad\Longrightarrow\quad\hat\theta=0.7。
$$

二阶导数 $-7/\theta^2-3/(1-\theta)^2<0$，所以这是最大值。如果观测仅记录“7 次正面”这一计数，似然还包含 $\binom{10}{7}$，但它不依赖参数，不改变 MLE。

## 9. 推导：MLE ⇔ 最小化 NLL ⇔ 最小化交叉熵

在监督分类中，将输入 $x_1,\ldots,x_N$ 视为给定，并假设标签在这些输入及参数给定后条件独立，条件似然为

$$
\mathcal L_c(\theta;D)=\prod_{i=1}^Nq_\theta(y_i\mid x_i)。
$$

因此：

$$
\begin{aligned}
\arg\max_\theta\mathcal L_c(\theta;D)
&=\arg\max_\theta\log\mathcal L_c(\theta;D)\\
&=\arg\min_\theta\left[-\sum_i\log q_\theta(y_i\mid x_i)\right]\\
&=\arg\min_\theta\left[-\frac1N\sum_i\log q_\theta(y_i\mid x_i)\right]\\
&=\boxed{\arg\min_\theta L(\theta)}。
\end{aligned}
$$

所以，对标准硬标签分类，**经验交叉熵恰好等于平均条件 NLL**。从统计估计看，这是 conditional MLE；从信息论看，这是对目标标签分布的交叉熵最小化。

“等价”指这些目标拥有相同的最优解集合，并非所有数值完全相等：NLL 总和与平均交叉熵相差因子 $N$。这一结论针对上述目标；加入正则化、非统一样本权重或软标签后，需要按新的目标重新解释，不能直接称为原始硬标签数据的 MLE。

## 10. KL divergence 与交叉熵的分解

KL 散度（Kullback–Leibler divergence）定义为

$$
\boxed{D_{\rm KL}(p\Vert q)=\sum_xp(x)\log\frac{p(x)}{q(x)}}。
$$

将对数比值展开：

$$
\begin{aligned}
D_{\rm KL}(p\Vert q)
&=\sum_xp(x)\log p(x)-\sum_xp(x)\log q(x)\\
&=-H(p)+H(p,q)。
\end{aligned}
$$

因此，在这些量有限的常见情形下，

$$
\boxed{H(p,q)=H(p)+D_{\rm KL}(p\Vert q)}。
$$

- $H(p)$：数据分布自身的不确定性；固定目标分布时，它与模型参数无关。
- $D_{\rm KL}(p\Vert q)$：用模型 $q$ 代替 $p$ 所带来的额外平均代价。
- KL 非负，故 $H(p,q)\ge H(p)$。KL 为零当且仅当两个分布相同；它一般不对称，也不是满足距离公理的普通距离。

继续使用 $p=(0.9,0.1)$、$q=(0.6,0.4)$：

$$
D_{\rm KL}(p\Vert q)
=0.9\ln\frac{0.9}{0.6}+0.1\ln\frac{0.1}{0.4}
\approx0.2263，
$$

于是 $0.5514\approx0.3251+0.2263$。

监督分类对应的总体分解是

$$
R(\theta)=H_p(Y\mid X)
+\mathbb E_{X\sim p_X}\left[D_{\rm KL}\big(p(\cdot\mid X)\Vert q_\theta(\cdot\mid X)\big)\right]。
$$

固定真实分布时，最小化总体交叉熵等价于最小化平均条件 KL。若模型能表示真实条件分布，理论最优为 $q_\theta(\cdot\mid x)=p(\cdot\mid x)$；模型受限时，则寻找其可表示范围内的最佳近似。

对单个 one-hot 目标，$H(t_i)=0$，所以该样本的交叉熵等于 $D_{\rm KL}(t_i\Vert q_\theta)$。但这不表示真实总体的条件熵也为 0，更不保证模型在有限参数下能把每个训练样本的 loss 降到 0。

## 11. 二分类的常用展开

若 $y_i\in\{0,1\}$，记 $r_i=q_\theta(Y=1\mid x_i)$，则

$$
q_\theta(y_i\mid x_i)=r_i^{y_i}(1-r_i)^{1-y_i}。
$$

取负对数得到 binary cross-entropy（BCE）：

$$
\boxed{\ell_i=-y_i\log r_i-(1-y_i)\log(1-r_i)}。
$$

$y_i=1$ 时只留下 $-\log r_i$；$y_i=0$ 时只留下 $-\log(1-r_i)$。它仍然是在惩罚“模型分给真实标签的概率太小”。

## 12. 最终知识链路

$$
\underbrace{-\log p(x)}_{\text{单次事件的信息量}}
\quad\xrightarrow{\text{按 }p\text{ 取平均}}\quad
\underbrace{-\sum_xp(x)\log p(x)}_{\text{熵 }H(p)}
$$

$$
\underbrace{-\sum_xp(x)\log q(x)}_{\text{交叉熵：真实权重 }p\text{，模型概率 }q}
\quad\xrightarrow{\text{one-hot 标签}}\quad
\underbrace{-\log q_\theta(y_i\mid x_i)}_{\text{单样本 loss}}
\quad\xrightarrow{\text{样本平均}}\quad
\underbrace{-\frac1N\sum_i\log q_\theta(y_i\mid x_i)}_{\text{经验交叉熵}}
$$

$$
\boxed{\text{最大化条件似然}
\iff\text{最大化条件 log-likelihood}
\iff\text{最小化条件 NLL}
\iff\text{最小化经验交叉熵}}
$$

$$
\boxed{\text{固定 }p:\quad\min_\theta H(p,q_\theta)
\iff\min_\theta D_{\rm KL}(p\Vert q_\theta)}
$$

最后记住三句话：**标签指定要评价的结果；模型概率决定这个结果有多被看好；负对数把这个概率变成可累加、可优化的损失。** 对样本求平均得到经验交叉熵；从联合概率出发，则得到同一个目标的极大似然解释。
