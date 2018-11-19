
# 附录 {#appendix .unnumbered}

\chaptermark{附录}

## 软件信息  {#sessioninfo .unnumbered}


```r
xfun::session_info(packages = c("rmarkdown", "bookdown"), dependencies = FALSE)
#> R version 3.5.1 (2018-07-02)
#> Platform: x86_64-w64-mingw32/x64 (64-bit)
#> Running under: Windows 8.1 x64 (build 9600)
#> 
#> Locale:
#>   LC_COLLATE=Chinese (Simplified)_China.936 
#>   LC_CTYPE=Chinese (Simplified)_China.936   
#>   LC_MONETARY=Chinese (Simplified)_China.936
#>   LC_NUMERIC=C                              
#>   LC_TIME=Chinese (Simplified)_China.936    
#> 
#> Package version:
#>   bookdown_0.7.18   rmarkdown_1.10.12
#> 
#> Pandoc version: 2.2.3.2
```


## 随机数生成 {#random-number-generation .unnumbered}

基于空间广义线性混合效应模型生成随机数的函数：
`generate_sim_data` 函数可生成响应变量服从泊松分布或二项分布，平稳高斯过程的自相关函数为二次幂指数族或梅隆族的随机数


```r
generate_sim_data <- function(N = 49, intercept = -1.0, 
                              slope1 = 1.0, slope2 = 0.5,
                              lscale = 1, sdgp = 1, 
                              cov.model = "exp_quad", type = "binomal") {
  # set.seed(2018) 
  ## 单位区域上采样
  d <- expand.grid(
    d1 = seq(0, 1, l = sqrt(N)),
    d2 = seq(0, 1, l = sqrt(N))
  )
  D <- as.matrix(dist(d)) # 计算采样点之间的欧氏距离
  switch (cov.model,
          matern = {
            phi = lscale
            corr_m = geoR::matern(D, phi = phi, kappa = 2) # 固定的 kappa = 2 
            m  = sdgp^2 * corr_m 
          },
          exp_quad = {
            phi <- 2 * lscale^2
            m <- sdgp^2 * exp(-D^2 / phi) # 多元高斯分布的协方差矩阵
          }
  )
  # powered.exponential (or stable)
  # rho(h) = exp[-(h/phi)^kappa] if 0 < kappa <= 2 此处 kappa 固定为 2
  S <- MASS::mvrnorm(1, rep(0, N), m) # 产生服从多元高斯分布的随机数
  # 模拟两个固定效应
  x1 <- rnorm(N, 0, 1)
  x2 <- rnorm(N, 0, 4)
  switch(type,
         binomal = {
           units.m <- rep(100, N) # N 个 100
           pred <- intercept + slope1 * x1 + slope2 * x2 + S
           mu <- exp(pred) / (1 + exp(pred))
           y <- rbinom(N, size = 100, prob = mu) # 每个采样点抽取100个样本
           data.frame(d, y, units.m, x1, x2)
         },
         poisson = {
           pred <- intercept + slope1 * x1 + slope2 * x2 + S
           y <- rpois(100, lambda = exp(pred)) # lambda 是泊松分布的期望  
           # Y ~ Possion(lambda) g(u) = ln(u) u = lambda = exp(g(u))
           data.frame(d, y, x1, x2)
         }
  )
}
```

## 算法比较 {#compare-algrithms .unnumbered}


```r
# 加载程序包
library(rstan)
library(brms)
# 以并行方式运行STAN-MCMC算法，指定CPU的核心数
options(mc.cores = parallel::detectCores())
# 将编译后的模型写入磁盘，可防止重新编译
rstan_options(auto_write = TRUE)
theme_set(theme_default())
prior <- c(
  set_prior("normal(0,10)", class = "b"), # 均值0 标准差 10 的先验
  set_prior("lognormal(0,1)", class = "lscale"),
  set_prior("lognormal(0,1)", class = "sdgp")
)
sim_binom_data <- generate_sim_data(type = "binomal")
benchmark.binomal <- microbenchmark::microbenchmark({
  fit.binomal <- brm(y | trials(units.m) ~ 0 + intercept + x1 + x2 + gp(d1, d2),
    sim_binom_data,
    prior = prior,
    chains = 4, thin = 5, iter = 15000, warmup = 5000,
    algorithm = "sampling", family = binomial()
  )
}, times = 10L)
summary(fit.binomal)

sim_poisson_data <- generate_sim_data(type = "poisson")
benchmark.poisson <- microbenchmark::microbenchmark({
  fit.poisson <- brm(y ~ 0 + intercept + x1 + x2 + gp(d1, d2),
    sim_poisson_data,
    prior = prior,
    chains = 4, thin = 5, iter = 15000, warmup = 5000, 
    algorithm = "sampling", family = poisson()
  )
}, times = 10L)
summary(fit.poisson)
plot(fit.poisson)
```

STAN 代码模拟高斯过程，自协方差函数见方程 \ref{eq:exp-quad}


```
data {
  int<lower=1> N;
  real x[N];
}
transformed data {
  matrix[N, N] K;
  vector[N] mu = rep_vector(0, N);
  for (i in 1:(N - 1)) {
    K[i, i] = 1 + 0.1;
    for (j in (i + 1):N) {
      K[i, j] = exp(-0.5 * square(x[i] - x[j]));
      K[j, i] = K[i, j];
    }
  }
  K[N, N] = 1 + 0.1;
}
parameters {
  vector[N] y;
}
model {
  y ~ multi_normal(mu, K);
}
```



