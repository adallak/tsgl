# tsglasso
## Introduction

The `tsglasso` is a package that implements time series graphical lasso as described in Dallakyan et. al 2022 (https://www.sciencedirect.com/science/article/abs/pii/S0167947322001372)
and Tugnait 2018 (https://ieeexplore.ieee.org/document/8645324).

The main function is `tsglasso()`, which takes a data matrix as an input and returns the estimate of (inverse) spectral density matrix. We also provide a usage of `tsglasso()` in the context of estimating sparse VAR coefficient matrix using
`msvar()` function.

## Installation

To install the latest version from Github, use

```s
library(devtools)
devtools::install_github("adallak/tsgl")
```

## Usage
The package contains functions `simulateVAR()` and `gendiagAR*(` for generating time series data.
```s
set.seed(12)
K = 25   ## Data dimension
N = 512  ## Number of observations
block = 5 ## Number of blocks
p = 1    ## Number of lags
burn = 300
Sigma = diag(K)
A = gendiagAR(K = K, p = p, block = block)
dta = simulateVAR(coefMatr=A, intercept =rep(0,K),
                  size=N, burn=burn,
                  Sigma=Sigma, error.dist="normal")$simData
```

## Estimating inverse spectral density matrix with a fixed tuning parameter. 

```s
halfWindowLength     = 12
ispd = tsglasso(dta, lambda = 0.01, method = "strict", halfWindowLength = halfWindowLength)$X
ispd = postprocess(ispd, threshold = 0.1)
```


## Estimating modified sparse VAR as described in Dallakyan et. al 2022 (https://www.sciencedirect.com/science/article/abs/pii/S0167947322001372)


```s
p.seq      		     = c(1,2,3)
p.ub       		     = max(p.seq)
msVAR.result = msvar(dta = dta, p.seq = p.seq, lambda = 0.1,
                     halfWindowLength = halfWindowLength, 
                     stage1.showStatus = FALSE, 
                     stage2.showStatus = FALSE,           
                     standardize = FALSE, 
                     stage1.info = "bic", stage2.info = "bic",
                     iteMax = 300, reltol = 1e-3,
                     rho.flex = TRUE, rho = 10) 
msvar_A = msVAR.result$stage2$estA
msvarAadj = getcoefadj(msvar_A)
## Print the result
plot_mat(msvarAadj, main = "msVAR")
```
