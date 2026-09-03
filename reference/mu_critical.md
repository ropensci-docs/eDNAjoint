# Calculate mu_critical

This function uses the full posterior distributions of parameters
estimated by
[`joint_model()`](https://docs.ropensci.org/eDNAjoint/reference/joint_model.md)
to calculate mu_critical, or the expected catch rate at which the
probabilities of a false positive eDNA detection and true positive eDNA
detection are equal. See more examples in the [Package
Vignette](https://ednajoint.netlify.app).

## Usage

``` r
mu_critical(model_fit, cov_val = NULL, ci = 0.9)
```

## Arguments

- model_fit:

  An object of class `stanfit`

- cov_val:

  A numeric vector indicating the values of site-level covariates to use
  for prediction. Default is NULL.

- ci:

  Credible interval calculated using highest density interval (HDI).
  Default is 0.9 (i.e., 90% credible interval).

## Value

A list with median mu_critical and lower and upper bounds on the
credible interval. If multiple gear types are used, a table of
mu_critical and lower and upper credible interval bounds is returned
with one column for each gear type.

## Note

Before fitting the model, this function checks to ensure that the
function is possible given the inputs. These checks include:

- Input model fit is an object of class 'stanfit'.

- Input credible interval is a univariate numeric value greater than 0
  and less than 1.

- Input model fit contains p10 parameter.

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
# Ex. 1: Calculating mu_critical with site-level covariates

# Load data
data(goby_data)

# Fit a model including 'Filter_time' and 'Salinity' site-level covariates
fit_cov <- joint_model(data = goby_data, cov = c('Filter_time','Salinity'),
                       family = "poisson", p10_priors = c(1,20), q = FALSE,
                       multicore = FALSE)
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 1).
#> Chain 1: 
#> Chain 1: Gradient evaluation took 2.9e-05 seconds
#> Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 0.29 seconds.
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
#> Chain 1:  Elapsed Time: 0.229 seconds (Warm-up)
#> Chain 1:                0.581 seconds (Sampling)
#> Chain 1:                0.81 seconds (Total)
#> Chain 1: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 2).
#> Chain 2: 
#> Chain 2: Gradient evaluation took 2.1e-05 seconds
#> Chain 2: 1000 transitions using 10 leapfrog steps per transition would take 0.21 seconds.
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
#> Chain 2:  Elapsed Time: 2.091 seconds (Warm-up)
#> Chain 2:                0.594 seconds (Sampling)
#> Chain 2:                2.685 seconds (Total)
#> Chain 2: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 3).
#> Chain 3: 
#> Chain 3: Gradient evaluation took 2.1e-05 seconds
#> Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 0.21 seconds.
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
#> Chain 3:  Elapsed Time: 0.251 seconds (Warm-up)
#> Chain 3:                0.593 seconds (Sampling)
#> Chain 3:                0.844 seconds (Total)
#> Chain 3: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 4).
#> Chain 4: 
#> Chain 4: Gradient evaluation took 2.1e-05 seconds
#> Chain 4: 1000 transitions using 10 leapfrog steps per transition would take 0.21 seconds.
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
#> Chain 4:  Elapsed Time: 0.198 seconds (Warm-up)
#> Chain 4:                0.61 seconds (Sampling)
#> Chain 4:                0.808 seconds (Total)
#> Chain 4: 
#> Refer to the eDNAjoint guide for visualization tips:  https://ednajoint.netlify.app/tips#visualization-tips 

# Calculate mu_critical at the mean covariate values (covariates are
# standardized, so mean = 0)
mu_critical(fit_cov$model, cov_val = c(0,0), ci = 0.9)
#> $median
#> [1] 0.00528536
#> 
#> $lower_ci
#> Highest Density Interval: 1.79e-03
#> 
#> $upper_ci
#> Highest Density Interval: 9.63e-03
#> 

# Calculate mu_critical at habitat size 0.5 z-scores greater than the mean
mu_critical(fit_cov$model, cov_val = c(0,0.5), ci = 0.9)
#> $median
#> [1] 0.004416897
#> 
#> $lower_ci
#> Highest Density Interval: 1.56e-03
#> 
#> $upper_ci
#> Highest Density Interval: 8.23e-03
#> 

# Ex. 2: Calculating mu_critical with multiple traditional gear types

# Load data
data(green_crab_data)

# Fit a model with no site-level covariates
fit_q <- joint_model(data = green_crab_data, cov = NULL, family = "negbin",
                     p10_priors = c(1,20), q = TRUE, multicore = FALSE)
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 1).
#> Chain 1: 
#> Chain 1: Gradient evaluation took 0.000467 seconds
#> Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 4.67 seconds.
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
#> Chain 1:  Elapsed Time: 2.728 seconds (Warm-up)
#> Chain 1:                7.467 seconds (Sampling)
#> Chain 1:                10.195 seconds (Total)
#> Chain 1: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 2).
#> Chain 2: 
#> Chain 2: Gradient evaluation took 0.000272 seconds
#> Chain 2: 1000 transitions using 10 leapfrog steps per transition would take 2.72 seconds.
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
#> Chain 2:  Elapsed Time: 2.941 seconds (Warm-up)
#> Chain 2:                9.975 seconds (Sampling)
#> Chain 2:                12.916 seconds (Total)
#> Chain 2: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 3).
#> Chain 3: 
#> Chain 3: Gradient evaluation took 0.000268 seconds
#> Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 2.68 seconds.
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
#> Chain 3:  Elapsed Time: 2.874 seconds (Warm-up)
#> Chain 3:                7.366 seconds (Sampling)
#> Chain 3:                10.24 seconds (Total)
#> Chain 3: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 4).
#> Chain 4: 
#> Chain 4: Gradient evaluation took 0.000268 seconds
#> Chain 4: 1000 transitions using 10 leapfrog steps per transition would take 2.68 seconds.
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
#> Chain 4:  Elapsed Time: 2.955 seconds (Warm-up)
#> Chain 4:                8.557 seconds (Sampling)
#> Chain 4:                11.512 seconds (Total)
#> Chain 4: 
#> Refer to the eDNAjoint guide for visualization tips:  https://ednajoint.netlify.app/tips#visualization-tips 

# Calculate mu_critical
mu_critical(fit_q$model, cov_val = NULL, ci = 0.9)
#>              gear_1     gear_2
#> median   0.05962583 0.04601941
#> lower_ci 0.00982894 0.00700122
#> upper_ci 0.13537095 0.10428428
# }
```
