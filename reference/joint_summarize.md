# Summarize posterior distributions of model parameters.

This function summarizes the posterior distributions of specified
parameters from a model fit. Summary includes mean, sd, and specified
quantiles, as well as effective sample size (n_eff) and Rhat for
estimated parameters. See more examples in the [Package
Vignette](https://ednajoint.netlify.app).

## Usage

``` r
joint_summarize(model_fit, par = "all", probs = c(0.025, 0.975), digits = 3)
```

## Arguments

- model_fit:

  An object of class `stanfit`.

- par:

  A character vector of parameter names. The default is 'all'.

- probs:

  A numeric vector of quantiles of interest. The default is
  c(0.025,0.975).

- digits:

  An integer indicating the number of decimal places to round values in
  summary table. Default value is 3.

## Value

A summary table of parameter estimates.

## Note

Before fitting the model, this function checks to ensure that the
function is possible given the inputs. These checks include:

- Input model fit is an object of class 'stanfit'.

- Input probs is a numeric vector.

- Input par is a character vector.

- Input par are present in fitted model.

- Input model fit has converged (i.e. no divergent transitions after
  warm-up).

If any of these checks fail, the function returns an error message.

## Examples

``` r
# \donttest{
data(green_crab_data)

# Fit a model
model_fit <- joint_model(data = green_crab_data, family = "negbin", q = TRUE,
                         multicore = FALSE)
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 1).
#> Chain 1: 
#> Chain 1: Gradient evaluation took 0.000294 seconds
#> Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 2.94 seconds.
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
#> Chain 1:  Elapsed Time: 2.94 seconds (Warm-up)
#> Chain 1:                7.274 seconds (Sampling)
#> Chain 1:                10.214 seconds (Total)
#> Chain 1: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 2).
#> Chain 2: 
#> Chain 2: Gradient evaluation took 0.000238 seconds
#> Chain 2: 1000 transitions using 10 leapfrog steps per transition would take 2.38 seconds.
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
#> Chain 2:  Elapsed Time: 2.889 seconds (Warm-up)
#> Chain 2:                8.564 seconds (Sampling)
#> Chain 2:                11.453 seconds (Total)
#> Chain 2: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 3).
#> Chain 3: 
#> Chain 3: Gradient evaluation took 0.000278 seconds
#> Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 2.78 seconds.
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
#> Chain 3:  Elapsed Time: 2.564 seconds (Warm-up)
#> Chain 3:                7.718 seconds (Sampling)
#> Chain 3:                10.282 seconds (Total)
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
#> Chain 4:  Elapsed Time: 2.753 seconds (Warm-up)
#> Chain 4:                8.637 seconds (Sampling)
#> Chain 4:                11.39 seconds (Total)
#> Chain 4: 
#> Refer to the eDNAjoint guide for visualization tips:  https://ednajoint.netlify.app/tips#visualization-tips 

# Create summary table of all parameters
joint_summarize(model_fit$model)
#>           mean se_mean    sd  2.5%  97.5%     n_eff Rhat
#> mu[1,1]  0.107   0.000 0.027 0.061  0.169 12702.739    1
#> mu[1,2]  0.083   0.000 0.022 0.046  0.133 11988.019    1
#> mu[2,1]  0.033   0.000 0.033 0.001  0.123 12975.708    1
#> mu[2,2]  0.025   0.000 0.026 0.001  0.096 13069.307    1
#> mu[3,1]  0.017   0.000 0.018 0.000  0.066 12256.007    1
#> mu[3,2]  0.013   0.000 0.014 0.000  0.051 12023.938    1
#> mu[4,1]  0.684   0.001 0.111 0.492  0.923 10476.855    1
#> mu[4,2]  0.530   0.001 0.091 0.373  0.725 13027.597    1
#> mu[5,1]  0.100   0.001 0.110 0.002  0.399 13716.752    1
#> mu[5,2]  0.078   0.001 0.086 0.002  0.302 13963.735    1
#> mu[6,1]  0.120   0.001 0.132 0.003  0.484 11750.037    1
#> mu[6,2]  0.093   0.001 0.102 0.002  0.373 11771.604    1
#> mu[7,1]  0.013   0.000 0.013 0.000  0.046 13321.659    1
#> mu[7,2]  0.010   0.000 0.010 0.000  0.036 13408.403    1
#> mu[8,1]  0.307   0.003 0.287 0.014  1.062 12443.399    1
#> mu[8,2]  0.238   0.002 0.223 0.011  0.823 13044.897    1
#> mu[9,1]  0.034   0.000 0.033 0.001  0.122 13782.811    1
#> mu[9,2]  0.026   0.000 0.025 0.001  0.093 13607.916    1
#> mu[10,1] 1.059   0.002 0.245 0.657  1.608 10772.437    1
#> mu[10,2] 0.819   0.002 0.188 0.510  1.244 13850.627    1
#> mu[11,1] 0.309   0.003 0.291 0.014  1.070 12613.458    1
#> mu[11,2] 0.239   0.002 0.225 0.010  0.819 12858.840    1
#> mu[12,1] 0.021   0.000 0.022 0.001  0.079 13458.178    1
#> mu[12,2] 0.016   0.000 0.017 0.000  0.061 13604.364    1
#> mu[13,1] 7.765   0.014 1.322 5.561 10.737  8689.664    1
#> mu[13,2] 5.999   0.008 0.973 4.360  8.190 13863.218    1
#> mu[14,1] 0.120   0.000 0.020 0.084  0.163 10735.956    1
#> mu[14,2] 0.093   0.000 0.016 0.064  0.126 13666.153    1
#> mu[15,1] 0.777   0.005 0.548 0.121  2.209 10675.283    1
#> mu[15,2] 0.602   0.004 0.423 0.094  1.692 11170.318    1
#> mu[16,1] 3.847   0.007 0.671 2.694  5.347  8953.153    1
#> mu[16,2] 2.968   0.004 0.476 2.153  4.007 15004.688    1
#> mu[17,1] 0.164   0.002 0.189 0.003  0.668 10913.547    1
#> mu[17,2] 0.127   0.001 0.147 0.002  0.517 11186.028    1
#> mu[18,1] 3.381   0.013 1.145 1.751  6.161  7640.158    1
#> mu[18,2] 2.616   0.010 0.888 1.355  4.796  8298.920    1
#> mu[19,1] 3.978   0.008 0.732 2.784  5.653  9087.636    1
#> mu[19,2] 3.081   0.005 0.587 2.135  4.402 11731.000    1
#> mu[20,1] 0.121   0.001 0.070 0.027  0.290 12980.607    1
#> mu[20,2] 0.093   0.000 0.054 0.021  0.223 13434.916    1
#> q[1]     0.780   0.001 0.101 0.603  0.996  7067.901    1
#> p10      0.018   0.000 0.010 0.005  0.042  8670.297    1
#> beta[1]  1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[2]  1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[3]  1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[4]  1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[5]  1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[6]  1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[7]  1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[8]  1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[9]  1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[10] 1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[11] 1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[12] 1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[13] 1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[14] 1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[15] 1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[16] 1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[17] 1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[18] 1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[19] 1.273   0.003 0.252 0.788  1.771  8270.141    1
#> beta[20] 1.273   0.003 0.252 0.788  1.771  8270.141    1
#> alpha[1] 1.273   0.003 0.252 0.788  1.771  8270.141    1
#> phi      0.914   0.001 0.129 0.688  1.196  9147.870    1

# Summarize just 'p10' parameter
joint_summarize(model_fit$model, par = "p10", probs = c(0.025, 0.975),
                digits = 3)
#>      mean se_mean   sd  2.5% 97.5%    n_eff Rhat
#> p10 0.018       0 0.01 0.005 0.042 8670.297    1
# }
```
