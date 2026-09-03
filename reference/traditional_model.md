# Specify and fit model using count data from traditional, non eDNA surveys

This function implements a Bayesian model that estimates expected
species catch rate using count data from traditional, non eDNA surveys.
When multiple traditional gear types are used, an optional variation
allows estimation of gear scaling coefficients, which scale the
catchability of gear types relative to the expected catch rate of a
reference gear type. Model is implemented using Bayesian inference using
the `rstan` package, which uses Hamiltonian Monte Carlo to simulate the
posterior distributions. See more examples in the [Package
Vignette](https://ednajoint.netlify.app).

## Usage

``` r
traditional_model(
  data,
  family = "poisson",
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
  `count` and `count_type`. `count` is a matrix or data frame of
  traditional survey count data, with first dimension equal to the
  number of sites (i) and second dimension equal to the maximum number
  of traditional survey replicates at a given site (j). `count_type` is
  an optional matrix or data frame of integers indicating gear type (k)
  used in corresponding count data, with first dimension equal to the
  number of sites (i) and second dimension equal to the maximum number
  of traditional survey replicates at a given site (j). Values should be
  integers beginning with 1 (referring to the first gear type) to n
  (last gear type). Empty cells should be NA and will be removed during
  processing. Sites, i, should be consistent in all count data.

- family:

  The distribution class used to model traditional survey count data.
  Options include poisson ('poisson'), negative binomial ('negbin'), and
  gamma ('gamma'). Default value is 'poisson'.

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
  parameters: mu and q. If no initial values are provided, default
  random values are drawn.

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

- All tags in data are valid (i.e., include count and count_type).

- Number of sites in count and count type data are equal.

- All data are numeric (i.e., integer or NA).

- Empty data cells (NA) match in count and count_type.

- family is 'poisson', 'negbin', or 'gamma'.

- phi_priors (if used) is a vector of two numeric values.

If any of these checks fail, the function returns an error message.

## Examples

``` r
# \donttest{
# Load data
data(green_crab_data)

# Examine data in list
# This function uses only traditional survey count data and optionally
# the count type data
names(green_crab_data)
#> [1] "pcr_n"      "pcr_k"      "count"      "count_type"

# Note that the surveyed sites (rows) should match in the data
dim(green_crab_data$count)[1]
#> [1] 20
dim(green_crab_data$count_type)[1]
#> [1] 20

# Fit a model without estimating a gear scaling coefficient for traditional
# survey gear types.
# This model assumes all traditional survey methods have the same
# catchability.
# Count data is modeled using a poisson distribution.
fit_no_q <- traditional_model(data = green_crab_data, family = "poisson",
                              q = FALSE, phi_priors = NULL,
                              multicore = FALSE, verbose = TRUE)
#> 
#> SAMPLING FOR MODEL 'traditional_count' NOW (CHAIN 1).
#> Chain 1: 
#> Chain 1: Gradient evaluation took 2.8e-05 seconds
#> Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 0.28 seconds.
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
#> Chain 1:  Elapsed Time: 0.23 seconds (Warm-up)
#> Chain 1:                0.602 seconds (Sampling)
#> Chain 1:                0.832 seconds (Total)
#> Chain 1: 
#> 
#> SAMPLING FOR MODEL 'traditional_count' NOW (CHAIN 2).
#> Chain 2: 
#> Chain 2: Gradient evaluation took 2e-05 seconds
#> Chain 2: 1000 transitions using 10 leapfrog steps per transition would take 0.2 seconds.
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
#> Chain 2:  Elapsed Time: 0.27 seconds (Warm-up)
#> Chain 2:                0.704 seconds (Sampling)
#> Chain 2:                0.974 seconds (Total)
#> Chain 2: 
#> 
#> SAMPLING FOR MODEL 'traditional_count' NOW (CHAIN 3).
#> Chain 3: 
#> Chain 3: Gradient evaluation took 2e-05 seconds
#> Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 0.2 seconds.
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
#> Chain 3:  Elapsed Time: 0.232 seconds (Warm-up)
#> Chain 3:                0.55 seconds (Sampling)
#> Chain 3:                0.782 seconds (Total)
#> Chain 3: 
#> 
#> SAMPLING FOR MODEL 'traditional_count' NOW (CHAIN 4).
#> Chain 4: 
#> Chain 4: Gradient evaluation took 4.4e-05 seconds
#> Chain 4: 1000 transitions using 10 leapfrog steps per transition would take 0.44 seconds.
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
#> Chain 4:  Elapsed Time: 0.24 seconds (Warm-up)
#> Chain 4:                0.64 seconds (Sampling)
#> Chain 4:                0.88 seconds (Total)
#> Chain 4: 


# Fit a model estimating a gear scaling coefficient for traditional survey
# gear types.
# This model does not assume all traditional survey methods have the same
# catchability.
# Count data is modeled using a negative binomial distribution.
fit_q <- traditional_model(data = green_crab_data, family = "negbin",
                           q = TRUE, phi_priors = c(0.25,0.25),
                           multicore = FALSE, initial_values = NULL,
                           n_chain = 4, n_warmup = 500, n_iter = 3000,
                           thin = 1, adapt_delta = 0.9, verbose = TRUE,
                           seed = 123)
#> Warning: There was 1 warning in `dplyr::mutate()`.
#> ℹ In argument: `L_ind = seq_len(data$count_type)[1]`.
#> Caused by warning in `seq_len()`:
#> ! first element used of 'length.out' argument
#> 
#> SAMPLING FOR MODEL 'traditional_count' NOW (CHAIN 1).
#> Chain 1: 
#> Chain 1: Gradient evaluation took 0.000277 seconds
#> Chain 1: 1000 transitions using 10 leapfrog steps per transition would take 2.77 seconds.
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
#> Chain 1:  Elapsed Time: 2.778 seconds (Warm-up)
#> Chain 1:                6.208 seconds (Sampling)
#> Chain 1:                8.986 seconds (Total)
#> Chain 1: 
#> 
#> SAMPLING FOR MODEL 'traditional_count' NOW (CHAIN 2).
#> Chain 2: 
#> Chain 2: Gradient evaluation took 0.000296 seconds
#> Chain 2: 1000 transitions using 10 leapfrog steps per transition would take 2.96 seconds.
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
#> Chain 2:  Elapsed Time: 2.541 seconds (Warm-up)
#> Chain 2:                6.429 seconds (Sampling)
#> Chain 2:                8.97 seconds (Total)
#> Chain 2: 
#> 
#> SAMPLING FOR MODEL 'traditional_count' NOW (CHAIN 3).
#> Chain 3: 
#> Chain 3: Gradient evaluation took 0.000234 seconds
#> Chain 3: 1000 transitions using 10 leapfrog steps per transition would take 2.34 seconds.
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
#> Chain 3:  Elapsed Time: 2.833 seconds (Warm-up)
#> Chain 3:                7.745 seconds (Sampling)
#> Chain 3:                10.578 seconds (Total)
#> Chain 3: 
#> 
#> SAMPLING FOR MODEL 'traditional_count' NOW (CHAIN 4).
#> Chain 4: 
#> Chain 4: Gradient evaluation took 0.000294 seconds
#> Chain 4: 1000 transitions using 10 leapfrog steps per transition would take 2.94 seconds.
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
#> Chain 4:  Elapsed Time: 2.838 seconds (Warm-up)
#> Chain 4:                5.097 seconds (Sampling)
#> Chain 4:                7.935 seconds (Total)
#> Chain 4: 
# }
```
