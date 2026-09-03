# Specify and fit joint model using count data from traditional surveys and eDNA PCR data

This function implements a Bayesian model that integrates data from
paired eDNA and traditional surveys, as described in Keller et al.
(2022) \<doi.org/10.1002/eap.2561\>. The model estimates parameters
including the expected species catch rate and the probability of false
positive eDNA detection. This function allows for optional model
variations, like inclusion of site-level covariates that scale the
sensitivity of eDNA sampling relative to traditional sampling, as well
as estimation of gear scaling coefficients that scales the relative
catchability of multiple traditional gear types. Model is implemented
using Bayesian inference using the `rstan` package, which uses
Hamiltonian Monte Carlo to simulate the posterior distributions. See
more examples in the [Package Vignette](https://ednajoint.netlify.app/).

## Usage

``` r
joint_model(
  data,
  cov = NULL,
  family = "poisson",
  p10_priors = c(1, 20),
  q = FALSE,
  phi_priors = NULL,
  multicore = FALSE,
  initial_values = NULL,
  n_chain = 4,
  n_warmup = 500,
  n_iter = 3000,
  thin = 1,
  adapt_delta = 0.9,
  verbose = TRUE,
  seed = NULL
)
```

## Arguments

- data:

  A list containing data necessary for model fitting. Valid tags are
  `pcr_n`, `pcr_k`, `count`, `count_type`, and `site_cov`. `pcr_n` and
  `pcr_k` are matrices or data frames with first dimension equal to the
  number of sites (i) and second dimension equal to the maximum number
  of eDNA samples at a given site (m). `pcr_n` contains the total number
  of PCR replicates per site and eDNA sample, and `pcr_k` contains the
  total number of positive PCR detections per site and eDNA sample.
  `count` is a matrix or data frame of traditional survey count data,
  with first dimension equal to the number of sites (i) and second
  dimension equal to the maximum number of traditional survey replicates
  at a given site (j). `count_type` is an optional matrix or data frame
  of integers indicating gear type used in corresponding count data,
  with first dimension equal to the number of sites (i) and second
  dimension equal to the maximum number of traditional survey replicates
  at a given site. Values should be integers beginning with 1 (referring
  to the first gear type) to n (last gear type). `site_cov` is an
  optional matrix or data frame of site-level covariate data, with first
  dimension equal to the number of sites (i). `site_cov` should include
  informative column names. Empty cells should be NA and will be removed
  during processing. Sites, i, should be consistent in all PCR, count,
  and site covariate data.

- cov:

  A character vector indicating the site-level covariates to include in
  the model. Default value is NULL.

- family:

  The distribution class used to model traditional survey count data.
  Options include poisson ('poisson'), negative binomial ('negbin'), and
  gamma ('gamma'). Default value is 'poisson'.

- p10_priors:

  A numeric vector indicating beta distribution hyperparameters (alpha,
  beta) used as the prior distribution for the eDNA false positive
  probability (p10). Default vector is c(1,20).

- q:

  A logical value indicating whether to estimate gear scaling
  coefficients, q, for traditional survey gear types (TRUE) or to not
  estimate gear scaling coefficients, q, for traditional survey gear
  types (FALSE). Default value is FALSE.

- phi_priors:

  A numeric vector indicating gamma distribution hyperparameters (shape,
  rate) used as the prior distribution for phi, the overdispersion in
  the negative binomial distribution for traditional survey gear data.
  Used when family = 'negbin.' If family = 'negbin', then default vector
  is c(0.25,0.25), otherwise, default is NULL.

- multicore:

  A logical value indicating whether to parallelize chains with multiple
  cores. Default is FALSE.

- initial_values:

  A list of lists of initial values to use in MCMC. The length should
  equal the number of MCMC chains. Initial values can be provided for
  parameters: beta, p10 (log-scale), mu, q, alpha. If no initial values
  are provided, default random values are drawn.

- n_chain:

  Number of MCMC chains. Default value is 4.

- n_warmup:

  A positive integer specifying the number of warm-up MCMC iterations.
  Default value is 500.

- n_iter:

  A positive integer specifying the number of iterations for each chain
  (including warmup). Default value is 3000.

- thin:

  A positive integer specifying the period for saving samples. Default
  value is 1.

- adapt_delta:

  Numeric value between 0 and 1 indicating target average acceptance
  probability used in
  [`rstan::sampling`](https://mc-stan.org/rstan/reference/stanmodel-method-sampling.html).
  Default value is 0.9.

- verbose:

  Logical value controlling the verbosity of output (i.e., warnings,
  messages, progress bar). Default is TRUE.

- seed:

  A positive integer seed used for random number generation in MCMC.
  Default is NULL, which means the seed is generated from 1 to the
  maximum integer supported by R.

## Value

A list of:

- a model object of class `stanfit` returned by
  [`rstan::sampling`](https://mc-stan.org/rstan/reference/stanmodel-method-sampling.html)

- initial values used in MCMC

## Note

Before fitting the model, this function checks to ensure that the model
specification is possible given the data files. These checks include:

- All tags in data are valid (i.e., include pcr_n, pcr_k, count,
  count_type, and site_cov).

- Dimensions of pcr_n and pcr_k are equal, and dimensions of count and
  count_type are equal (if count_type provided).

- Number of sites in PCR and count data are equal.

- All data are numeric (i.e., integer or NA).

- Empty data cells (NA) match in pcr_n and pcr_k and in count and
  count_type.

- family is either 'poisson', 'negbin', or 'gamma'.

- p10_priors and phi_priors (if used) is a vector of two numeric values.

- site_cov has same number of rows as pcr_n and count, if present

- site_cov is numeric, if present

- cov values match column names in site_cov, if present

If any of these checks fail, the function returns an error message.

## Examples

``` r
# \donttest{
# Ex. 1: Implementing the joint model

# Load data
data(goby_data)

# Examine data in list
names(goby_data)
#> [1] "pcr_n"    "pcr_k"    "count"    "site_cov"

# Note that the surveyed sites (rows) should match in all data
dim(goby_data$pcr_n)[1]
#> [1] 39
dim(goby_data$count)[1]
#> [1] 39

# Fit a basic model with paired eDNA and traditional survey data.
# Count data is modeled using a poisson distribution.
fit <- joint_model(data = goby_data, family = "poisson",
                   p10_priors = c(1, 20),
                   multicore = FALSE)
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 1).
#> Chain 1: 
#> Chain 1: Gradient evaluation took 3.1e-05 seconds
#> Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 0.31 seconds.
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
#> Chain 1:  Elapsed Time: 0.302 seconds (Warm-up)
#> Chain 1:                0.595 seconds (Sampling)
#> Chain 1:                0.897 seconds (Total)
#> Chain 1: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 2).
#> Chain 2: 
#> Chain 2: Gradient evaluation took 2.3e-05 seconds
#> Chain 2: 1000 transitions using 10 leapfrog steps per transition would take 0.23 seconds.
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
#> Chain 2:  Elapsed Time: 0.336 seconds (Warm-up)
#> Chain 2:                0.608 seconds (Sampling)
#> Chain 2:                0.944 seconds (Total)
#> Chain 2: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 3).
#> Chain 3: 
#> Chain 3: Gradient evaluation took 2.3e-05 seconds
#> Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 0.23 seconds.
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
#> Chain 3:  Elapsed Time: 0.326 seconds (Warm-up)
#> Chain 3:                0.579 seconds (Sampling)
#> Chain 3:                0.905 seconds (Total)
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
#> Chain 4:  Elapsed Time: 0.323 seconds (Warm-up)
#> Chain 4:                0.62 seconds (Sampling)
#> Chain 4:                0.943 seconds (Total)
#> Chain 4: 
#> Refer to the eDNAjoint guide for visualization tips:  https://ednajoint.netlify.app/tips#visualization-tips 

# Ex. 2: Implementing the joint model with site-level covariates

# With the same data, fit a model including 'Filter_time' and 'Salinity'
# site-level covariates
# These covariates will scale the sensitivity of eDNA sampling relative to
# traditional surveys
# Count data is modeled using a poisson distribution.
fit_cov <- joint_model(data = goby_data, cov = c('Filter_time','Salinity'),
                       family = "poisson", p10_priors = c(1, 20),
                       multicore = FALSE)
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 1).
#> Chain 1: 
#> Chain 1: Gradient evaluation took 3.1e-05 seconds
#> Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 0.31 seconds.
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
#> Chain 1:  Elapsed Time: 0.334 seconds (Warm-up)
#> Chain 1:                0.634 seconds (Sampling)
#> Chain 1:                0.968 seconds (Total)
#> Chain 1: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 2).
#> Chain 2: 
#> Chain 2: Gradient evaluation took 2.3e-05 seconds
#> Chain 2: 1000 transitions using 10 leapfrog steps per transition would take 0.23 seconds.
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
#> Chain 2:  Elapsed Time: 1.602 seconds (Warm-up)
#> Chain 2:                0.609 seconds (Sampling)
#> Chain 2:                2.211 seconds (Total)
#> Chain 2: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 3).
#> Chain 3: 
#> Chain 3: Gradient evaluation took 2.3e-05 seconds
#> Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 0.23 seconds.
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
#> Chain 3:  Elapsed Time: 0.23 seconds (Warm-up)
#> Chain 3:                0.65 seconds (Sampling)
#> Chain 3:                0.88 seconds (Total)
#> Chain 3: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 4).
#> Chain 4: 
#> Chain 4: Gradient evaluation took 2.2e-05 seconds
#> Chain 4: 1000 transitions using 10 leapfrog steps per transition would take 0.22 seconds.
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
#> Chain 4:  Elapsed Time: 0.31 seconds (Warm-up)
#> Chain 4:                0.61 seconds (Sampling)
#> Chain 4:                0.92 seconds (Total)
#> Chain 4: 
#> Refer to the eDNAjoint guide for visualization tips:  https://ednajoint.netlify.app/tips#visualization-tips 


# Ex. 3: Implementing the joint model with multiple traditional gear types

# Load data
data(green_crab_data)

# Examine data in list
names(green_crab_data)
#> [1] "pcr_n"      "pcr_k"      "count"      "count_type"

# Note that the surveyed sites (rows) should match in all data
dim(green_crab_data$pcr_n)[1]
#> [1] 20
dim(green_crab_data$count)[1]
#> [1] 20

# Fit a model estimating a gear scaling coefficient for traditional survey
# gear types.
# This model does not assume all traditional survey methods have the same
# catchability.
# Count data is modeled using a negative binomial distribution.
fit_q <- joint_model(data = green_crab_data, cov = NULL, family = "negbin",
                     p10_priors = c(1, 20), q = TRUE,
                     phi_priors = c(0.25, 0.25),
                     multicore = FALSE, initial_values = NULL,
                     n_chain = 4, n_warmup = 500,
                     n_iter = 3000, thin = 1, adapt_delta = 0.9,
                     verbose = TRUE, seed = 123)
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 1).
#> Chain 1: 
#> Chain 1: Gradient evaluation took 0.000263 seconds
#> Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 2.63 seconds.
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
#> Chain 1:  Elapsed Time: 2.883 seconds (Warm-up)
#> Chain 1:                7.849 seconds (Sampling)
#> Chain 1:                10.732 seconds (Total)
#> Chain 1: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 2).
#> Chain 2: 
#> Chain 2: Gradient evaluation took 0.00032 seconds
#> Chain 2: 1000 transitions using 10 leapfrog steps per transition would take 3.2 seconds.
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
#> Chain 2:  Elapsed Time: 2.896 seconds (Warm-up)
#> Chain 2:                5.928 seconds (Sampling)
#> Chain 2:                8.824 seconds (Total)
#> Chain 2: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 3).
#> Chain 3: 
#> Chain 3: Gradient evaluation took 0.000302 seconds
#> Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 3.02 seconds.
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
#> Chain 3:  Elapsed Time: 2.895 seconds (Warm-up)
#> Chain 3:                7.571 seconds (Sampling)
#> Chain 3:                10.466 seconds (Total)
#> Chain 3: 
#> 
#> SAMPLING FOR MODEL 'joint_count' NOW (CHAIN 4).
#> Chain 4: 
#> Chain 4: Gradient evaluation took 0.000256 seconds
#> Chain 4: 1000 transitions using 10 leapfrog steps per transition would take 2.56 seconds.
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
#> Chain 4:  Elapsed Time: 2.687 seconds (Warm-up)
#> Chain 4:                7.75 seconds (Sampling)
#> Chain 4:                10.437 seconds (Total)
#> Chain 4: 
#> Refer to the eDNAjoint guide for visualization tips:  https://ednajoint.netlify.app/tips#visualization-tips 
# }
```
