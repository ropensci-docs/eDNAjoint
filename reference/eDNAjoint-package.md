# The 'eDNAjoint' package.

Models integrate environmental DNA (eDNA) detection data and traditional
survey data to jointly estimate species catch rate (see package
vignette: <https://ednajoint.netlify.app/>). Models can be used with
count data via traditional survey methods (i.e., trapping,
electrofishing, visual) and replicated eDNA detection/nondetection data
via polymerase chain reaction (i.e., PCR or qPCR) from multiple survey
locations. Estimated parameters include probability of a false positive
eDNA detection, a site-level covariates that scale the sensitivity of
eDNA surveys relative to traditional surveys, and catchability
coefficients for traditional gear types. Models are implemented with a
Bayesian framework (Markov chain Monte Carlo) using the 'Stan'
probabilistic programming language.

## References

Stan Development Team (NA). RStan: the R interface to Stan.
https://mc-stan.org

## See also

Useful links:

- <https://github.com/ropensci/eDNAjoint>

- <https://docs.ropensci.org/eDNAjoint/>

- Report bugs at <https://github.com/ropensci/eDNAjoint/issues>

## Author

**Maintainer**: Abigail G. Keller <agkeller@berkeley.edu>

Other contributors:

- Ryan P. Kelly <rpkelly@uw.edu> \[contributor\]

- Chitra M. Saraswati \[reviewer\]

- Saras M. Windecker \[reviewer\]
