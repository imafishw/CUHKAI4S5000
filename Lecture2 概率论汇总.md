# Lecture2 概率论汇总 | Probability Summary

> 来源：香港中文大学 Huichen Zhu Lecture 2 Probability
> 公式支持 LaTeX 渲染，例题沿用 PPT 原始案例，中英对照

## 目录 Table of Contents

1. 机器学习引入概率论：不确定性 | Motivation: Probability in Machine Learning
2. 概率基本法则 | The Rules of Probability
3. 概率密度 | Probability Densities
4. 期望、方差、协方差 | Expectation, Variance and Covariance
5. 高斯分布 | The Gaussian Distribution
6. 密度的变量变换 | Transformation of Densities
7. 信息论 | Information Theory

---

# 1 机器学习引入概率论：不确定性 | Motivation: Probability in Machine Learning

传统确定性机器学习模型 | Deterministic view of machine learning

$$x \to \hat{y}$$

- $x$：观测特征 observed features
- $\hat y$：点预测 point-wise prediction

概率视角机器学习模型 | Probabilistic view

$$x \to p(y \mid x)$$

> 模型不只是输出决策，而是量化决策的不确定性。
> The model is not merely making a decision; it is quantifying uncertainty about that decision.

## 两类不确定性 Two Sources of Uncertainty

### 认知不确定性 Epistemic Uncertainty

- 中文：由知识有限带来的不确定性；可通过增加训练数据降低。
- English: Uncertainty due to limited knowledge; can generally be reduced by collecting more data.
- 成因 Cause：有限训练数据 finite training data；模型学习不充分 imperfectly learned model
- 通俗理解：**我们了解得还不够多 We do not know enough yet.**

### 偶然不确定性 Aleatoric Uncertainty

- 中文：来自数据本身固有波动、观测不完备；即使有无穷多训练数据依然存在。
- English: Uncertainty arising from intrinsic variability or incomplete observations; may remain even with unlimited training data.
- 成因 Cause：测量噪声 noisy measurements；存在未观测的相关变量 unobserved relevant variables
- 通俗理解：**观测输入没有包含全部信息 The observed input does not contain all the information.**

> 案例：脑部肿瘤 MRI 检测 Brain-Tumor Example
> 仅使用图像：$p(\text{brain tumor} \mid \text{image})$ 仍有很大不确定性
> 增加活检信息：$p(\text{brain tumor} \mid \text{image}, \text{biopsy})$，不确定性大幅下降
> 更丰富的观测可以降低偶然不确定性。Richer information can reduce uncertainty.

> 关键区分 Key distinction
> 对同一批有限特征做更多观测，**无法消除偶然不确定性**；
> 但是收集更多维度、不同类型测量，有时可以降低偶然不确定性。

## 点预测 vs 分布预测 Point Prediction vs. Distribution Prediction

- 点预测 Point prediction：$\hat y = 10$，**无法提供任何不确定信息**
- 概率分布预测 Distribution prediction：$Y \mid X = x \sim \mathcal{N}(10, 2^2)$
  - 既给出中心预测值 the central prediction
  - 同时给出预测的不确定程度 how uncertain that prediction is

概率机器学习核心对象：条件分布 $p(y \mid x)$

## 概率在机器学习各处的应用 Probability Appears Across Machine Learning

1. 分类 Classification：$P(Y = k \mid X = x)$，描述类别归属的不确定性 uncertainty about class membership
2. 回归 Regression：$p(y \mid x)$，描述连续输出的不确定性 uncertainty about a continuous outcome
3. 生成建模 Generative modeling：$p(x)$ or $p(x \mid z)$，描述数据生成方式 how data are generated
4. 参数不确定性 Parameter uncertainty：贝叶斯机器学习，参数后验 $p(\theta \mid \mathcal{D})$
5. 预测 Predictive distribution：结合参数与观测的不确定性，$p(y_{\text{new}} \mid x_{\text{new}}, \mathcal{D})$

> 概率的作用 Why Probability?
> 概率提供一套自洽框架：
> 1. 表示不确定性 representing uncertainty
> 2. 融合不同来源信息 combining different sources of information
> 3. 获取新信息后更新不确定性 updating uncertainty when new information becomes available
> 4. 信息不完备时做预测与决策 making predictions and decisions when information is incomplete
> 概率是描述不确定性的数学语言 Probability is the mathematical language of uncertainty.

---

# 2 概率基本法则 | The Rules of Probability

> PPT 案例：癌症筛查医学示例 A Medical Screening Example
> $p(\text{cancer}) = 0.01$
> - 无癌症人群，3% 检测阳性 false-positive rate = 0.03
> - 患癌人群，10% 检测阴性 false-negative rate = 0.10

定义随机变量：

$$C = \begin{cases} 1, & \text{cancer 患癌} \\ 0, & \text{no cancer 无癌症} \end{cases}, \quad T = \begin{cases} 1, & \text{positive test 检测阳性} \\ 0, & \text{negative test 检测阴性} \end{cases}$$

已知条件：

$$p(C=1) = 0.01,\quad p(C=0) = 0.99$$

$$p(T=1 \mid C=1) = 0.90,\quad p(T=0 \mid C=1) = 0.10$$

$$p(T=1 \mid C=0) = 0.03,\quad p(T=0 \mid C=0) = 0.97$$

**问题 1**：随机抽一个人，检测阳性的总概率？
Question 1: Probability that the test result is positive for a randomly selected person?

$$\begin{aligned} p(T=1) &= p(T=1 \mid C=1)\,p(C=1) + p(T=1 \mid C=0)\,p(C=0) \\ &= 0.90 \times 0.01 + 0.03 \times 0.99 = 0.0387 \end{aligned}$$

**问题 2**：检测结果为阳性时，此人实际患癌的概率？
Question 2: If test positive, probability of actually having cancer?

$$\begin{aligned} p(C=1 \mid T=1) &= \frac{p(T=1 \mid C=1)\,p(C=1)}{p(T=1)} \\ &= \frac{0.90 \times 0.01}{0.0387} = \frac{90}{387} \approx 0.23 \end{aligned}$$

> 检测阳性，实际患癌概率仅约 23%。可以用 10000 人列联表直观理解。

| | T=1 阳性 | T=0 阴性 | Total |
|---|---|---|---|
| C=1 患癌 | 90 | 10 | 100 |
| C=0 无癌 | 297 | 9603 | 9900 |
| Total | 387 | 9613 | 10000 |

> - 先验 Prior：观测之前 $p(C)$
> - 后验 Posterior：观测检测结果之后更新得到 $p(C \mid T)$
> 贝叶斯定理将先验知识与新证据结合 Bayes' theorem combines prior information with new evidence.

## 离散随机变量基础定义 Basic definition for discrete RV

- 联合概率 Joint probability $p(X, Y)$：X 与 Y 同时发生的概率 probability of X and Y
- 条件概率 Conditional probability $p(Y \mid X)$：已知 X 发生，Y 发生的概率 probability of Y given X
- 边缘概率 Marginal probability $p(X)$：消去 Y 变量后 X 单独的概率

### 求和规则 Sum Rule

$$p(X) = \sum_{Y} p(X, Y)$$

> 对不需要的变量做求和，得到边缘分布 Sum over alternatives.

### 乘积规则 Product Rule

$$p(X, Y) = p(Y \mid X)\,p(X)$$

> 沿着条件做乘法 Multiply along conditions.

### 贝叶斯定理 Bayes' Theorem

由乘积规则变形：

$$p(Y \mid X) = \frac{p(X \mid Y)\,p(Y)}{p(X)}$$

分母归一化常数，使用求和规则展开：

$$p(X) = \sum_{Y'} p(X \mid Y')\,p(Y')$$

### 独立变量 Independent Variables

X, Y 独立的充要条件：

$$p(X, Y) = p(X)\,p(Y) \iff p(Y \mid X) = p(Y)$$

> 若测试 T 和癌症 C 互相独立，则 $p(C \mid T) = p(C)$，检测完全不能提供信息，测试无效。

> ⚠️ 重要区分：独立一定协方差为 0；协方差为 0 **不一定独立**。
> Important distinction: Independence implies zero covariance, but zero covariance does not generally imply independence.

---

# 3 概率密度 | Probability Densities

离散变量 Discrete RV：可以讨论单点概率 $P(X = x)$。
连续变量 Continuous RV：单点概率 $P(X = x) = 0$；概率定义在区间上 Probability assigned to interval.

## 概率密度函数 PDF $p(x)$

小区间近似 Approximation for small width $\Delta$:

$$P(x < X < x + \Delta) \approx p(x)\,\Delta$$

区间 $(a, b)$ 的概率 Probability over interval:

$$P(a < X < b) = \int_{a}^{b} p(x)\,dx$$

合法 PDF 的两个条件 Valid probability density:

$$p(x) \ge 0,\quad \int_{-\infty}^{+\infty} p(x)\,dx = 1$$

> ⚠️ 重点提醒 Important note：密度值可以大于 1！只有积分之后的概率必须落在 $[0, 1]$。
> Density is not probability. The value $p(x)$ may exceed 1. Only integrated probability must lie between 0 and 1.

## 累积分布函数 CDF Cumulative Distribution Function

$$P(z) = P(X \le z) = \int_{-\infty}^{z} p(x)\,dx$$

若 CDF 可导 If P is differentiable：

$$p(x) = \frac{dP(x)}{dx}$$

## 混合分布（离散+连续）Mixed joint density

C 离散，X 连续 Discrete C, continuous X

$$p(c, x) = P(C = c)\,p(x \mid C = c)$$

> 经验口诀 Rule of thumb：对离散变量求和 sum over discrete variables，对连续变量积分 integrate over continuous variables.

## 连续版本三大概率规则 Probability rules for continuous variables

$$\begin{aligned} \text{求和规则 Sum rule}:&\quad p(x) = \int p(x, y)\,dy \\ \text{乘积规则 Product rule}:&\quad p(x, y) = p(y \mid x)\,p(x) \\ \text{贝叶斯定理 Bayes' theorem}:&\quad p(y \mid x) = \frac{p(x \mid y)\,p(y)}{\int p(x \mid y')\,p(y')\,dy'} \end{aligned}$$

## 常用密度 Common densities

### 1. 均匀分布 Uniform distribution

$$p(x) = \begin{cases} \dfrac{1}{d - c}, & c < x < d \\[6pt] 0, & \text{otherwise} \end{cases}$$

### 2. 指数分布 Exponential distribution

常用于非负量，等待时间 nonnegative quantities such as waiting times

$$p(x \mid \lambda) = \lambda e^{-\lambda x},\quad x \ge 0$$

### 3. 拉普拉斯分布 Laplace distribution

尖峰重尾 sharp peak and heavier tails than Gaussian

$$p(x \mid \mu, \gamma) = \frac{1}{2\gamma}\exp\left(-\frac{|x - \mu|}{\gamma}\right)$$

### 4. 狄拉克 δ 函数 Dirac-delta function

全部概率集中在单点 all probability mass concentrated at $\mu$

$$p(x \mid \mu) = \delta(x - \mu),\quad \int_{-\infty}^{\infty} \delta(x - \mu)\,dx = 1$$

筛选性质 Sifting property：$\displaystyle\int f(x)\,\delta(x - \mu)\,dx = f(\mu)$

### 5. 经验分布 Empirical distribution

样本 $\mathcal{D} = \{x_1, \dots, x_N\}$

$$p(x \mid \mathcal{D}) = \frac{1}{N}\sum_{n=1}^{N} \delta(x - x_n)$$

每个观测分配权重 $1/N$，不需要参数假设 places weight $1/N$ at each observed data point.

> PPT 快速练习题 Quick Check
> $$p(x) = \begin{cases} 2x, & 0 < x < 1 \\ 0, & \text{otherwise} \end{cases}$$
> $$P\left(X < \frac{1}{2}\right) = \int_{0}^{1/2} 2x\,dx = \frac{1}{4},\quad \mathbb{E}[X] = \int_{0}^{1} x \cdot 2x\,dx = \frac{2}{3}$$

---

# 4 期望、方差、协方差 | Expectation, Variance and Covariance

## 期望 Expectation

概率加权平均 probability-weighted average

离散 Discrete：

$$\mathbb{E}[f(X)] = \sum_x p(x)\,f(x)$$

连续 Continuous：

$$\mathbb{E}[f(X)] = \int p(x)\,f(x)\,dx$$

样本估计（大数定律）Estimating expectation from data

$$\mathbb{E}[f(X)] \approx \frac{1}{N}\sum_{n=1}^{N} f(x_n)$$

当样本趋向无穷大时等式成立，approximation exact as $N \to \infty$.

条件期望 Conditional expectation

$$\mathbb{E}_{X}[f(X) \mid Y = y] = \int p(x \mid y)\,f(x)\,dx$$

## 方差 Variance

衡量围绕均值的离散程度 spread around the mean

$$\text{Var}[f(X)] = \mathbb{E}\left[\big(f(X) - \mathbb{E}[f(X)]\big)^2\right]$$

计算恒等式 Computational identity：

$$\text{Var}[f(X)] = \mathbb{E}\left[f(X)^2\right] - \big(\mathbb{E}[f(X)]\big)^2$$

## 协方差 Covariance

衡量变量共同变化趋势 tendency to vary together

$$\text{Cov}[X, Y] = \mathbb{E}\left[(X - \mathbb{E}[X])(Y - \mathbb{E}[Y])\right] = \mathbb{E}[XY] - \mathbb{E}[X]\,\mathbb{E}[Y]$$

- $\text{Cov} > 0$：两者倾向同时变大；large values tend to occur together
- $\text{Cov} < 0$：一个变大另一个倾向变小；one large when other small
- $\text{Cov} = 0$：无线性共变，不代表独立 no linear co-variation, not imply independence

## 协方差矩阵 Covariance Matrices

针对随机向量 for random vectors

$$\text{Cov}[\boldsymbol{X}] = \mathbb{E}\left[(\boldsymbol{X} - \mathbb{E}[\boldsymbol{X}])(\boldsymbol{X} - \mathbb{E}[\boldsymbol{X}])^\top\right]$$

对角线 diagonal：各分量方差 component variances；非对角线 off-diagonal：两两协方差 pairwise covariances.

---

# 5 高斯分布 | The Gaussian Distribution

一维高斯概率密度 One-dimensional Gaussian density

$$\mathcal{N}\left(x \mid \mu, \sigma^2\right) = \frac{1}{\sqrt{2\pi\sigma^2}} \exp\left\{-\frac{(x - \mu)^2}{2\sigma^2}\right\}$$

参数 Parameters

- $\mu$：均值 mean
- $\sigma^2$：方差 variance
- $\sigma = \sqrt{\sigma^2}$：标准差 standard deviation
- $\beta = \dfrac{1}{\sigma^2}$：精度 precision

高斯分布性质 Gaussian properties

$$\mathbb{E}[X] = \mu,\quad \text{Var}(X) = \sigma^2$$

高斯分布满足：均值 = 中位数 = 众数 mean = median = mode = $\mu$

## 高斯极大似然估计 Maximum Likelihood Estimation for Gaussian

i.i.d 样本 $\mathcal{D} = \{x_1, \dots, x_N\},\ x_n \sim \mathcal{N}(\mu, \sigma^2)$

似然函数 Likelihood function：

$$p(\mathcal{D} \mid \mu, \sigma^2) = \prod_{n=1}^{N} \mathcal{N}(x_n \mid \mu, \sigma^2)$$

对数似然 Log-likelihood（乘积转求和，避免数值下溢）

$$\log p(\mathcal{D} \mid \mu, \sigma^2) = -\frac{1}{2\sigma^2}\sum_{n=1}^{N} (x_n - \mu)^2 - \frac{N}{2}\log\sigma^2 - \frac{N}{2}\log(2\pi)$$

MLE 估计结果：

$$\mu_{\text{ML}} = \frac{1}{N}\sum_{n=1}^{N} x_n \quad (\text{样本均值 sample mean，无偏 unbiased})$$

$$\sigma^2_{\text{ML}} = \frac{1}{N}\sum_{n=1}^{N} (x_n - \mu_{\text{ML}})^2$$

> ⚠️ MLE 方差是有偏估计 MLE variance is biased
> $$\mathbb{E}\left[\sigma^2_{\text{ML}}\right] = \frac{N - 1}{N}\sigma^2$$
> 无偏样本方差 Unbiased sample variance
> $$\tilde{\sigma}^2 = \frac{1}{N - 1}\sum_{n=1}^{N} (x_n - \mu_{\text{ML}})^2$$

> PPT 快速练习题 Quick Check
> 样本 $\mathcal{D} = \{1, 2, 3\}$
> $$\mu_{\text{ML}} = \frac{1 + 2 + 3}{3} = 2$$
> $$\sigma^2_{\text{ML}} = \frac{1}{3}\left[(1-2)^2 + (2-2)^2 + (3-2)^2\right] = \frac{2}{3}$$
> 无偏方差 unbiased variance：$\tilde{\sigma}^2 = \dfrac{2}{3 - 1} = 1$

## 概率视角回归 Probabilistic view of regression

假设输出带有高斯噪声 target with Gaussian noise

$$p(t \mid x, w, \sigma^2) = \mathcal{N}\big(t \mid y(x, w), \sigma^2\big)$$

等价形式 Equivalent form

$$t = y(x, w) + \varepsilon,\quad \varepsilon \sim \mathcal{N}(0, \sigma^2)$$

> 重要结论 Important conclusion：平方误差损失不是人为选择；它等价于高斯噪声假设下的负对数似然。
> Sum-of-squares loss is not arbitrary choice: it is negative log-likelihood implied by Gaussian observation noise.

新的预测为完整分布 New prediction is distribution:

$$p(t \mid x, w_{\text{ML}}, \sigma^2_{\text{ML}}) = \mathcal{N}\big(t \mid y(x, w_{\text{ML}}), \sigma^2_{\text{ML}}\big)$$

均值给出点预测，方差量化预测不确定性。

---

# 6 密度的变量变换 | Transformation of Densities

> 背景：标准化流 Normalizing Flow 生成模型数学基础 foundation of normalizing-flow models
> 已知 X 的密度，变换 $x = g(y)$，求 Y 的密度。
> ⚠️ 密度不能直接代入函数，变换拉伸压缩区间，需要雅可比修正因子保证概率质量守恒。

## 一维变量替换公式 One-dimensional change-of-variables

$$p_y(y) = p_x\big(g(y)\big)\left|\frac{dg(y)}{dy}\right|$$

- $|\cdot|$ 绝对值：保证因子非负 absolute value ensures non-negative factor
- 导数代表局部区间缩放倍数 derivative measures local change in scale

使用逆变换的等价写法 Equivalent form with inverse transform $y = f(x),\ x = f^{-1}(y)$

$$p_y(y) = p_x\left(f^{-1}(y)\right)\left|\frac{d\,f^{-1}(y)}{dy}\right|$$

> PPT 快速练习题 Quick Check
> $X \sim \text{Uniform}(0, 3),\ Y = \dfrac{X}{3}$，求 Y 的密度。
> $x = 3y,\ \left|\dfrac{dx}{dy}\right| = 3$
> $$p_y(y) = p_x(3y) \cdot \left|\frac{dx}{dy}\right| = \frac{1}{3} \times 3 = 1,\quad 0 < y < 1$$
> 得到 $Y \sim \text{Uniform}(0, 1)$。

> 直观理解 intuition：
> - 变换拉伸区间 → 密度下降 stretch interval → lower density
> - 变换压缩区间 → 密度上升 compress interval → higher density

## 多维变量替换 Multivariate change-of-variables

$\boldsymbol{x} = g(\boldsymbol{y}),\ \boldsymbol{x}, \boldsymbol{y} \in \mathbb{R}^D$

$$p_{\boldsymbol{y}}(\boldsymbol{y}) = p_{\boldsymbol{x}}\big(g(\boldsymbol{y})\big) \cdot \big|\det J_g(\boldsymbol{y})\big|$$

$J$ 雅可比矩阵 Jacobian matrix，全部一阶偏导数：

$$J = \frac{\partial \boldsymbol{x}}{\partial \boldsymbol{y}^\top} = \begin{pmatrix} \dfrac{\partial g_1}{\partial y_1} & \dots & \dfrac{\partial g_1}{\partial y_D} \\ \vdots & \ddots & \vdots \\ \dfrac{\partial g_D}{\partial y_1} & \dots & \dfrac{\partial g_D}{\partial y_D} \end{pmatrix}$$

$|\det J|$：局部体积缩放倍数 absolute determinant measures local volume change.

## 标准化流 Normalizing Flows

一连串可逆变换 sequence of invertible transformations:

$$z_0 \to z_1 \to \cdots \to z_K = x$$

每一步 $z_k = f_k(z_{k-1})$

$$\log p_k(z_k) = \log p_{k-1}(z_{k-1}) - \log\left|\det \frac{\partial f_k}{\partial z_{k-1}^\top}\right|$$

累积得到最终对数密度：

$$\log p_x(x) = \log p_0(z_0) - \sum_{k=1}^{K} \log\left|\det J_k\right|$$

---

# 7 信息论 | Information Theory

> 信息论量化不确定性被消除时获得多少信息。Quantifies how much information is revealed when uncertainty is reduced.

## 自信息 Information Content

观测事件 $x$ 得到的信息量：

$$h(x) = -\log_b p(x)$$

- $\log_2$，单位 bit 比特
- $\ln$，单位 nat 纳特

性质 Properties

1. 低概率事件，信息量更大；Unlikely event provides more information
2. 确定事件信息量为 0；Certain event provides no information
3. 独立事件信息量可加；Independent events provide additive information

## 离散香农熵 Discrete Shannon Entropy

熵是随机变量的期望自信息 entropy is expected information content

$$H[X] = -\sum_{x} p(x)\log p(x)$$

约定 $0\log 0 = 0$。熵代表观测 X 之前的平均不确定性 average uncertainty before observing X。

伯努利分布熵 Bernoulli variable $X \sim \text{Bernoulli}(\mu)$

$$H[X] = -\mu\log_2\mu - (1 - \mu)\log_2(1 - \mu)$$

当 $\mu = 0.5$，熵取最大值，不确定性最高。

## 微分熵 Differential Entropy（连续随机变量）

$$H[X] = -\int p(x)\log p(x)\,dx$$

⚠️ 微分熵特点 Important properties of differential entropy

1. 可以为负数 can be negative
2. 依赖坐标尺度 depends on measurement scale
3. 不能直接代表编码比特 does not equal number of bits

> 重要定理 Theorem：给定均值与方差，**高斯分布拥有最大微分熵**。
> Among all continuous distributions with specified mean and variance, Gaussian has largest differential entropy.

$$H[\mathcal{N}(\mu, \sigma^2)] = \frac{1}{2}\big(1 + \ln(2\pi\sigma^2)\big) = \frac{1}{2}\ln\left(2\pi e\,\sigma^2\right)$$

## 交叉熵与 KL 散度 Cross-Entropy & Kullback-Leibler divergence

$p(x)$ 真实分布 true distribution，$q(x)$ 近似分布 approximation

交叉熵 Cross-entropy

$$H(p, q) = -\int p(x)\log q(x)\,dx$$

KL 散度（相对熵）KL-divergence

$$D_{\text{KL}}(p \| q) = \int p(x)\log\frac{p(x)}{q(x)}\,dx = H(p, q) - H(p)$$

KL 核心性质：

1. $D_{\text{KL}}(p \| q) \ge 0$；当且仅当 $p(x) = q(x)$ 几乎处处成立取等。
2. **KL 非对称 $D_{\text{KL}}(p \| q) \neq D_{\text{KL}}(q \| p)$，不是距离 not a metric**。
3. 极大似然等价最小化前向 KL 散度 Maximum likelihood minimizes $D_{\text{KL}}(p_{\text{data}} \| q_\theta)$。

## 条件熵、联合熵 Conditional Entropy & Joint Entropy

$$H[Y \mid X] = -\iint p(x, y)\log p(y \mid x)\,dx\,dy$$

熵链式法则 Chain rule for entropy

$$H[X, Y] = H[X] + H[Y \mid X]$$

> X, Y 联合不确定性 = X 的不确定性 + 已知 X 条件下 Y 剩余的不确定性。

## 互信息 Mutual Information

衡量观测 X 之后，Y 不确定性下降多少，捕捉统计依赖（包含非线性依赖）

$$I[X; Y] = D_{\text{KL}}\big(p(x, y) \,\|\, p(x)p(y)\big) = \iint p(x, y)\log\frac{p(x, y)}{p(x)p(y)}\,dx\,dy$$

等价形式 Equivalent expressions:

$$I[X; Y] = H[X] - H[X \mid Y] = H[Y] - H[Y \mid X]$$

- $I[X; Y] \ge 0$
- 当且仅当 X, Y 统计独立，$I[X; Y] = 0$

> PPT 案例：
> 1. 完全观测 Perfect observation：$Y = X$，$I[X; Y] = 1\ \text{bit}$
> 2. 完全独立 Independent observation：Y 独立于 X，$I[X; Y] = 0$

> 互信息优点：可以捕捉非线性依赖，不限于线性相关。
> Mutual information depends on statistical dependence, including nonlinear dependence. It is not restricted to linear association.

---

# 附录：易混淆概念速查表 Appendix: Quick reference table

| 概念 Concept | 核心要点 Key takeaway |
|---|---|
| 概率 vs 概率密度 Probability vs PDF | 概率 $0 \le P \le 1$；密度可大于 1，只有积分得到概率 |
| 认知/偶然不确定性 Epistemic / Aleatoric | 认知：增加数据可降低；偶然：任务固有噪声，靠增加特征维度缓解 |
| MLE 高斯方差有偏 Biased Gaussian MLE variance | 估计均值消耗 1 自由度；无偏估计分母使用 $N - 1$ |
| 变量变换 Change-of-variable | 必须乘雅可比行列式绝对值；忽略则破坏概率归一 |
| KL 散度 Kullback-Leibler | 非对称，不是距离；极大似然等价最小化正向 KL |
| 微分熵 Differential entropy | 可负数，依赖坐标尺度，不可直接当作编码比特；离散熵恒 $\ge 0$ |
