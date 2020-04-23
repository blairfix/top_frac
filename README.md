# top_frac

`top_frac` is an R function that calculates the income fraction held by the top *x* fraction of earners.  It is implemented in [RcppArmadillo](https://cran.r-project.org/web/packages/RcppArmadillo/index.html).


### Inputs

* `pay` = a vector of raw income values
* `frac` =  the desired top-income fraction (as a decimal, not a percentage)

### Output
`top_frac` returns the income share of the top *x* fraction of earners.


### Example:

```R
library(RcppArmadillo)
library(Rcpp)

sourceCpp("top_frac.cpp")

# lognormal distribution of income
pay = rlnorm(10^6, 1, 1)

# get income share of top 1%

top_frac(pay, frac = 0.01)

[1] 0.09186115

```


### Installation
To use `top_frac`, install the following R packages:
 * [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) 
 * [RcppArmadillo](https://cran.r-project.org/web/packages/RcppArmadillo/index.html) 

Put the source code (`top_frac.cpp`) in the directory of your R script. Then source it with the command `sourceCpp(top_frac.cpp)`.


### Performance

It is not hard to write simple R function to calculate the income share of top earners. (See below.) However, `top_frac` is far faster than this simple function. This is because `top_frac` is written in C++, which is faster than R. It's also because `top_frac` uses a partial sort. It only sorts the top incomes (not all incomes). That makes `top_frac` about 20 times faster than a simpe R function.



```R

# a simple R function to get top income share

top_frac_R = function(pay, frac){
  
  pay = sort(x, decreasing = T)
  id_frac = length(x) * frac
  top_share = sum(pay[1:id_frac]) / sum(pay)
  
  return(top_share)
    
}

# performance
pay = rlnorm(10^6, 1, 1)

library(microbenchmark)

microbenchmark(
  top_frac(pay, frac = 0.01),  
  top_frac_R(pay, frac = 0.01),
  times = 100
)


Unit: milliseconds
       expr        min         lq       mean    median         uq        max neval
   top_frac   5.860983   6.094036   6.392669   6.17193   6.290681   8.070248   100
 top_frac_R 118.738301 120.774631 134.244602 122.76915 126.124941 199.610647   100

```




