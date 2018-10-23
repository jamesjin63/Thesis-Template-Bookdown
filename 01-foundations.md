
# 基础知识 {#prepare}

作为第 \@ref(models) 章统计模型和第 \@ref(algorithms) 章参数估计的知识准备，本章给出主要的知识点。第 \@ref(sec:exp) 节首先介绍指数族的一般形式，包含各成分的定义，特别给出正态分布、二项分布和泊松分布情形下均值函数、联系函数和方差函数等特征量。第 \@ref(sec:lse) 节给出线性模型下，设计矩阵保持正定时的最小二乘估计和加权最小二乘估计。第 \@ref(sec:def-mle) 节给出一般的极大似然估计的定义，相合性，以及在一定条件下的渐进正态性。第 \@ref(sec:stationary-gaussian-process) 节给出平稳高斯过程的定义，均方连续性和可微性的定义，以及判断可微性的一个充要条件。第 \@ref(sec:Laplace-approximation) 节介绍了拉普拉斯近似的一般方法。第 \@ref(sec:bayes-prior) 介绍了先验、后验分布和 Jeffreys 无信息先验分布。第 \@ref(sec:stan-samplers) 节首先从 Stan 的发展、内置算法设置以及与同类软件的比较等三方面介绍，然后以数据集 Eight Schools 为例子介绍 Stan 的使用，为空间广义线性混合效应模型的 Stan 实现作铺垫。

## 指数族 {#sec:exp}

一般地，样本 $\mathbf{Y}$ 的分布服从指数族，即形如
\begin{equation}
f_{Y}(y;\theta,\phi) = \exp\big\{ \big(y\theta - b(\theta) \big)/a(\phi) + c(y,\phi) \big\}
(\#eq:common-exponential-family)
\end{equation}
\noindent 其中，$a(\cdot),b(\cdot),c(\cdot)$ 是某些特定的函数。如果 $\phi$ 已知，这是一个含有典则参数 $\theta$ 的指数族模型，如果 $\phi$ 未知，它可能是含有两个参数的指数族。对于正态分布
\begin{equation}
\begin{aligned}
f_{Y}(y;\theta,\phi) & = \frac{1}{\sqrt{2\pi\sigma^2}} \exp\{-\frac{(y - \mu)^2}{2\sigma^2}  \}  \\
 & = \exp\big \{ (y\mu - \mu^2/2)/\sigma^2 - \frac{1}{2}\big(y^2/\sigma^2 + \log(2\pi\sigma^2)\big) \big\}
\end{aligned} (\#eq:normal-distribution)
\end{equation}
\noindent 通过与 \@ref(eq:common-exponential-family) 式对比，可知 $\theta = \mu$，$\phi = \sigma^2$，并且有
\[
a(\phi) = \phi, \quad b(\theta) = \theta^2/2, \quad c(y,\phi) = - \frac{1}{2}\{ y^2/\sigma^2 + \log(2\pi\sigma^2) \} 
\]
\noindent 记 $l(\theta,\phi;y) = \log f_{Y}(y;\theta,\phi)$ 为给定样本点 $y$ 的情况下，关于 $\theta$ 和 $\phi$ 的对数似然函数。样本 $Y$ 的均值和方差具有如下关系
\begin{equation}
\mathsf{E}\big( \frac{\partial l}{\partial \theta} \big) = 0
(\#eq:mean-log-lik)
\end{equation}
\noindent 和
\begin{equation}
\mathsf{E}\big( \frac{\partial^2 l}{\partial \theta^2} \big) + \mathsf{E}\big(\frac{\partial l}{\partial \theta}\big)^2  = 0
(\#eq:variance-log-lik)
\end{equation}
\noindent 从 \@ref(eq:common-exponential-family) 式知
\[ l(\theta,\phi;y) = {y\theta - b(\theta)}/a(\phi) + c(y,\phi) \]
\noindent 因此，
\begin{equation}
\begin{aligned}
\frac{\partial l}{\partial \theta} & = {y - b'(\theta)}/a(\phi)  \\
\frac{\partial^2 l}{\partial \theta^2}  & = - b''(\theta)/a(\phi)
\end{aligned} (\#eq:partial-log-lik)
\end{equation}
\noindent 从 \@ref(eq:mean-log-lik) 式和 \@ref(eq:partial-log-lik)，可以得出
\[ 
0 = \mathsf{E}\big( \frac{\partial l}{\partial \theta} \big) = \big\{ \mu - b'(\theta) \big\}/a(\phi)
\]
\noindent 所以
\[ \mathsf{E}(Y) = \mu = b'(\theta) \]
\noindent 根据 \@ref(eq:variance-log-lik) 式和 \@ref(eq:partial-log-lik) 式，可得
\[ 0 = - \frac{b''(\theta)}{a(\phi)} + \frac{\mathsf{Var}(Y)}{a^2(\phi)} \]
\noindent 所以
\[ \mathsf{Var}(Y) = b''(\theta)a(\phi) \]
可见，$Y$ 的方差是两个函数的乘积，一个是 $b''(\theta)$， 它仅仅依赖典则参数，叫做方差函数，另一个是 $a(\phi)$，它独立于 $\theta$，仅仅依赖 $\phi$，方差函数可以看作是 $\mu$ 的函数，记作 $V(\mu)$。

函数 $a(\phi)$ 通常形如
\[ a(\phi) = \phi/w \]
\noindent 其中 $\phi$ 可由 $\sigma^2$ 表示，故而也叫做发散参数 (dispersion parameter)，是一个与样本观察值相关的常数，$w$ 是已知的权重，随样本观察值变化。对正态分布模型而言，$w$ 的分量是 $m$ 个相互独立的样本观察值的均值，有 $a(\phi) = \sigma^2/m$，所以，$w = m$。

根据 \@ref(eq:common-exponential-family)式，正态、泊松和二项分布的特征见表 \@ref(tab:common-characteristics)，其它常见分布见 Peter McCullagh 等 (1989年) [@McCullagh1989]。

Table: (\#tab:common-characteristics) 指数族内常见的一元分布的共同特征及符号表示^[(ref:footnote-tab-common-characteristics)] 

|                   |      正态分布      |      泊松分布      |      二项分布      |
| :---------------- | :----------------: | :----------------: | :----------------: | 
|  记号             | $\mathcal{N}(\mu,\sigma^2)$  |       $\mathrm{Poisson}(\mu)$     |     $B(m,\pi)/m$   |
|  $y$ 取值范围     | $(-\infty,\infty)$ |     $0(1)\infty$   |  $\frac{0(1)m}{m}$ |
|  $\phi$           | $\phi = \sigma^2$  |         $1$        |        $1/m$       |
|  $b(\theta)$      | $\theta^2/2$       |  $\exp(\theta)$    |$\log(1+e^{\theta})$|
| $c(y;\theta)$     | $-\frac{1}{2}\big( \frac{y^2}{\phi} + \log(2\pi\phi) \big)$  |   $-\log(y!)$    | $\log\binom{m}{my}$ |   
| $\mu(\theta) = \mathsf{E}(Y;\theta)$  |  $\theta$   | $\exp(\theta)$ |  $e^{\theta}/(1+e^{\theta})$ |
| 联系函数：$\theta(\mu)$   |  identity |    log      |     logit      |
| 方差函数：$V(\mu)$        |   1       |   $\mu$     |  $\mu(1-\mu)$  |

(ref:footnote-tab-common-characteristics) 均值参数用 $\mu$ 表示，二项分布里用 $\pi$ 表示；典则参数用 $\theta$ 表示，定义见 \@ref(eq:common-exponential-family) 式，$\mu$ 和 $\theta$ 的关系在表 \@ref(tab:common-characteristics) 的第 6 和第 7 行给出。 

## 最小二乘估计 {#sec:lse}

考虑如下线性模型的最小二乘估计
\begin{equation}
\mathsf{E}\mathbf{Y} = \mathbf{X}\boldsymbol{\beta}; \mathsf{Var}(\mathbf{Y}) = \sigma^2 \mathbf{I}_{n} (\#eq:linear-models)
\end{equation}
\noindent 其中， $\mathbf{Y}$ 为 $n \times 1$ 维观测向量， $\mathbf{X}$ 为已知的 $n \times p (p \leq n)$ 阶设计矩阵，$\boldsymbol{\beta}$ 为 $p \times 1$ 维未知参数，$\sigma^2$ 未知，$\mathbf{I}_{n}$ 为 $n$ 阶单位阵。
\BeginKnitrBlock{definition}\iffalse{-91-26368-23567-20108-20056-20272-35745-93-}\fi{}<div class="definition"><span class="definition" id="def:least-squares-estimate"><strong>(\#def:least-squares-estimate)  \iffalse (最小二乘估计) \fi{} </strong></span>在模型 \@ref(eq:linear-models) 中，如果
\begin{equation}
(\mathbf{Y} - \mathbf{X}\hat{\boldsymbol{\beta}})^{\top}(\mathbf{Y} - \mathbf{X}\hat{\boldsymbol{\beta}}) = \min_{\beta}(\mathbf{Y} - \mathbf{X}\boldsymbol{\beta})^{\top}(\mathbf{Y} - \mathbf{X}\boldsymbol{\beta}) (\#eq:least-squares)
\end{equation}
\noindent 则称 $\hat{\boldsymbol{\beta}}$ 为 $\boldsymbol{\beta}$ 的最小二乘估计 (Least Squares Estimate，简称 LSE)。</div>\EndKnitrBlock{definition}
\BeginKnitrBlock{theorem}\iffalse{-91-26368-23567-20108-20056-20272-35745-93-}\fi{}<div class="theorem"><span class="theorem" id="thm:unbiased"><strong>(\#thm:unbiased)  \iffalse (最小二乘估计) \fi{} </strong></span>若模型  \@ref(eq:linear-models) 中的 $\mathbf{X}$ 是列满秩的矩阵，则 $\boldsymbol{\beta}$ 的最小二乘估计为
\[
\hat{\boldsymbol{\beta}}_{LS} = ( \mathbf{X}^{\top}\mathbf{X} )^{-1}\mathbf{X}^{\top} \mathbf{Y}, \quad  \mathsf{Var}(\hat{\boldsymbol{\beta}}_{LS}) = \sigma^2 (\mathbf{X}^{\top}\mathbf{X})^{-1}  
\]
\noindent $\sigma^2$ 的最小二乘估计为
\[
\hat{\sigma^2}_{LS} = (\mathbf{Y} - \mathbf{X}\hat{\boldsymbol{\beta}}_{LS})^{\top}(\mathbf{Y} - \mathbf{X}\hat{\boldsymbol{\beta}}_{LS})/(n - p)
\]
若将模型  \@ref(eq:linear-models) 的条件 $\mathsf{Var}(\mathbf{Y}) = \sigma^2 \mathbf{I}_{n}$ 改为 $\mathsf{Var}(\mathbf{Y}) = \sigma^2 \mathbf{G}$， $G(>0)$ 为已知正定阵，则$\boldsymbol{\beta}$ 的最小二乘估计为
\[
\tilde{\boldsymbol{\beta}}_{LS} = ( \mathbf{X}^{\top} G^{-1} \mathbf{X})^{-1} \mathbf{X}^{\top} G^{-1} \mathbf{Y} 
\]
\noindent 称 $\tilde{\boldsymbol{\beta}}_{LS}$ 为广义最小二乘估计 (Generalized Least Squares Estimate，简称 GLSE)，特别地，当 $G = \mathrm{diag}(\sigma^2_{1},\ldots,\sigma^2_{n})$，$\sigma^2_{i},i = 1,\ldots,n$ 已知时，称 $\tilde{\boldsymbol{\beta}}_{LS}$ 为加权最小二乘估计 (Weighted Least Squares Estimate，简称 WLSE)[@wang2004]</div>\EndKnitrBlock{theorem}


## 极大似然估计 {#sec:def-mle}

\BeginKnitrBlock{definition}\iffalse{-91-26497-22823-20284-28982-20272-35745-93-}\fi{}<div class="definition"><span class="definition" id="def:maximum-likelihood-estimate"><strong>(\#def:maximum-likelihood-estimate)  \iffalse (极大似然估计) \fi{} </strong></span>设 $p(\mathbf{x};\boldsymbol{\theta}),\boldsymbol{\theta} \in \boldsymbol{\Theta}$ 是 $(\mathbb{R}^n,\mathscr{P}_{\mathbb{R}^n})$ 上的一族联合密度函数，对给定的 $\mathbf{x}$，称
\[ L(\boldsymbol{\theta};\mathbf{x}) = kp(\mathbf{x};\boldsymbol{\theta}) \]
\noindent 为 $\boldsymbol{\theta}$ 的似然函数，其中 $k > 0$ 是不依赖于 $\boldsymbol{\theta}$ 的量，常取 $k=1$。进一步，若存在 $(\mathbb{R}^n,\mathscr{P}_{\mathbb{R}^n})$ 到 $(\boldsymbol{\Theta},\mathscr{P}_{\boldsymbol{\Theta}})$ 的统计量 $\hat{\boldsymbol{\theta}}(\mathbf{x})$ 使
\[ L(\hat{\boldsymbol{\theta}}(\mathbf{x});\mathbf{x}) = \sup_{\boldsymbol{\theta}} L(\boldsymbol{\theta};\mathbf{x}) \]
\noindent 则 $\hat{\boldsymbol{\theta}}(\mathbf{x})$ 称为 $\boldsymbol{\theta}$ 的一个极大似然估计(Maximum Likelihood Eestimate，简称 MLE)。</div>\EndKnitrBlock{definition}

概率密度函数很多可以写成具有指数函数的形式，如指数族，采用似然函数的对数通常更为简便。称
\[ l(\boldsymbol{\theta},\mathbf{x}) = \ln L(\boldsymbol{\theta},\mathbf{x}) \]
\noindent 为 $\boldsymbol{\theta}$ 的对数似然函数。对数变换是严格单调的，所以 $l(\boldsymbol{\theta},\mathbf{x})$ 与 $L(\boldsymbol{\theta},\mathbf{x})$ 的极大值是等价的。当 MLE 存在时，寻找 MLE 的常用方法是求导数。如果 $\hat{\boldsymbol{\theta}}(\mathbf{x})$ 是 $\boldsymbol{\Theta}$ 的内点，则 $\hat{\boldsymbol{\theta}}(\mathbf{x})$ 是下列似然方程组
\begin{equation}
\partial l(\boldsymbol{\theta},\mathbf{x})/ \partial \boldsymbol{\theta}_{i} = 0, \quad i = 1,\ldots, m (\#eq:likelihood-equations)
\end{equation}
\noindent 的解。$p(\mathbf{x};\boldsymbol{\theta})$ 属于指数族时，似然方程组 \@ref(eq:likelihood-equations) 的解唯一。

\BeginKnitrBlock{theorem}\iffalse{-91-30456-21512-24615-93-}\fi{}<div class="theorem"><span class="theorem" id="thm:consistency"><strong>(\#thm:consistency)  \iffalse (相合性) \fi{} </strong></span>设 $x_{1}, \ldots, x_{n}$ 是来自概率密度函数 $p(x;\theta)$ 的一个样本，叙述简单起见，考虑单参数情形，参数空间 $\boldsymbol{\Theta}$ 是一个开区间，$l(\theta;x) = \sum_{i=1}^{n}\ln p(x_{i};\theta)$。

若 $\ln (p;\theta)$ 在 $\boldsymbol{\Theta}$ 上可微，且 $p(x;\theta)$ 是可识别的（即 $\forall \theta_1 \neq \theta_2, \{x: p(x;\theta_1) \neq p(x; \theta_2)\}$ 不是零测集），则似然方程 \@ref(eq:likelihood-equations) 在 $n \to \infty$ 时，以概率 $1$ 有解，且此解关于 $\theta$ 是相合的。</div>\EndKnitrBlock{theorem}

\BeginKnitrBlock{theorem}\iffalse{-91-28176-36817-27491-24577-24615-93-}\fi{}<div class="theorem"><span class="theorem" id="thm:asymptotic-normality"><strong>(\#thm:asymptotic-normality)  \iffalse (渐近正态性) \fi{} </strong></span>假设 $\boldsymbol{\Theta}$ 为开区间，概率密度函数 $p(x;\theta), \theta \in \boldsymbol{\Theta}$ 满足

1. 在参数真值 $\theta_{0}$ 的邻域内，$\partial \ln p/\partial \theta, \partial^2 \ln p/\partial \theta^2, \partial^3 \ln p/\partial \theta^3$ 对所有的 $x$ 都存在；
2. 在参数真值 $\theta_{0}$ 的邻域内，$| \partial^3 \ln p/\partial \theta^3 | \leq H(x)$，且 $\mathsf{E}H(x) < \infty$；
3. 在参数真值 $\theta_{0}$ 处，

$$\mathsf{E}_{\theta_{0}} \big[ \frac{ p'(x,\theta_{0}) }{ p(x,\theta_{0}) } \big] = 0, \quad
\mathsf{E}_{\theta_{0}} \big[ \frac{ p''(x,\theta_{0}) }{ p(x,\theta_{0}) } \big] = 0, \quad
I(\theta_{0}) = \mathsf{E}_{\theta_{0}} \big[ \frac{ p'(x,\theta_{0}) }{ p(x,\theta_{0}) } \big]^{2} > 0$$
\noindent 其中撇号表示对 $\theta$ 的微分。记 $\hat{\theta}_{n}$ 为 $n \to \infty$ 时，似然方程组的相合解，则$\sqrt{n}(\hat{\theta}_{n} - \theta_{0}) \longrightarrow  \mathcal{N}(\mathbf{0},I^{-1}(\theta))$。</div>\EndKnitrBlock{theorem}

## 平稳高斯过程 {#sec:stationary-gaussian-process}

一般地，空间高斯过程 $\mathcal{S} = \{S(x),x\in\mathbb{R}^2\}$ 必须满足条件：任意给定一组空间位置 $x_1,x_2,\ldots,x_n, \forall x_{i} \in \mathbb{R}^2$， 每个位置上对应的随机变量 $S(x_i), i = 1,2,\ldots,n$ 的联合分布 $\mathcal{S} = \{S(x_1), S(x_2),\ldots,S(x_n)\}$ 是多元高斯分布，其由均值 $\mu(x) = \mathsf{E}[S(x)]$ 和协方差 $G_{ij} = \gamma(x_i,x_j) = \mathsf{Cov}\{S(x_i),S(x_j)\}$ 完全确定，即 $\mathcal{S} \sim \mathcal{N}(\mu_{S},G)$。

平稳空间高斯过程需要空间高斯过程满足平稳性条件：其一， $\mu(x) = \mu, \forall x \in \mathbb{R}^2$， 其二，自协方差函数 $\gamma(x_i,x_j) = \gamma(u),u=\|x_{i} - x_{j}\|$。 可见均值 $\mu$ 是一个常数， 而自协方差函数 $\gamma(x_i,x_j)$ 只与空间距离有关。 注意到平稳高斯过程 $\mathcal{S}$ 的方差是一个常数，即 $\sigma^2 = \gamma(0)$， 然后可以定义自相关函数 $\rho(u) = \gamma(u)/\sigma^2$， 并且 $\rho(u)$ 满足关于空间距离的对称性， $\rho(u) = \rho(-u)$， 因为对 $\forall u, \mathsf{Corr}\{S(x),S(x-u)\} = \mathsf{Corr}\{S(x-u), S(x)\} = \mathsf{Corr}\{S(x),S(x+u)\}$， 这里的第二个等式是根据平稳性得来的， 由协方差的定义不难验证。 在本论文中如果不特别说明， 平稳就指上述协方差意义下的平稳， 因为这种平稳性条件广泛应用于空间数据的统计建模。

不失一般性，给出一维空间下随机过程 $S(x)$ 的均方连续性和可微性定义。

\BeginKnitrBlock{definition}\iffalse{-91-36830-32493-24615-21644-21487-24494-24615-93-}\fi{}<div class="definition"><span class="definition" id="def:continuous-differentiable"><strong>(\#def:continuous-differentiable)  \iffalse (连续性和可微性) \fi{} </strong></span>随机过程 $S(x)$ 满足
\[ \lim_{h \to 0} \mathsf{E}\big[ \{S(x + h) - S(x)\}^{2} \big] = 0 \] 
\noindent 则称 $S(x)$ 是均方连续(mean-square continuous)的。随机过程 $S(x)$ 满足
\[ \lim_{h \to 0} \mathsf{E} \big[ \{ \frac{S(x+h) - S(x)}{h} - S'(x) \}^2 \big] = 0 \]
\noindent 则称 $S(x)$ 是均方可微(mean-square differentiable)的，并且 $S'(x)$ 就是均方意义下的一阶导数。如果 $S'(x)$ 是均方可微的，则 $S(x)$ 是二次均方可微的，随机过程 $S(x)$ 的高阶均方可微性可类似定义。M. S. Bartlett (1955 年) [@Bartlett1955] 得到如下重要结论</div>\EndKnitrBlock{definition}
\BeginKnitrBlock{theorem}\iffalse{-91-24179-31283-38543-26426-36807-31243-30340-21487-24494-24615-93-}\fi{}<div class="theorem"><span class="theorem" id="thm:stationary-mean-square-properties"><strong>(\#thm:stationary-mean-square-properties)  \iffalse (平稳随机过程的可微性) \fi{} </strong></span>自相关函数为 $\rho(u)$ 的平稳随机过程是 $k$ 次均方可微的，当且仅当 $\rho(u)$ 在 $u = 0$ 处是 $2k$ 次可微的。</div>\EndKnitrBlock{theorem}

## 修正的第二类贝塞尔函数 {#sec:modified-bessel-function}

平稳空间高斯过程的自协方差函数是梅隆型时，需要用到修正的第二类贝塞尔函数 $\mathcal{K}_{\kappa}(u)$，它是修正的贝塞尔方程的解 [@Abramowitz1972]，函数形式如下

\begin{equation}
\begin{aligned}
I_{-\kappa}(u) & =  \sum_{m=0}^{\infty} \frac{1}{m!\Gamma(m + \kappa + 1)} \big(\frac{u}{2}\big)^{2m + \kappa} \\
\mathcal{K}_{\kappa}(u) & = \frac{\pi}{2} \frac{I_{-\kappa}(u) - I_{\kappa}(u)}{\sin (\kappa \pi)}
\end{aligned} (\#eq:besselK-function)
\end{equation}

\noindent 其中 $u \geq 0$，$\kappa \in \mathbb{R}$，如果 $\kappa \in \mathbb{Z}$，则取该点的极限值，$\mathcal{K}_{\kappa}(u)$ 的值可由 R 内置的函数 `besselK` 计算 [@Campbell1980]。
\begin{figure}

{\centering \includegraphics[width=0.7\linewidth]{figures/bessel} 

}

\caption{(ref:bessel-function)}(\#fig:bessel-function)
\end{figure}

(ref:bessel-function) 在 $\kappa$ 取不同值时的贝塞尔函数图像：横轴表示距离$u$，纵轴表示函数值$\mathcal{K}_{\kappa}(u)$


## 拉普拉斯近似 {#sec:Laplace-approximation}

先回顾一下基本的泰勒展开，将一个函数 $f(x)$ 在点 $a$ 处展开成和的形式，有时候是无穷多项，可以使用其中的有限项作为近似，通常会选用前三项，即到达函数 $f(x)$ 二阶导的位置。
\[ f(x) = f(a) + \frac{f'(a)}{1!}(x-a) + \frac{f''(a)}{2!}(x-a)^2 + \frac{f'''(a)}{3!}(x-a)^3 + \ldots \]
\noindent 以基本的抛物线函数 $f(x) = x^2$ 为例，考虑将它在 $a = 2$ 处展开。首先计算 $f(x)$ 的各阶导数
\[ f(x) = x^2, \quad f'(x) = 2x, \quad f''(x) = 2, \quad f^{(n)}(x) = 0, \quad n = 3,4,\ldots \]
\noindent 因此，$f(x)$ 可以展开成有限项的和的形式
\[ f(x) = x^2 = 2^2 + 2(2)(x-2) + \frac{2}{2}(x-2)^2 \]
拉普拉斯近似本质上是用正态分布来近似任意分布 $g(x)$，用泰勒展开的前三项近似 $\log g(x)$，展开的位置是密度函数 $g(x)$ 的极值点 $\hat{x}$，则有
\[ \log g(x) \approx \log g(\hat{x}) + \frac{\partial \log g(\hat{x})}{\partial x} (x - \hat{x}) + \frac{\partial^2 \log g(\hat{x})}{2\partial x^2} (x - \hat{x})^2 \]
\noindent 由于是在函数 $g(x)$ 的极值点 $\hat{x}$ 展开， 所以 $x = \hat{x}$ 一阶导是 0，用曲率去估计方差是 $\hat{\sigma}^2 = -1/\frac{\partial^2 \log g(\hat{x})}{2\partial x^2}$，再重写上述近似
\[ \log g(x) \approx \log g(\hat{x}) - \frac{1}{2\hat{\sigma}^2} (x - \hat{x})^2 \]
\noindent 现在，用这个结果做正态近似，将上式两端先取指数，再积分，移去常数项
\[ \int g(x) \mathrm{d}x = \int \exp[\log g(x)] \mathrm{d}x \approx \mathrm{constant} \int \exp[- \frac{(x - \hat{x})^2}{2\hat{\sigma}^2}] \mathrm{d}x \]
\noindent 则拉普拉斯方法近似任意密度函数 $g(x)$ 得到的正态分布的均值为 $\hat{x}$， $\hat{x}$ 可以通过求解方程 $g'(x) = 0$ 获得，方差为 $\hat{\sigma}^2 = -1/g''(\hat{x})$。下面以卡方分布 $\chi^2$ 为例，由于
\begin{align*}
    f(x; k) & = \frac{ x^{k/2-1} \mathrm{e}^{-x/2} }{ 2^{k/2}\Gamma(k/2) }, x \geq 0 \quad \log f(x) = (k/2 - 1) \log x - x/2 \\
 \log f'(x) & = (k/2-1)/x - 1/2 = 0 \quad \log f''(x)  = -(k/2-1)/x^2
\end{align*}
\noindent 所以，卡方分布的拉普拉斯近似为
\[ \chi_{k}^2 \overset{LA}{\sim}  N(\hat{x} = k-2, \hat{\sigma}^2 = 2(k-2)) \]
\noindent 自由度越大，近似效果越好，对于多元分布的情况不难推广，使用多元泰勒展开和黑塞矩阵即可表示[@Tierney1986]。


## 先验和后验分布 {#sec:bayes-prior}

贝叶斯推断中，常涉及模型参数的先验、后验分布，以及一种特殊的无信息先验分布 --- Jeffreys 先验，下面分别给出它们的概念定义。

\BeginKnitrBlock{definition}\iffalse{-91-20808-39564-20998-24067-93-}\fi{}<div class="definition"><span class="definition" id="def:prior-distribution"><strong>(\#def:prior-distribution)  \iffalse (先验分布) \fi{} </strong></span>参数空间 $\Theta$ 上的任一概率分布都称作先验分布 (prior distribution)。</div>\EndKnitrBlock{definition}

\BeginKnitrBlock{definition}\iffalse{-91-21518-39564-20998-24067-93-}\fi{}<div class="definition"><span class="definition" id="def:posterior-distribution"><strong>(\#def:posterior-distribution)  \iffalse (后验分布) \fi{} </strong></span>在获得样本 $\mathbf{Y}$ 后，模型参数 $\boldsymbol{\theta}$ 的后验分布 (posterior distribution) 就是在给定 $\mathbf{Y}$ 条件下 $\boldsymbol{\theta}$ 的条件分布。根据条件概率定义、链式法则、全概率公式，有
\begin{align}
\begin{array}{rcll}
p(\boldsymbol{\theta}|\mathbf{Y})  & =  & \displaystyle \frac{p(\boldsymbol{\theta},\mathbf{Y})}{p(\mathbf{Y})}
& \mbox{ [条件概率定义]}
\\[16pt]
& = & \displaystyle \frac{p(\mathbf{Y}|\boldsymbol{\theta}) p(\boldsymbol{\theta})}{p(\mathbf{Y})}
& \mbox{ [链式法则]}
\\[16pt]
& = & \displaystyle \frac{p(\mathbf{Y}|\boldsymbol{\theta})p(\boldsymbol{\theta})}{\int_{\Theta}p(\mathbf{Y},\boldsymbol{\theta})d\boldsymbol{\theta}}
& \mbox{ [全概率公式]}
\\[16pt]
& = & \displaystyle \frac{p(\mathbf{Y}|\boldsymbol{\theta})p(\boldsymbol{\theta})}{\int_{\Theta}p(\mathbf{Y}|\boldsymbol{\theta})p(\boldsymbol{\theta})d\boldsymbol{\theta}}
& \mbox{ [链式法则]}
\\[16pt]
& \propto & \displaystyle p(\mathbf{Y}|\boldsymbol{\theta})p(\boldsymbol{\theta})
& \mbox{ [$\mathbf{Y}$ 已知]}
\end{array} (\#eq:bayes-theorem)
\end{align}</div>\EndKnitrBlock{definition}

\BeginKnitrBlock{definition}\iffalse{-91-74-101-102-102-114-101-121-115-32-20808-39564-20998-24067-93-}\fi{}<div class="definition"><span class="definition" id="def:Jeffreys-prior-distribution"><strong>(\#def:Jeffreys-prior-distribution)  \iffalse (Jeffreys 先验分布) \fi{} </strong></span>设 $\mathbf{x} = (x_1,\ldots,x_n)$ 是来自密度函数 $p(x|\theta)$ 的一个样本，其中 $\boldsymbol{\theta} = (\theta_1,\ldots,\theta_p)$ 是 $p$ 维参数向量。在对 $\boldsymbol{\theta}$ 无任何先验信息可用时， Jeffreys (1961年)利用变换群和 Harr 测度导出 $\boldsymbol{\theta}$ 的无信息先验分布可用 Fisher 信息阵的行列式的平方根表示。这种无信息先验分布常称为 Jeffreys 先验分布。其求取步骤如下：</div>\EndKnitrBlock{definition}
1. 写出样本的对数似然函数 $l(\boldsymbol{\theta}|x) = \sum_{i=1}^{n}\ln p(x_i | \theta)$； 
2. 算出参数 $\boldsymbol{\theta}$ 的 Fisher 信息阵 $$\mathbf{I}(\boldsymbol{\theta}) = \mathsf{E}_{x|\theta} \big( - \frac{\partial^2 l}{\partial \theta_i \partial \theta_j} \big)_{i,j=1,\ldots,p}$$ 在单参数场合， $\mathbf{I}(\theta) = \mathsf{E}_{x|\theta} \big( - \frac{\partial^2 l}{\partial \theta^2} \big)$；
3. $\boldsymbol{\theta}$ 的无信息先验密度函数为 $\pi(\boldsymbol{\theta}) = [\det \mathbf{I}(\theta) ]^{1/2}$，在单参数场合， $\pi(\theta) = [\mathbf{I}(\theta) ]^{1/2}$。

## 常用贝叶斯估计 {#bayes-estimates}

\BeginKnitrBlock{theorem}\iffalse{-91-24179-26041-25439-22833-93-}\fi{}<div class="theorem"><span class="theorem" id="thm:bayes-estimate-square"><strong>(\#thm:bayes-estimate-square)  \iffalse (平方损失) \fi{} </strong></span>在给定先验分布 $\pi(\theta)$ 和平方损失 $L(\theta,\delta) = (\delta - \theta)^2$ 下，$\theta$ 的贝叶斯估计 $\delta^{\pi}(x)$ 为后验分布 $\pi(\theta|x)$ 的均值，即 $\delta^{\pi}(x) = \mathsf{E}(\theta|x)$。</div>\EndKnitrBlock{theorem}

\BeginKnitrBlock{theorem}\iffalse{-91-48-32-45-32-49-32-25439-22833-93-}\fi{}<div class="theorem"><span class="theorem" id="thm:bayes-estimate-01"><strong>(\#thm:bayes-estimate-01)  \iffalse (0 - 1 损失) \fi{} </strong></span>在给定先验分布 $\pi(\theta)$ 和 $0$ - $1$ 损失函数

\begin{equation*}
L(\theta,\delta) = 
\begin{cases}
1, & | \delta - \theta| \leq \epsilon \\
0, & | \delta - \theta| > \epsilon
\end{cases}
\end{equation*}

当 $\epsilon$ 较小时，$\theta$ 的贝叶斯估计$\delta^{\pi}(x)$为后验分布 $\pi(\theta|x)$ 的众数。</div>\EndKnitrBlock{theorem}

\BeginKnitrBlock{theorem}\iffalse{-91-32477-23545-20540-25439-22833-93-}\fi{}<div class="theorem"><span class="theorem" id="thm:bayes-estimate-abs"><strong>(\#thm:bayes-estimate-abs)  \iffalse (绝对值损失) \fi{} </strong></span>在给定先验分布 $\pi(\theta)$ 和绝对损失函数 $L(\theta,\delta) = |\delta - \theta|$ 下，$\theta$ 的贝叶斯估计 $\delta^{\pi}(x)$ 为后验分布 $\pi(\theta|x)$ 的中位数。</div>\EndKnitrBlock{theorem}

评价贝叶斯估计 $\delta^{\pi}(x)$ 的精度常用后验均方误差
$$\mathsf{MSE}(\delta^{\pi}|x) = \mathsf{E}_{\theta|x}(\delta^{\pi} - \theta)^2$$
表示，或用其平方根$[\mathsf{MSE}(\delta^{\pi}|x)]^{1/2}$ (称为标准误) 表示。容易算得
$$\mathsf{MSE}(\delta^{\pi}|x) = \mathsf{Var}(\delta^{\pi}|x) + [\delta^{\pi}(x) - \mathsf{E}(\theta|x)]^2$$
可见，当贝叶斯估计$\delta^{\pi}(x)$为后验均值时，贝叶斯估计的精度就用$\delta^{\pi}$的后验方差$\mathsf{Var}(\delta^{\pi}|x)$ 表示，或用后验标准差 $[\mathsf{Var}(\delta^{\pi}|x)]^{1/2}$ 表示 [@mao2006]。

## 蒙特卡罗积分 {#Curse-of-Dimensionality}

一般地，空间广义线性混合效应模型的统计推断总是不可避免的要面对高维积分，处理高维积分的方法一个是寻找近似方法避免求积分，一个是寻找有效的随机模拟方法直接求积分。这里，介绍蒙特卡罗方法求积分，以计算 $N$ 维超立方体的内切球的体积为例说明。

假设我们有一个 $N$ 维超立方体，其中心在坐标 $\mathbf{0} = (0,\ldots,0)$。超立方体在点 $(\pm 1/2,\ldots,\pm 1/2)$，有 $2^{N}$ 个角落，超立方体边长是1，$1^{N}=1$，所以它的体积是1。如果 $N=1$，超立方体是一条从 $-\frac{1}{2}$ 到 $\frac{1}{2}$ 的单位长度的线，如果 $N=2$，超立方体是一个单位正方形，对角是 $\left( -\frac{1}{2}, -\frac{1}{2} \right)$ 和 $\left( \frac{1}{2}, \frac{1}{2} \right)$，如果 $N=3$，超立方体就是单位体积的立方体，对角是 $\left( -\frac{1}{2}, -\frac{1}{2}, -\frac{1}{2} \right)$ 和 $\left( \frac{1}{2}, \frac{1}{2}, \frac{1}{2} \right)$，依此类推，$N$ 维超立方体体积是1，对角是 $\left( -\frac{1}{2}, \ldots, -\frac{1}{2} \right)$ 和 $\left( \frac{1}{2}, \ldots, \frac{1}{2} \right)$。

现在，考虑 $N$ 维超立方体的内切球，我们把它称为 $N$ 维超球，它的中心在原点，半径是 $\frac{1}{2}$。我们说点 $y$ 在超球内，意味着它到原点的距离小于半径，即 $\| y \| < \frac{1}{2}$。一维情形下，超球是从的线，包含了整个超立方体。二维情形下，超球是中心在原点，半径为 $\frac{1}{2}$ 的圆。三维情形下，超球是立方体的内切球。已知单位超立方体的体积是1，但是其内的内切球的体积是多少呢？我们已经学过如何去定义一个积分计算半径为 $r$ 的二维球（即圆）的体积（即面积）是 $\pi r^2$，三维情形下，内切球是 $\frac{4}{3}\pi r^3$。但是更高维的欧式空间里，内切球的体积是多少呢？

在这种简单的体积积分设置下，当然可以去计算越来越复杂的多重积分，但是这里介绍采样的方法去计算积分，即所谓的蒙特卡罗方法，由乌拉姆 (S. Ulam)、冯$\cdot$诺依曼(J. von Neumann) 和梅特罗波利斯 (N. Metropolis) 等 在美国核武器研究实验室创立，当时正值二战期间，为了研制原子弹，出于保密的需要，与随机模拟相关的技术就代号蒙特卡罗。现在，蒙特卡罗方法占据现代统计计算的核心地位，特别是与贝叶斯相关的领域。

用蒙特卡罗方法去计算单位超立方体内的超球，首先需要在单位超立方体内产生随机点，然后计算落在超球内的点的比例，即超球的体积。随着点的数目增加，估计的体积会收敛到真实的体积。因为这些点都独立同均匀分布，根据中心极限定理，误差下降的比率是 $\mathcal{O}\left( 1 / \sqrt{n} \right)$，这也意味着每增加一个小数点的准确度，样本量要增加 100 倍。

Table: (\#tab:calculate-volume-of-hyperball) 前 10 维单位超立方体内切球的体积，超立方体内随机模拟的点的个数是 100000（已经四舍五入保留小数点后三位）

| 维数 |   1     |   2     |    3     |    4     |   5     |   6     |    7     |    8     |    9    |    10   |
| :--- | :-----: | :-----: | :------: | :------: | :-----: | :-----: | :------: | :------: | :-----: | :-----: |
| 体积 | 1.000   | 0.784   | 0.525    | 0.307    | 0.166   | 0.081   |  0.037   |  0.016   | 0.006   | 0.0027  |

表 \@ref(tab:calculate-volume-of-hyperball) 列出了前 10 维超球的体积，从上述计算过程中，我们发现随着维数增加，超球的体积迅速变小。这里有一个反直观的现象，内切球的体积竟然随着维数的增加变小，并且在 10 维的情形下，内切球的体积已不到超立方体的 0.3\%，可以预见如果这个积分是 100 维甚至更多，那么内切球相比于正方体仅仅是一个极小的角落，随机点会越来越难以落在内切球内。甚至会因为所需要的随机数太多或者计算机资源的限制，而不可计算，开发更加高效的随机模拟算法也就势在必行。

## Stan 简介  {#sec:stan-samplers}

在上世纪 40\~50 年代，由梅特罗波利斯 (Nicholas Metropolis)，冯$\cdot$诺依曼 (John von Neumann) 和乌拉姆 (Stanislaw Ulam) 创立蒙特卡罗方法，为了纪念乌拉姆，Stan 就以他的名字命名。Stan 是一门基于 C++ 的概率编程语言，主要用于贝叶斯推断，它的代码完全[开源][stan]的，托管在 [Github][stan-dev] 上，自 2012 年 8 月 30 日发布第一个 1.0 版本以来，截至写作时间已发布 33 个版本，目前最新版本是 2.18.0。使用 Stan，用户需提供数据、Stan 代码写的脚本模型，编译 Stan 写的程序，然后与数据一起运行，模型参数的后验模拟过程是自动实现的。除了可以在命令行环境下编译运行 Stan 脚本中写模型外，Stan 还提供其他编程语言的接口，如 R、Python、Matlab、Mathematica、Julia 等等，这使得熟悉其他编程语言的用户可以方便地调用和分析数据。但是，与 Python、R等 这类解释型编程语言不同， Stan 代码需要先翻译成 C++ 代码，然后使用系统编译器 (如 GCC) 编译，若使用 R 语言接口，编译后的动态链接库可以载入 R 内存中，再被其他 R 函数调用执行。

随机模拟的前提是有能产生高质量高效的伪随机数发生器，只有周期长，生成速度快，能通过一系列统计检验的伪随机数才能用作统计模拟，Stan 内置了 Mersenne Twister 发生器，它的周期长达 \(2^{19937}-1\)，通过了一系列严格的检验，被广泛采用到现代软件中，如 Octave 和 Matlab 等 [@Huang2017COS]。除了 Mersenne Twister 随机数发生器，Stan 还使用了 [Boost C++][boost-cpp] 和 [Eigen C++][eigen-cpp] 等模版库用于线性代数计算，这样的底层设计路线使得 Stan 的运算效率很高。 

Stan 内置的采样器 No-U-Turn (简称 NUTS) 源于汉密尔顿蒙特卡罗算法 (Hamiltonian Monte Carlo，简称 HMC)，最早由 Matthew D. Hoffman 和 Andrew Gelman (2014年) [@hoffman2014] 提出。与 Stan 有相似功能的软件 BUGS 和 JAGS 主要采用的是 Gibbs 采样器，前者基于 Pascal 语言开发于 1989 年至 2004 年，后者基于 C++ 活跃开发于 2007 年至 2013 年。在时间上， Stan 具有后发优势，特别在灵活性和扩展性方面，它支持任意的目标函数，模型语言也更加简单易于推广学习，其每一行都是命令式的语句，而 BUGS 和 JAGS 采用声明式；在大量数据的建模分析中， Stan 可以更快地处理复杂模型，这一部分归功于它高效的算法实现和内存管理，另一部分在于高级的 MCMC 算法 --- 带 NUTS 采样器的 HMC 算法。

Donald B. Rubin (1981年) [@Rubin1981] 分析了 Donald L. Alderman 和 Donald E. Powers [@Alderman1980] 收集的原始数据，得出表 \@ref(tab:eight-high-schools)， Andrew Gelman 和 John B. Carlin 等 (2003年) [@Gelman2003] 建立分层正态模型 \@ref(eq:hierarchical-normal-models) 分析 Eight Schools 数据集，由美国教育考试服务调查搜集，用以分析不同的培训项目对学生考试分数的影响，其随机调查了 8 所高中，学生的成绩作为培训效应的估计 $y_j$，其样本方差 $\sigma^2_j$，数据集见表 \@ref(tab:eight-high-schools)。这里再次以该数据集和模型为例介绍 Stan 的使用。

Table: (\#tab:eight-high-schools) Eight Schools 数据集

|   School   |   A   |   B   |   C   |   D   |   E   |   F   |   G   |   H   |
|:----------:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|   $y_i$    |  28   |   8   |   -3  |   7   |   -1  |   1   |   18  |   12  |
| $\sigma_i$ |  15   |  10   |   16  |   11  |    9  |   11  |   10  |   18  |

\begin{equation}
\begin{aligned}
     \mu & \sim \mathcal{N}(0,5) \\
    \tau & \sim \text{Half-Cauchy}(0,5) \\
p(\mu,\tau) & \propto 1 \\
  \eta_i & \sim \mathcal{N}(0,1) \\
\theta_i &  =   \mu + \tau \cdot \eta_i \\
     y_i & \sim \mathcal{N}(\theta_i,\sigma^2_{i}), i = 1,\ldots,8
\end{aligned}
(\#eq:hierarchical-normal-models)
\end{equation}

根据公式组 \@ref(eq:hierarchical-normal-models) 指定的各参数的先验分布，分层正态模型可以在 Stan 中写成如下形式，我们在工作目录下把它保存为 `8schools.stan ` ，供后续编程使用。


```
// saved as 8schools.stan
data {
  int<lower=0> J; // number of schools 
  real y[J]; // estimated treatment effects
  real<lower=0> sigma[J]; // s.e. of effect estimates 
}
parameters {
  real mu; // population mean
  real<lower=0> tau; // population sd
  real eta[J]; // school-level errors
}
transformed parameters {
  real theta[J];  // schools effects
  for (j in 1:J)
    theta[j] = mu + tau * eta[j];
  // theta = mu + tau*eta;
}
model {
  // set prior for mu or uniform prior distribution default
  // target += normal_lpdf(mu  | 0, 10); 
  // target += cauchy_lpdf(tau | 0, 25); # the same as mu
  target += normal_lpdf(eta | 0, 1);
  target += normal_lpdf(y | theta, sigma); // target distribution
  // y ~ normal(theta, sigma);
}
```

上述 Stan 代码的第一段提供数据：学校的数目 $J$，估计值 $y_1,\ldots,y_{J}$，标准差 $\sigma_1,\ldots,\sigma_{J}$，数据类型可以是整数、实数，结构可以是向量，或更一般的数组，还可以带约束，如在这个模型中 $J$ 限制为非负， $\sigma_{J}$ 必须是正的，另外两个反斜杠 // 表示注释。第二段代码声明参数：模型中的待估参数，学校总体的效应 $\theta_j$，均值 $\mu$，标准差 $\tau$，学校水平上的误差 $\eta$ 和效应 $\theta$。在这个模型中，用 $\mu,\tau,\eta$ 表示 $\theta$ 而不是直接声明 $\theta$ 作一个参数，通过这种参数化，采样器的运行效率会提高，还应该尽量使用向量化操作代替 for 循环语句。最后一段是模型：稍微注意的是，正文中正态分布 $N(\cdot,\cdot)$ 中后一个位置是方差，而 Stan 代码中使用的是标准差。`target += normal_lpdf(y | theta, sigma)`  和 `y ~ normal(theta, sigma)` 对模型的贡献是一样的，都使用正态分布的对数概率密度函数，只是后者扔掉了对数后验密度的常数项而已，这对于 Stan 的采样、近似和优化算法没有影响 [@Stan2017JSS]。

算法运行的硬件环境是 16 核 32 线程主频 2.8 GHz 英特尔至强 E5-2680 处理器，系统环境 CentOS 7，R 软件版本 3.5.1，RStan 版本 2.17.3。算法参数设置了 4 条迭代链，每条链迭代 10000 次，为复现模型结果随机数种子设为 2018。

分层正态模型\@ref(eq:hierarchical-normal-models) 的参数 $\mu,\tau$，及其参数化引入的中间参数 $\eta_i,\theta_i,i=1,\ldots,8$，还有对数后验 $lp\_\_$ 的估计值见表 \@ref(tab:eight-schools-output)。




Table: (\#tab:eight-schools-output) 对 Eight Schools 数据集建立分层正态模型 \@ref(eq:hierarchical-normal-models)，采用 HMC 算法估计模型各参数值

|          |  mean  | se_mean |   sd  |  2.5% |   25% |   50% |   75% | 97.5% | n_eff | Rhat |
|   :---   |  ----: |   ----: | -----:| ----: | ----: | ----: | ----: | ----: | ----: | ----:|       
|$\mu$     |   7.99 |   0.05  |5.02   | -1.65 |  4.75 |  7.92 | 11.15 | 18.10 | 8455  |  1   |
|$\tau$    |   6.47 |   0.06  |5.44   |  0.22 |  2.45 |  5.18 |  9.07 | 20.50 | 7375  |  1   |
|$\eta_1$  |   0.40 |   0.01  |0.93   | -1.49 | -0.21 |  0.42 |  1.02 |  2.19 |16637  |  1   |
|$\eta_2$  |   0.00 |   0.01  |0.87   | -1.73 | -0.58 |  0.00 |  0.57 |  1.70 |16486  |  1   |
|$\eta_3$  |  -0.20 |   0.01  |0.93   | -1.99 | -0.82 | -0.20 |  0.41 |  1.66 |20000  |  1   |
|$\eta_4$  |  -0.04 |   0.01  |0.88   | -1.80 | -0.60 | -0.04 |  0.53 |  1.74 |20000  |  1   |
|$\eta_5$  |  -0.36 |   0.01  |0.88   | -2.06 | -0.94 | -0.38 |  0.20 |  1.42 |15489  |  1   |
|$\eta_6$  |  -0.22 |   0.01  |0.90   | -1.96 | -0.82 | -0.23 |  0.37 |  1.57 |20000  |  1   |
|$\eta_7$  |   0.34 |   0.01  |0.89   | -1.49 | -0.24 |  0.36 |  0.93 |  2.04 |16262  |  1   |
|$\eta_8$  |   0.05 |   0.01  |0.94   | -1.81 | -0.57 |  0.06 |  0.69 |  1.91 |20000  |  1   |
|$\theta_1$|  11.45 |   0.08  |8.27   | -1.86 |  6.07 | 10.27 | 15.50 | 31.68 |11788  |  1   |
|$\theta_2$|   7.93 |   0.04  |6.15   | -4.45 |  3.99 |  7.90 | 11.74 | 20.44 |20000  |  1   |
|$\theta_3$|   6.17 |   0.06  |7.67   |-11.17 |  2.07 |  6.74 | 10.89 | 19.94 |16041  |  1   |
|$\theta_4$|   7.66 |   0.05  |6.51   | -5.63 |  3.75 |  7.72 | 11.62 | 20.78 |20000  |  1   |
|$\theta_5$|   5.13 |   0.05  |6.41   | -9.51 |  1.37 |  5.66 |  9.43 | 16.41 |20000  |  1   |
|$\theta_6$|   6.14 |   0.05  |6.66   | -8.63 |  2.35 |  6.58 | 10.40 | 18.47 |20000  |  1   |
|$\theta_7$|  10.64 |   0.05  |6.76   | -1.14 |  6.11 | 10.11 | 14.52 | 25.88 |20000  |  1   |
|$\theta_8$|   8.42 |   0.06  |7.86   | -7.24 |  3.91 |  8.26 | 12.60 | 25.24 |16598  |  1   |
|lp__      | -39.55 |   0.03  |2.64   |-45.41 |-41.15 |-39.31 |-37.67 |-35.12 | 6325  |  1   |

表 \@ref(tab:eight-schools-output) 的列为后验量的估计值：依次是后验均值 $\mathsf{E}(\mu|Y)$、 蒙特卡罗标准误(Monte Carlo standard error)、后验标准差 (standard deviation) $\mathsf{E}(\sigma|Y)$ 、后验分布的 5 个分位点、有效样本数 $n_{eff}$ 和潜在尺度缩减因子 (potential scale reduction factor)，最后两个量 用来分析采样效率和评估迭代序列的平稳性；最后一行表示每次迭代的未正则的对数后验密度 (unnormalized log-posterior density) $\hat{R}$，当链条都收敛到同一平稳分布的时候，$\hat{R}$ 接近 1。

这里对 $\tau$ 采用的非信息先验是均匀先验，参数 $\tau$ 的 95\% 的置信区间是 (0.22,20.5)， 数据支持 $\tau$ 的范围低于 20.5。 

\begin{figure}

{\centering \includegraphics[width=0.7\linewidth]{figures/posterior_mu_tau} 

}

\caption{对 $\mu,\tau$ 给定均匀先验，后验均值 $\mu$ 和标准差 $\tau$ 的直方图}(\#fig:posterior-mu-tau)
\end{figure}

为了得到可靠的后验估计，做出合理的推断，诊断序列的平稳性是必不可少的部分，前 5000 次迭代作为 warm-up 阶段，后 5000 次迭代用作参数的推断，图 \@ref(fig:posterior-mu-tau) (a) 给出 $\mu$ 和 $\log(\tau)$ 的迭代序列图，其中橘黄色线分别是对应的后验均值(表 \@ref(tab:eight-schools-output)的第一列)，图 \@ref(fig:posterior-mu-tau) (b) 分别给出 $\log(\tau)$ 的蒙特卡罗误差，图中显示随着迭代次数增加，蒙特卡罗误差趋于稳定，说明参数 $\tau$ 的迭代序列达到平稳分布，即迭代点列可以看作来自参数的后验分布的样本。

\begin{figure}[!htb]

{\centering \subfloat[参数 $\log(\tau)$ 和 $\mu$ 的迭代序列图(trace plot)(\#fig:diagnostic1)]{\includegraphics[width=0.7\linewidth]{figures/trace_mu_log_tau} }\\\subfloat[参数 $\log(\tau)$ 的蒙特卡罗均值误差随迭代次数的变化，右图参数 $\log(\tau),\mu$ 的迭代点对的散点图，其中橘黄色点表示使迭代发散的点(\#fig:diagnostic2)]{\includegraphics[width=0.7\linewidth]{figures/mcmc_mean_tau_div} }

}

\caption{诊断参数$\mu,\log(\tau)$迭代序列的平稳性}(\#fig:diagnostic)
\end{figure}

为了评估链条之间和内部的混合效果， Andrew Gelman 等 [@Gelman2013R] 提出引入潜在尺度缩减因子 (potential scale reduction factor) $\hat{R}$ 描述链条的波动程度，类似一组数据的方差含义，方差越小波动性越小，数据越集中，这里意味着链条波动性小。一般地，对于每个待估的量 $\omega$，模拟产生 $m$ 条链，每条链有 $n$ 次迭代值 $\omega_{ij} (i = 1,\ldots,n;j=1,\ldots,m)$，用 $B$ 和 $W$ 分别表示链条之间（不妨看作组间方差）和内部的方差（组内方差）

\begin{equation}
\begin{aligned}
& B = \frac{n}{m-1}\sum_{j=1}^{m}(\bar{\omega}_{.j} - \bar{\omega}_{..} ), \quad \bar{\omega}_{.j} = \frac{1}{n}\sum_{i=1}^{n}\omega_{ij}, \quad \bar{\omega}_{..} = \frac{1}{m}\sum_{j=1}^{m} \bar{\omega}_{.j}\\
& W = \frac{1}{m}\sum_{j=1}^{m}s^{2}_{j}, \quad s^{2}_{j} = \frac{1}{n-1}\sum_{i=1}^{n}(\omega_{ij} - \bar{\omega}_{.j})^2
\end{aligned} (\#eq:potential-scale-reduction)
\end{equation}

\noindent $\omega$ 的边际后验方差 $\mathsf{\omega|Y}$ 是 $W$ 和 $B$ 的加权平均

\begin{equation}
\widehat{\mathsf{Var}}^{+}(\omega|Y) = \frac{n-1}{n} W + \frac{1}{n} B 
\end{equation}

当初始分布发散 (overdispersed) 时，这个量会高估边际后验方差，但在链条平稳或 $n \to \infty$ 时，它是无偏的。同时，对任意有限的 $n$，组内方差 $W$ 应该会低估 $\mathsf{Var}(\omega|Y)$，因为单个链条没有时间覆盖目标分布；在 $n \to \infty$， $W$ 的期望会是 $\mathsf{Var}(\omega|Y)$。

通过迭代序列采集的样本估计 $\hat{R}$ 以检测链条的收敛性

\begin{equation}
\hat{R} = \sqrt{\frac{\widehat{\mathsf{Var}}^{+}(\omega|Y)}{W}}
\end{equation}

\noindent 随着 $n \to \infty$， $\hat{R}$ 下降到 1。如果 $\hat{R}$ 比较大，我们有理由认为需要增加模拟次数以改进待估参数 $\omega$ 的后验分布。从表 \@ref(tab:eight-schools-output) 来看，各参数的 $\hat{R}$ 值都是 1，说明各个迭代链混合得好。

## 本章小结 {#sec:foundations}

本章第\@ref(sec:exp)节介绍了指数族的一般形式，指出基于样本点的对数似然函数和样本均值、样本方差的关系，以表格的形式列出了正态、泊松和二项分布的各个特征，为第\@ref(models)章统计模型和第\@ref(algorithms)章参数估计作铺垫。接着，第\@ref(sec:lse)节和第\@ref(sec:def-mle)节分别介绍了最小二乘估计和极大似然估计的定义、性质，给出了线性模型的最小二乘估计，极大似然估计的相合性和渐进正态性。第\@ref(sec:stationary-gaussian-process)节介绍了平稳高斯过程，给出了其均方连续性、可微性定义以及一个均方可微的判断定理，平稳高斯过程作为空间随机效应的实现，多次出现在后续章节中。第\@ref(sec:Laplace-approximation)节介绍了拉普拉斯近似的思想，具体以正态分布作为阐述，它是空间广义线性混合模型参数估计的重要部分，主要应用在第\@ref(algorithms)章第\@ref(subsec:LA)小节当中，用以近似似然函数中关于空间随机效应的高维积分。第\@ref(sec:bayes-prior)节至第\@ref(sec:stan-samplers)节分别是与贝叶斯相关的概念定义、参数估计、计算方法。

[stan]: http://mc-stan.org/
[stan-dev]: https://github.com/stan-dev/stan
[boost-cpp]: https://www.boost.org/
[eigen-cpp]: http://eigen.tuxfamily.org/index.php?title=Main_Page
