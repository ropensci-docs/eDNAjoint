# Perform model selection using leave one out cross validation of model objects

This function performs leave one out cross validation of a list of model
fits using functions in the `loo` package, as described in Vehtari,
Gelman, and Gabry (2017) <doi:10.1007/s11222-016-9696-4>. Compare models
fit using
[`joint_model()`](https://docs.ropensci.org/eDNAjoint/reference/joint_model.md)
or models fits using
[`traditional_model()`](https://docs.ropensci.org/eDNAjoint/reference/traditional_model.md).
See more examples in the [Package
Vignette](https://ednajoint.netlify.app).

## Usage

``` r
joint_select(model_fits)
```

## Arguments

- model_fits:

  A list containing model fits of class `stanfit`.

## Value

A matrix of delta elpd (expected log pointwise predictive density)
between model fits. Function is performed using the `loo` package.

## Note

Before model selection, this function makes the following check:

- Input is a list of model fits of class 'stanfit'.

- All models compared were fit wither either
  [`joint_model()`](https://docs.ropensci.org/eDNAjoint/reference/joint_model.md)
  or all with `traditional_model().`

If any of these checks fail, the function returns an error message.

## Examples

``` r
# \donttest{
data(green_crab_data)

# Fit a model without estimating a gear scaling coefficient for traditional
# survey gear types.
# This model assumes all traditional survey methods have the same
# catchability.
# Count data is modeled using a poisson distribution.
fit_no_q <- joint_model(data = green_crab_data, family = "poisson",
                        p10_priors = c(1,20), q = FALSE, multicore = FALSE)
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 1).
#> Chain 1: 
#> Chain 1: Gradient evaluation took 3.8e-05 seconds
#> Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 0.38 seconds.
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
#> Chain 1:  Elapsed Time: 0.312 seconds (Warm-up)
#> Chain 1:                0.826 seconds (Sampling)
#> Chain 1:                1.138 seconds (Total)
#> Chain 1: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 2).
#> Chain 2: 
#> Chain 2: Gradient evaluation took 3e-05 seconds
#> Chain 2: 1000 transitions using 10 leapfrog steps per transition would take 0.3 seconds.
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
#> Chain 2:  Elapsed Time: 0.293 seconds (Warm-up)
#> Chain 2:                0.846 seconds (Sampling)
#> Chain 2:                1.139 seconds (Total)
#> Chain 2: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 3).
#> Chain 3: 
#> Chain 3: Gradient evaluation took 3e-05 seconds
#> Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 0.3 seconds.
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
#> Chain 3:  Elapsed Time: 0.282 seconds (Warm-up)
#> Chain 3:                0.682 seconds (Sampling)
#> Chain 3:                0.964 seconds (Total)
#> Chain 3: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 4).
#> Chain 4: 
#> Chain 4: Gradient evaluation took 6.8e-05 seconds
#> Chain 4: 1000 transitions using 10 leapfrog steps per transition would take 0.68 seconds.
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
#> Chain 4:  Elapsed Time: 0.273 seconds (Warm-up)
#> Chain 4:                0.784 seconds (Sampling)
#> Chain 4:                1.057 seconds (Total)
#> Chain 4: 
#> Refer to the eDNAjoint guide for visualization tips:  https://ednajoint.netlify.app/tips#visualization-tips 


# Fit a model estimating a gear scaling coefficient for traditional
# survey gear types.
# This model does not assume all traditional survey methods have the
# same catchability.
# Gear type 1 is used as the reference gear type.
# Count data is modeled using a negative binomial distribution.
fit_q <- joint_model(data = green_crab_data, family = "negbin",
                     p10_priors = c(1,20), q = TRUE, multicore = FALSE)
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 1).
#> Chain 1: 
#> Chain 1: Gradient evaluation took 0.000256 seconds
#> Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 2.56 seconds.
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
#> Chain 1:  Elapsed Time: 3.021 seconds (Warm-up)
#> Chain 1:                9.773 seconds (Sampling)
#> Chain 1:                12.794 seconds (Total)
#> Chain 1: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 2).
#> Chain 2: 
#> Chain 2: Gradient evaluation took 0.000284 seconds
#> Chain 2: 1000 transitions using 10 leapfrog steps per transition would take 2.84 seconds.
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
#> Chain 2:  Elapsed Time: 2.715 seconds (Warm-up)
#> Chain 2:                9.054 seconds (Sampling)
#> Chain 2:                11.769 seconds (Total)
#> Chain 2: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 3).
#> Chain 3: 
#> Chain 3: Gradient evaluation took 0.000269 seconds
#> Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 2.69 seconds.
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
#> Chain 3:  Elapsed Time: 2.699 seconds (Warm-up)
#> Chain 3:                9.251 seconds (Sampling)
#> Chain 3:                11.95 seconds (Total)
#> Chain 3: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 4).
#> Chain 4: 
#> Chain 4: Gradient evaluation took 0.000283 seconds
#> Chain 4: 1000 transitions using 10 leapfrog steps per transition would take 2.83 seconds.
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
#> Chain 4:  Elapsed Time: 2.778 seconds (Warm-up)
#> Chain 4:                8.222 seconds (Sampling)
#> Chain 4:                11 seconds (Total)
#> Chain 4: 
#> Refer to the eDNAjoint guide for visualization tips:  https://ednajoint.netlify.app/tips#visualization-tips 

# Perform model selection
joint_select(model_fits = list(fit_no_q$model, fit_q$model))
#>   model elpd_diff se_diff p_worse diag_diff diag_elpd
#>  model2       0.0     0.0      NA                    
#>  model1    -164.9    39.1    1.00                    
# }
```
