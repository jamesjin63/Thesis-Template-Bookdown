
# 数据分析 {#applications}

第\@ref(sec:spatial-random-effects)节基于小麦产量数据建立空间线性混合效应模型，以 R 软件和相关 R 包为工具，介绍空间统计建模分析的过程，特别是诊断和添加空间随机效应的分析方法和模型参数初值的确定方式。这个分析方法和初值的确定方式具有普适性，对于更复杂的空间广义线性混合效应模型也是适用的。第\@ref(case-rongelap)节建立响应变量服从泊松分布的空间广义线性混合效应模型分析一个真实数据集 rongelap。rongelap 数据集目前由 Ole F. Christensen  维护在 R 包 **geoRglm** 里，曾被 Peter J. Diggle 等 (1998年) [@Diggle1998] 和 Ole F Christensen (2004年) [@Christensen2004] 分析过，与他们不同的是，第\@ref(case-rongelap)节将基于蒙特卡罗极大似然算法估计该泊松型空间广义线性混合效应模型的参数。

## 小麦产量的空间分布 {#sec:spatial-random-effects}

Walter W. Stroup 和 P. Stephen Baenziger (1994年) [@Stroup1994] 采用完全随机的区组设计研究小麦产量与品种等因素的关系，在 4 块肥力不同的地里都随机种植了 56 种不同的小麦， 实验记录了小麦产量、品种、位置以及土地肥力等数据， José Pinheiro 和 Douglas Bates (2000年) [@Pinheiro2000] 将该数据集命名为 Wheat2 ，整理后放在 **nlme** 包里。 这里利用该真实的农业生产数据构建带空间效应的线性混合效应模型，展示诊断和添加空间效应的过程。

\begin{figure}

{\centering \includegraphics[width=0.85\linewidth]{figures/Yields-Block} 

}

\caption{小麦产量与土壤肥力的关系，图中纵轴表示试验田的4种类型，且土壤肥力强弱顺序是 1 > 2 > 3 > 4，横轴表示小麦产量，每块试验田都种植了 56 种小麦，图中分别以不同的颜色标识，图上方是小麦类型的编号}(\#fig:yields-block)
\end{figure}

图 \@ref(fig:yields-block) 按土壤肥力不同分块展示每种小麦的产量，图中暗示数据中有明显的 block 效应，即不同实验田对结果产生显著影响，而且不同实验田之间，小麦产量呈现异方差性，为了更好地表达这些效应，可以基于经纬度坐标信息添加与空间相关的结构 (spatial correlation structures)。基于上述对图\@ref(fig:yields-block) 的探索，先建立一般的线性模型，以量化上述描述性分析结果，模型结构如下
\begin{equation}
y_{ij} = \tau_i + \epsilon_{ij}, \quad \boldsymbol{\epsilon} \sim \mathcal{N}(\mathbf{0},\sigma^2 \boldsymbol{\Lambda}) (\#eq:extended-linear-model)
\end{equation}
\noindent 其中，$y_{ij}$ 表示第 $i$ 种小麦在第 $j$ 块试验田里的产量，$i = 1,\ldots,56$，$j = 1,\ldots,4$。 $\tau_i$ 表示第 $i$ 种小麦的平均产量，$\epsilon_{ij}$ 是随机误差，假定服从均值为 0，协差阵为 $\sigma^2 \boldsymbol{\Lambda}$ 的多元正态分布。进一步，继续探索线性模型\@ref(eq:extended-linear-model)中的协方差 $\boldsymbol{\Lambda}$ 的结构，不妨先假定模型 \@ref(eq:extended-linear-model) 的随机误差是独立且方差齐性的，即 $\boldsymbol{\Lambda} = \boldsymbol{I}$ 。接着，需要确认方差齐性的假设是否合适，拟合残差散点图是一个有用又方便的判断工具。特别地，对于空间效应的探索，采用样本变差图探索数据中存在的空间相关性，可调用 **nlme** 包中的 `Variogram` 函数获得 `gls` 函数拟合方差齐性的线性模型的变差图\@ref(fig:yields-variogram)。

\begin{figure}

{\centering \includegraphics[width=0.7\linewidth]{figures/Yields-Variogram} 

}

\caption{样本变差散点图，横坐标是小麦之间的欧氏距离，纵坐标是样本变差，图中的平滑线根据局部多项式拟合的方法添加，用以估计样本变差的大致趋势}(\#fig:yields-variogram)
\end{figure}

图 \@ref(fig:yields-variogram) 显示样本变差随空间距离有明显的增长趋势，可见空间随机效应明显，根据第\@ref(models)章第\@ref(subsec:covariance-function)小节，可以有理由地估计块金效应$\tau^2$大约是 0.2，参数 $\phi$ 可由样本变差为 1 对应的空间距离来初步估计，图\@ref(fig:yields-variogram)显示该值大约是 31。 图中的平滑曲线是 局部多项式回归拟合的结果，也可以用局部加权回归拟合的平滑法来确定初值 [@Xie2008COS]。上述图示分析，首先采用球性自相关函数拟合这组数据中的空间结构。考虑空间效应后，采用 `gls` 函数提供的限制极大似然法 (Restricted Maximum Likelihood Estimation， 简称 REML) 拟合模型\@ref(eq:extended-linear-model)，与第\@ref(prepare)章第\@ref(sec:def-mle)节介绍的极大似然估计相比，它对方差分量的估计偏差更小一些[@Diggle2007]，适合估计线性混合效应模型的参数，`gls` 还支持不同类型的空间自相关函数，因此继续探索球型和二次有理型自相关函数^[(ref:corRatio)]对模型拟合结果的影响。

Table: (\#tab:yields-model-compare) 以小麦数据为例估计空间线性混合效应模型的参数，比较不同初值和自协方差函数对模型拟合效果的影响，表中$\phi_0,\tau^2_{0},\sigma^2_{0}$和$\hat{\phi},\hat{\tau}^2,\hat{\sigma}^2$分别是模型\@ref(eq:extended-linear-model)参数$\phi,\tau^2,\sigma^2$的初值和估计值

|       | 自相关函数 |   $\hat{\phi}(\phi_0)$   |  $\hat{\tau}^2(\tau^2_{0})$|  $\hat{\sigma}^2(\sigma^2_{0})$ |   log-REML  |
|:-----:| ----------:| --------------: | --------------:| -----:| ---------:|
|  I    |       球型 | $1.515\times 10^{5}(31)$ | $5.471\times 10^{-5}(0.2)$ |    466.785    |  -533.418   |
|  II   | 二次有理型 |             $13.461(13)$ |               $0.193(0.2)$ |      8.847    |  -532.639   |
|  III  |       球型 |             $27.457(28)$ |               $0.209(0.2)$ |      7.410    |  -533.931   |

其中二次有理型自相关函数 $\rho(u) = (u/\phi)^2/[1 + (u/\phi)^2]$，则半变差函数 $V(u) = 1-\rho(u) = [\tau + (u/\phi)^2]/[1 + (u/\phi)^2]$。当距离 $u = \phi$ 时，变差等于 $(1+\tau)/2$，由图\@ref(fig:yields-variogram)可知 $\tau = 0.2$， 样本变差就等于 0.6 对应的距离，大约是 13，所以 $\phi=13$。

值得注意的是，用限制极大似然法估计模型 \@ref(eq:extended-linear-model) 的参数时，对初始值很敏感，通过几番试错调整初值获得如表 \@ref(tab:yields-model-compare) 所示结果。根据表\@ref(tab:yields-model-compare)， 可以得出两个结论，其一选择合适的自相关函数可以取得更好的拟合效果，其二限制极大似然算法对初值很敏感，不断试错以选择合适的初值很重要。最后，再来观察使用空间线性混合效应模型拟合小麦数据后的标准化残差图，如图 \@ref(fig:model-check)所示，残差中空间效应已经提取的很充分了。

\begin{figure}[!htb]

{\centering \subfloat[检查标准化拟合残差后的异方差性：横轴表示模型的拟合值，纵轴是标准化后的残差值(\#fig:model-check1)]{\includegraphics[width=0.48\linewidth]{figures/heteroscedasticity} }\subfloat[检查标准化拟合残差后的正态性：横轴表示标准化后的残差值，纵轴表示标准正态分布的分位数(\#fig:model-check2)]{\includegraphics[width=0.48\linewidth]{figures/normality} }

}

\caption{空间线性混合效应模型的拟合残差诊断}(\#fig:model-check)
\end{figure}

(ref:corRatio) 详见 R 包 **nlme** 内函数 corRatio 帮助文档。

## 朗格拉普岛核污染浓度的空间分布 {#case-rongelap}

朗格拉普岛位于南太平洋上，是马绍尔群岛的一部分，二战后，美国在该岛上进行了多次核武器测试，核爆炸后产生的放射性尘埃笼罩了全岛，目前该岛仍然不适合人类居住，只有经批准的科学研究人员才能登岛。基于马绍尔群岛国家的放射性调查数据，Peter J. Diggle 等 (1998年) [@Diggle1998] 使用 蒙特卡罗极大似然算法估计空间广义线性混合效应模型 \@ref(eq:rongelap-without-nugget-effect) 的各个参数，Ole F Christensen (2004年) [@Christensen2004] 发现该核污染数据集中存在不能被泊松分布解释的残差，因此添加了非空间的随机效应 $Z_i$，建立模型 \@ref(eq:rongelap-with-nugget-effect)，在地质统计领域内，$Z_i$ 还有个专有名词叫块金效应。
\begin{align}
\log\{\lambda(x_{i})\}& =  \beta + S(x_{i}) (\#eq:rongelap-without-nugget-effect)\\
\log\{\lambda(x_{i})\}& =  \beta + S(x_{i}) + Z_{i} (\#eq:rongelap-with-nugget-effect)
\end{align}
放射性调查获得的 rongelap 数据集包含几个观测变量，分别是放射粒子数、相应时间间隔和157个空间坐标。图\@ref(fig:rongelap-dataset) 表示记录放射性数据的站点分布。根据 ${}^{137}\mathsf{Cs}$ 放出的伽马射线在 $N=157$ 站点不同时间间隔的放射量， 建立泊松广义线性混合效应模型 \@ref(eq:rongelap-with-nugget-effect)。模型\@ref(eq:rongelap-with-nugget-effect)中，$\beta$ 是截距， 放射粒子数作为响应变量服从强度为 $\lambda(x_i)$ 的泊松分布，即 $Y_{i} \sim \mathrm{Poisson}( \lambda(x_i) )$，平稳空间高斯过程 $S(x),x \in \mathbb{R}^2$的自协方差函数为 $\mathsf{Cov}( S(x_i), S(x_j) ) = \sigma^2 \exp( -\|x_i -x_j\|_{2} / \phi )$，且 $Z_i$ 之间相互独立同正态分布 $\mathcal{N}(0,\tau^2)$，这里 $i = 1,\ldots, 157$。

\begin{figure}

{\centering \includegraphics[width=0.6\linewidth]{figures/rongelap-island} 

}

\caption{朗格拉普岛上测量伽玛粒子放射性强度的位置分布，图中加号 + 标注采样的位置，水平方向表示横坐标，垂直方向表示纵坐标，这里使用的坐标系是 UTM (Universal Transverse Mercator) 坐标系}(\#fig:rongelap-dataset)
\end{figure}

蒙特卡罗极大似然算法迭代的初值 $\beta_{0} = 6.2,\sigma^2_{0} = 2.40,\phi_{0} = 340,\tau^2_{rel} = 2.074$，模拟次数为 30000 次，前 10000 次迭代视为预热阶段 (warm-up)，其后每隔 20 次迭代采一个样本点，即存储模型各参数的迭代值，每个参数获得1000次迭代值。蒙特卡罗模拟平稳空间高斯过程 $S(x)$ 关于响应变量$Y$的条件分布 $[S(x_i)|Y_i]$ 时，使用了第\@ref(algorithms)章第\@ref(sec:MCMC)节介绍的 Langevin-Hastings 算法 [@Omiros2003]，157 个站点意味着有 157 个条件分布需要模拟，共产生 157个迭代链，每条链需保持平稳才可用于模型参数的推断，因此需要先检验每条链的平稳性，可以采用自相关图和时序图来检验，篇幅所限，取部分站点展示，见附图\@ref(fig:rongelap-trace-plot) 和图 \@ref(fig:rongelap-acf-plot)，经观察157个站点处的$S_i$的迭代点列没有出现不平稳的现象。

\begin{figure}

{\centering \includegraphics[width=0.7\linewidth]{figures/profile-phitausq} 

}

\caption{(ref:profile-phi-tausq)}(\#fig:profile-phi-tausq)
\end{figure}

(ref:profile-phi-tausq) 关于 $\phi$ 和相对块金效应 $\tau^2_{rel} = \tau^2 / \sigma^2$ 的剖面似然函数曲面，平稳空间高斯过程的自协方差函数选用指数型

Table: (\#tab:rongelap-mcml-result) 蒙特卡罗极大似然算法估计模型 \@ref(eq:rongelap-with-nugget-effect) 的参数，其中块金效应的估计值 $\hat{\tau}^2 = \hat{\sigma}^{2} \times \hat{\tau}^2_{rel} = 4.929$

| 参数    | $\hat{\beta}$ | $\hat{\sigma}^{2}$ | $\hat{\phi}$  | $\hat{\tau}^2_{rel}$ |  $\log L_{m}$          |
| :------:| :-------------| :-------------     | :------------ | :------------------- |:---------------------  |
| 初始值  |    6.200      |     2.400          |   340.000     |   2.074              |         -              |
| 估计值  |    6.190      |     2.401          |   338.126     |   2.053              | $2.892 \times 10^{-3}$ |

由表 \@ref(tab:rongelap-mcml-result) 可知，正如第 \@ref(simulations) 章第 \@ref(sec:simulations) 节对蒙特卡罗极大似然算法所指出的那样，必须提供足够接近真值的初值，才能获得好的参数估计。由图 \@ref(fig:profile-phi-tausq) 不难看出，关于 $\phi$ 和相对块金效应 $\tau^2_{rel}$ 的剖面似然函数曲面类似一个极其狭长的、坡度又平缓的山谷，基于似然的算法对这种类型的问题还没有好的解决办法，目前取多个不同参数初值进行迭代，用迭代值画出剖面似然函数曲面，通过观察获得最佳初值。

## 本章小结 {#sec:cases}

从小麦数据和核污染数据的似然分析中，可以清晰地看到模型参数初值对求似然函数极值点的重要性。借助变差图等可视化手段探索模型结构，确定初值是很有意义的，在小麦数据的分析过程中，并不是一步到位地给出空间线性混合效应模型，而是给出了模型从简单到复杂的建模过程，这对于实际应用有指导意义。对核残留数据集，建立了泊松型空间广义线性混合效应模型，其似然分析则借助了剖面似然估计的思想，将一个含有多个参数的未知似然函数降至只有两维的剖面似然函数曲面，再对似然曲面的分析获得最佳初始值的位置，最后根据蒙特卡罗最大似然算法获得了近似全局最优的参数估计值。



