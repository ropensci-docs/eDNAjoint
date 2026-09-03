# Calculate the survey effort necessary to detect species presence, given the species expected catch rate.

This function calculates the number of survey effort units to necessary
detect species presence using median estimated parameter values from
joint_model(). Detecting species presence is defined as producing at
least one true positive eDNA detection or catching at least one
individual. See more examples in the [Package
Vignette](https://ednajoint.netlify.app/).

## Usage

``` r
detection_calculate(
  model_fit,
  mu,
  cov_val = NULL,
  probability = 0.9,
  pcr_n = 3
)
```

## Arguments

- model_fit:

  An object of class `stanfit`.

- mu:

  A numeric vector of species densities/capture rates. If multiple
  traditional gear types are represented in the model, mu is the catch
  rate of gear type 1.

- cov_val:

  A numeric vector indicating the values of site-level covariates to use
  for prediction. Default is NULL.

- probability:

  A numeric value indicating the probability of detecting presence. The
  default is 0.9.

- pcr_n:

  An integer indicating the number of PCR replicates per eDNA sample.
  The default is 3.

## Value

A summary table of survey efforts necessary to detect species presence,
given mu, for each survey type.

## Note

Before fitting the model, this function checks to ensure that the
function is possible given the inputs. These checks include:

- Input model fit is an object of class 'stanfit'.

- Input mu is a numeric vector.

- Input probability is a univariate numeric value.

- If model fit contains alpha, cov_val must be provided.

- Input cov_val is numeric.

- Input cov_val is the same length as the number of estimated
  covariates.

- Input model fit has converged (i.e. no divergent transitions after
  warm-up).

If any of these checks fail, the function returns an error message.

## Examples

``` r
# \donttest{
# Ex. 1: Calculating necessary effort for detection with site-level
# covariates

# Load data
data(goby_data)

# Fit a model including 'Filter_time' and 'Salinity' site-level covariates
fit_cov <- joint_model(data = goby_data, cov = c('Filter_time','Salinity'),
                       family = "poisson", p10_priors = c(1,20), q = FALSE,
                       multicore = FALSE)
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 1).
#> Chain 1: 
#> Chain 1: Gradient evaluation took 5.1e-05 seconds
#> Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 0.51 seconds.
#> Chain 1: Adjust your expectations accordingly!
#> Chain 1: 
#> Chain 1: 
#> Chain 1: Iteration:    1 / 3000 [  0%]  (Warmup)
#> Chain 1: Iteration:  500 / 3000 [ 16%]  (Warmup)
#> Chain 1: Iteration:  501 / 3000 [ 16%]  (Sampling)
#> Chain 1: Iteration: 1000 / 3000 [ 33%]  (Sampling)
#> Chain 1: Iteration: 1500 / 3000 [ 50%]  (Sampling)
#> Chain 1: Iteration: 2000 / 3000 [ 66%]  (Sampling)
#> Chain 1: Iteration: 2500 / 3000 [ 83%]  (Sampling)
#> Chain 1: Iteration: 3000 / 3000 [100%]  (Sampling)
#> Chain 1: 
#> Chain 1:  Elapsed Time: 0.221 seconds (Warm-up)
#> Chain 1:                0.647 seconds (Sampling)
#> Chain 1:                0.868 seconds (Total)
#> Chain 1: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 2).
#> Chain 2: 
#> Chain 2: Gradient evaluation took 3.4e-05 seconds
#> Chain 2: 1000 transitions using 10 leapfrog steps per transition would take 0.34 seconds.
#> Chain 2: Adjust your expectations accordingly!
#> Chain 2: 
#> Chain 2: 
#> Chain 2: Iteration:    1 / 3000 [  0%]  (Warmup)
#> Chain 2: Iteration:  500 / 3000 [ 16%]  (Warmup)
#> Chain 2: Iteration:  501 / 3000 [ 16%]  (Sampling)
#> Chain 2: Iteration: 1000 / 3000 [ 33%]  (Sampling)
#> Chain 2: Iteration: 1500 / 3000 [ 50%]  (Sampling)
#> Chain 2: Iteration: 2000 / 3000 [ 66%]  (Sampling)
#> Chain 2: Iteration: 2500 / 3000 [ 83%]  (Sampling)
#> Chain 2: Iteration: 3000 / 3000 [100%]  (Sampling)
#> Chain 2: 
#> Chain 2:  Elapsed Time: 0.282 seconds (Warm-up)
#> Chain 2:                0.612 seconds (Sampling)
#> Chain 2:                0.894 seconds (Total)
#> Chain 2: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 3).
#> Chain 3: 
#> Chain 3: Gradient evaluation took 2.7e-05 seconds
#> Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 0.27 seconds.
#> Chain 3: Adjust your expectations accordingly!
#> Chain 3: 
#> Chain 3: 
#> Chain 3: Iteration:    1 / 3000 [  0%]  (Warmup)
#> Chain 3: Iteration:  500 / 3000 [ 16%]  (Warmup)
#> Chain 3: Iteration:  501 / 3000 [ 16%]  (Sampling)
#> Chain 3: Iteration: 1000 / 3000 [ 33%]  (Sampling)
#> Chain 3: Iteration: 1500 / 3000 [ 50%]  (Sampling)
#> Chain 3: Iteration: 2000 / 3000 [ 66%]  (Sampling)
#> Chain 3: Iteration: 2500 / 3000 [ 83%]  (Sampling)
#> Chain 3: Iteration: 3000 / 3000 [100%]  (Sampling)
#> Chain 3: 
#> Chain 3:  Elapsed Time: 0.33 seconds (Warm-up)
#> Chain 3:                0.601 seconds (Sampling)
#> Chain 3:                0.931 seconds (Total)
#> Chain 3: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 4).
#> Chain 4: 
#> Chain 4: Gradient evaluation took 3.5e-05 seconds
#> Chain 4: 1000 transitions using 10 leapfrog steps per transition would take 0.35 seconds.
#> Chain 4: Adjust your expectations accordingly!
#> Chain 4: 
#> Chain 4: 
#> Chain 4: Iteration:    1 / 3000 [  0%]  (Warmup)
#> Chain 4: Iteration:  500 / 3000 [ 16%]  (Warmup)
#> Chain 4: Iteration:  501 / 3000 [ 16%]  (Sampling)
#> Chain 4: Iteration: 1000 / 3000 [ 33%]  (Sampling)
#> Chain 4: Iteration: 1500 / 3000 [ 50%]  (Sampling)
#> Chain 4: Iteration: 2000 / 3000 [ 66%]  (Sampling)
#> Chain 4: Iteration: 2500 / 3000 [ 83%]  (Sampling)
#> Chain 4: Iteration: 3000 / 3000 [100%]  (Sampling)
#> Chain 4: 
#> Chain 4:  Elapsed Time: 0.249 seconds (Warm-up)
#> Chain 4:                0.608 seconds (Sampling)
#> Chain 4:                0.857 seconds (Total)
#> Chain 4: 
#> Refer to the eDNAjoint guide for visualization tips:  https://ednajoint.netlify.app/tips#visualization-tips 

# Calculate at the mean covariate values
# (covariates are standardized, so mean = 0)
detection_calculate(fit_cov$model, mu = seq(from = 0.1, to = 1, by = 0.1),
                    cov_val = c(0,0), pcr_n = 3)
#>        mu n_traditional n_eDNA
#>  [1,] 0.1            24     14
#>  [2,] 0.2            12      7
#>  [3,] 0.3             8      5
#>  [4,] 0.4             6      4
#>  [5,] 0.5             5      4
#>  [6,] 0.6             4      3
#>  [7,] 0.7             4      3
#>  [8,] 0.8             3      3
#>  [9,] 0.9             3      2
#> [10,] 1.0             3      2

# Calculate mu_critical at salinity 0.5 z-scores greater than the mean
detection_calculate(fit_cov$model, mu = seq(from = 0.1, to = 1, by = 0.1),
                    cov_val = c(0,0.5), pcr_n = 3)
#>        mu n_traditional n_eDNA
#>  [1,] 0.1            24     12
#>  [2,] 0.2            12      6
#>  [3,] 0.3             8      5
#>  [4,] 0.4             6      4
#>  [5,] 0.5             5      3
#>  [6,] 0.6             4      3
#>  [7,] 0.7             4      2
#>  [8,] 0.8             3      2
#>  [9,] 0.9             3      2
#> [10,] 1.0             3      2

# Ex. 2: Calculating necessary effort for detection with multiple traditional
# gear types

# Load data
data(green_crab_data)

# Fit a model with no site-level covariates
fit_q <- joint_model(data = green_crab_data, cov = NULL, family = "negbin",
                     p10_priors = c(1,20), q = TRUE, multicore = FALSE)
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 1).
#> Chain 1: 
#> Chain 1: Gradient evaluation took 0.000308 seconds
#> Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 3.08 seconds.
#> Chain 1: Adjust your expectations accordingly!
#> Chain 1: 
#> Chain 1: 
#> Chain 1: Iteration:    1 / 3000 [  0%]  (Warmup)
#> Chain 1: Iteration:  500 / 3000 [ 16%]  (Warmup)
#> Chain 1: Iteration:  501 / 3000 [ 16%]  (Sampling)
#> Chain 1: Iteration: 1000 / 3000 [ 33%]  (Sampling)
#> Chain 1: Iteration: 1500 / 3000 [ 50%]  (Sampling)
#> Chain 1: Iteration: 2000 / 3000 [ 66%]  (Sampling)
#> Chain 1: Iteration: 2500 / 3000 [ 83%]  (Sampling)
#> Chain 1: Iteration: 3000 / 3000 [100%]  (Sampling)
#> Chain 1: 
#> Chain 1:  Elapsed Time: 2.783 seconds (Warm-up)
#> Chain 1:                7.358 seconds (Sampling)
#> Chain 1:                10.141 seconds (Total)
#> Chain 1: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 2).
#> Chain 2: 
#> Chain 2: Gradient evaluation took 0.000255 seconds
#> Chain 2: 1000 transitions using 10 leapfrog steps per transition would take 2.55 seconds.
#> Chain 2: Adjust your expectations accordingly!
#> Chain 2: 
#> Chain 2: 
#> Chain 2: Iteration:    1 / 3000 [  0%]  (Warmup)
#> Chain 2: Iteration:  500 / 3000 [ 16%]  (Warmup)
#> Chain 2: Iteration:  501 / 3000 [ 16%]  (Sampling)
#> Chain 2: Iteration: 1000 / 3000 [ 33%]  (Sampling)
#> Chain 2: Iteration: 1500 / 3000 [ 50%]  (Sampling)
#> Chain 2: Iteration: 2000 / 3000 [ 66%]  (Sampling)
#> Chain 2: Iteration: 2500 / 3000 [ 83%]  (Sampling)
#> Chain 2: Iteration: 3000 / 3000 [100%]  (Sampling)
#> Chain 2: 
#> Chain 2:  Elapsed Time: 2.837 seconds (Warm-up)
#> Chain 2:                8.023 seconds (Sampling)
#> Chain 2:                10.86 seconds (Total)
#> Chain 2: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 3).
#> Chain 3: 
#> Chain 3: Gradient evaluation took 0.000238 seconds
#> Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 2.38 seconds.
#> Chain 3: Adjust your expectations accordingly!
#> Chain 3: 
#> Chain 3: 
#> Chain 3: Iteration:    1 / 3000 [  0%]  (Warmup)
#> Chain 3: Iteration:  500 / 3000 [ 16%]  (Warmup)
#> Chain 3: Iteration:  501 / 3000 [ 16%]  (Sampling)
#> Chain 3: Iteration: 1000 / 3000 [ 33%]  (Sampling)
#> Chain 3: Iteration: 1500 / 3000 [ 50%]  (Sampling)
#> Chain 3: Iteration: 2000 / 3000 [ 66%]  (Sampling)
#> Chain 3: Iteration: 2500 / 3000 [ 83%]  (Sampling)
#> Chain 3: Iteration: 3000 / 3000 [100%]  (Sampling)
#> Chain 3: 
#> Chain 3:  Elapsed Time: 2.718 seconds (Warm-up)
#> Chain 3:                8.385 seconds (Sampling)
#> Chain 3:                11.103 seconds (Total)
#> Chain 3: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 4).
#> Chain 4: 
#> Chain 4: Gradient evaluation took 0.000271 seconds
#> Chain 4: 1000 transitions using 10 leapfrog steps per transition would take 2.71 seconds.
#> Chain 4: Adjust your expectations accordingly!
#> Chain 4: 
#> Chain 4: 
#> Chain 4: Iteration:    1 / 3000 [  0%]  (Warmup)
#> Chain 4: Iteration:  500 / 3000 [ 16%]  (Warmup)
#> Chain 4: Iteration:  501 / 3000 [ 16%]  (Sampling)
#> Chain 4: Iteration: 1000 / 3000 [ 33%]  (Sampling)
#> Chain 4: Iteration: 1500 / 3000 [ 50%]  (Sampling)
#> Chain 4: Iteration: 2000 / 3000 [ 66%]  (Sampling)
#> Chain 4: Iteration: 2500 / 3000 [ 83%]  (Sampling)
#> Chain 4: Iteration: 3000 / 3000 [100%]  (Sampling)
#> Chain 4: 
#> Chain 4:  Elapsed Time: 2.8 seconds (Warm-up)
#> Chain 4:                5.56 seconds (Sampling)
#> Chain 4:                8.36 seconds (Total)
#> Chain 4: 
#> Refer to the eDNAjoint guide for visualization tips:  https://ednajoint.netlify.app/tips#visualization-tips 

# Calculate
detection_calculate(fit_q$model, mu = seq(from = 0.1, to = 1, by = 0.1),
                    cov_val = NULL, pcr_n = 3)
#>        mu n_traditional_1 n_traditional_2 n_eDNA
#>  [1,] 0.1              25              32     28
#>  [2,] 0.2              13              17     15
#>  [3,] 0.3               9              12     10
#>  [4,] 0.4               7               9      8
#>  [5,] 0.5               6               8      6
#>  [6,] 0.6               6               7      5
#>  [7,] 0.7               5               6      5
#>  [8,] 0.8               5               5      4
#>  [9,] 0.9               4               5      4
#> [10,] 1.0               4               5      4

# Change probability of detecting presence to 0.95
detection_calculate(fit_q$model, mu = 0.1, cov_val = NULL,
                    probability = 0.95, pcr_n = 3)
#>       mu n_traditional_1 n_traditional_2 n_eDNA
#> [1,] 0.1              32              41     37
# }
```
