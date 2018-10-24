
---
title: "Master Thesis Template"
subtitle: "China University of Mining and Technology, Beijing"
author: "Xiang-Yun Huang"
date: "2018-10-24"
site: bookdown::bookdown_site
documentclass: book
mainfont: Times New Roman
sansfont: Arial
monofont: Inconsolata
geometry: margin=1.18in
bibliography: ["latex/book.bib", "latex/refer.bib"]
link-citations: yes
graphics: yes
tables: yes
mathspec: yes
papersize: "a4"
fontsize: "12pt"
fontenc: T1
linestretch: 1.25
classoption: "UTF8,twoside"
natbiboptions: super,square,sort
biblio-style: "GBT7714-2005"
indent: 40pt
pdfproducer: "Pandoc, R Markdown, TinyTeX, knitr, bookdown, Stan"
github-repo: "XiangyunHuang/Thesis-Template-Bookdown"
cover-image: "images/logo.png"
favicon: "images/favicon.ico"
description: "Spatial generalized linear mixed models, Stationary Spatial Gaussian Process, Stan platform, Markov chain Monte Carlo."
---

\mainmatter

# 绪论 {#intro}

空间统计的内容非常丰富，主要分为地质统计 （geostatistics）、 离散空间变差 （discrete spatial variation） 和空间点过程 （spatial point processes） 三大块 [@Cressie1993]。 地质统计这个术语最初来自南非的采矿业 [@Krige1951]， 并由 Georges Matheron 及其同事继承和发展，用以预测黄金的矿藏含量和质量。空间广义线性混合效应模型 （Spatial Generalized Linear Mixed Model，简称 SGLMM） 在空间统计中有着广泛的应用，如评估岩心样本石油含量，分析核污染物浓度的空间分布 [@Diggle1998]， 预测冈比亚儿童疟疾流行度的空间分布 [@Diggle2002Childhood]， 喀麦隆及其周边地区的热带眼线虫流行病的的空间分布 [@Diggle2007ATMP]，对热带疾病预防和控制项目提供决策支持 [@Schl2016Using]。 在热带地区，淋巴丝虫病和盘尾丝虫病是严峻的公共卫生问题， 据世界卫生组织统计， 在非洲撒哈拉以南、 阿拉伯半岛和南美洲的 34 个国家约 2000 \~ 4000 万人感染河盲病 [@Takougang2002Rapid]。 例如， 喀麦隆中部省份 Loa loa 是导致河盲病的寄生虫，它的感染强度与疾病流行度之间存在线性关系， 即 Loa loa 感染强度越大流行度越高 [@Boussinesq2001]。 1997 年，研究表明 Loa loa 流行度对应的高感染强度的临界值为 20\% [@Gardon1997Serious]， 而研究个体水平的感染情况与群体水平流行度之间的关系有助于大规模给药 [@Schl2016Using]，所以更加高效的算法和算法实现可以更快、更准、更有效地在大范围内做疾病预防和医疗资源分配。

## 文献综述 {#reviews}

如何计算空间广义线性混合效应模型的参数一直以来是研究的重点，由于模型中的随机效应和空间位置相关联，而空间位置的数量和具体坐标直接影响空间效应的维度，给参数估计值的计算带来很大的复杂性，因为参数的贝叶斯估计和极大似然估计都离不开对空间效应的高维积分，所以在计算上是一个很大的挑战。在贝叶斯方法下，Diggle 等 （1998 年） [@Diggle1998] 提出随机游走的 Metropolis 程序实现马尔科夫链蒙特卡罗算法获得模型参数的后验密度分布及后验量的估计值。Ribeiro 和 Diggle （2001 年） [@geoR2001] 提出 Langevin-Hastings 算法，相比于随机游走的 Metropolis 算法，取得了更好的计算效率，后续的一个稳健版本由 Christensen （2006 年） [@Christensen2006] 给出。在实际操作中，马尔科夫链蒙特卡罗算法（简称 MCMC）面临的主要问题是收敛性诊断和计算时间， 当然算法实现的本身也很重要，对终端用户来说，可能大部分并不善于编程，所以算法的实现过程可能存在问题，因此，寻求一个好的贝叶斯推断工具或平台也很重要。目前，通过 MCMC 方式拟合带随机效应的模型有 [WinBUGS][winbugs] [@BUGS2009]，[OpenBUGS][openbugs]， [JAGS][jags]，[BayesX][bayesx]， [MultiBUGS][multi-bugs]，NIMBLE [@nimble2017]，Stan [@Stan2017JSS] 等软件。近年来，一些研究者开始将注意力放到高维积分的近似上，从而出现了一类新的近似贝叶斯推断， Rue 等 （2009 年） [@Rue2009] 在高斯马尔科夫随机场近似平稳空间高斯过程的设置下，用拉普拉斯近似空间效应的高维积分，从而提出集成嵌套拉普拉斯算法，Lindgren 等 （2011 年） [@Lindgren2011] 提出相似的算法用于随机效应是偏态分布情形下的 SGLMM 模型的参数估计。Rue 等 （2009 年） [@Rue2009] 肯定了拉普拉斯近似方法的使用，认为这类近似具有足够的准确度，可以用于实际数据分析。虽然在计算上达到了快捷，但人们对贝叶斯方法最严厉的评判依然是它依赖于先验分布的选择。 Christensen （2004 年） [@Christensen2004] 又提出蒙特卡罗极大似然算法，它还是依赖 MCMC 算法，但是提供了关于参数的似然分析，其算法实现打包在 R 包 geoRglm 里，详细描述参见 Diggle 和 Ribeiro（2007 年） [@Diggle2007]。作为蒙特卡罗似然的一个替代方法， Hao （2002 年） 提出蒙特卡罗期望极大算法 （简称 MCEM ），他将不能直接观察到的空间随机效应部分看作是缺失数据。

Diggle 等 （1998 年） 基于马绍尔群岛国家放射性调查数据 --- 记录南太平洋朗格拉普岛上 ${}^{137}\mathrm{Cs}$ 放射 $\gamma$ 粒子的强度数据，建立响应变量服从泊松分布的 SGLMM 模型，在贝叶斯方法下，用 Metropolis-Hastings 采样实现 MCMC 算法，获得 SGLMM 模型的参数估计，分析了残留的核污染物浓度的空间分布，此外，他们还建立响应变量服从二项分布的 SGLMM 模型分析北拉纳克郡和南坎布里亚郡的居民感染弯曲杆菌的空间分布情况。 Christensen （2004 年） [@Christensen2004] 在 Diggle 等 （1998 年） [@Diggle1998] 分析格拉普岛核残留数据的模型上，添加非空间的相互独立的随机效应，取得了更好的拟合效果，这种非空间的随机效应在地质统计学中常称为块金效应。Diggle 和 Giorgi （2016年） [@Diggle2016] 基于肯尼亚尼扬扎省的疟疾数据，该数据组合了学校和村庄的信息，分析的是一个多源数据，假定其中一个数据是有偏的，来自非随机的调查，另一个数据是无偏的，来自随机调查，因而建立包含两个服从平稳空间过程的空间随机效应，使用蒙特卡罗极大似然算法（简称 MCML ）估计二项 SGLMM 模型的参数，获得疟疾在该省的空间分布；第二个数据是马拉维奇瓦瓦区 2010 年 5 月至 2013 年 6 月收集的疟疾数据，在 Diggle 等 （1998 年） 的基础上将时间考虑进二项 SGLMM 模型中，并且假定时间项和空间项是无关的，而块金效应只依赖于时间变化，同样基于 MCML 算法，估计了模型的各个参数；第三个数据建模是在带块金效应的 SGLMM 基础上，认为响应变量应服从混合二项分布以包含极低的感染程度，比如有些村庄没有一个受到感染，因此建立零过多 （Zero-inflation）二项空间混合效应模型分析第三个河盲病数据集。

在面对复杂的高维积分时，每种替代方法，无论走随机模拟还是近似的路线，都有相应的代价，基于拉普拉斯近似的方法依赖于初值的选择，基于随机模拟的 MCMC 算法依赖于先验分布和算法参数的调整，这些对最后的数据分析结果都会产生影响，调参的过程往往充满经验和技巧。尽管不断有新的、复杂的算法和方法开发出来，Bonat 和 Ribeiro（2016年） [@Bonat2016Practical] 认为只有能被广泛使用，实现方式比较直接的参数估计方法才是比较安全可靠的选择。

## 论文结构 {#stracture}

第 \@ref(intro) 章绪论部分主要介绍了 SGLMM 模型的研究现状， 综述了 SGLMM 模型参数估计的贝叶斯 MCMC 和 MCML等 算法及其应用情况。 第 \@ref(prepare) 章介绍了指数族，最小二乘估计，极大似然估计，平稳高斯过程，拉普拉斯近似，先验和后验分布和蒙特卡罗积分等基础知识，它们作为后续相关章节的知识准备。第 \@ref(models) 章回顾了一般线性模型到 SGLMM 模型的结构， 指出了模型从简单到复杂的变化过程，及其中的区别和联系。第 \@ref(algorithms) 章介绍了目前估计 SGLMM 模型参数的算法，分别是拉普拉斯近似算法、蒙特卡罗极大似然算法、贝叶斯 MCMC 算法、低秩近似算法，并在贝叶斯 MCMC 算法的基础上，提出基于 Stan 程序库实现的贝叶斯 STAN-MCMC 算法。第 \@ref(simulations) 章首先介绍了一维和二维情形下平稳空间高斯过程的模拟，然后在二维情形下，分别模拟了响应变量服从二项分布和泊松分布的 SGLMM 模型，比较了贝叶斯 MCMC 算法和贝叶斯 STAN-MCMC 算法。 第 \@ref(applications) 章给出了两个案例分析，分别是基于空间线性混合效应模型的小麦数据分析和基于泊松型空间广义线性混合效应模型的核污染数据分析。第 \@ref(summary) 章总结论文的主要工作、相关结论和后续研究方向。

[multi-bugs]: https://www.multibugs.org
[JAGS]: http://mcmc-jags.sourceforge.net/
[openbugs]: http://www.openbugs.net/
[winbugs]: http://www.mrc-bsu.cam.ac.uk/software/bugs/the-bugs-project-winbugs/
[bayesx]: http://www.BayesX.org
