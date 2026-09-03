# green_crab_data

green_crab_data

## Usage

``` r
green_crab_data
```

## Format

A list with four matrices representing eDNA sampling data (pcr_n and
pcr_k) and trap sampling data (count and count_type).

- pcr_n:

  Total number of eDNA qPCR replicates at each site (row) and eDNA
  sample replicate (column). Data includes 20 total sites and 5 eDNA
  sample replicates.

- pcr_k:

  Total number of positive eDNA qPCR detections at each site (row) and
  eDNA sample replicate (column). Data includes 20 total sites and 5
  eDNA sample replicates.

- count:

  Count of green crab individuals in trap samples at each site (row) and
  trap sample replicate (column). Data includes 20 total sites and a
  maximum of 420 trap replicates. NA indicates that fewer trap samples
  were collected than the maximum at a site.

- count_type:

  Integer indicating the traditional gear type used at each site (row)
  and trap sample replicate (column). '1' refers to Fukui traps, and '2'
  refers to Minnow traps. Data includes 20 total sites and a maximum of
  420 trap replicates. NA indicates that fewer trap samples were
  collected than the maximum at a site.

## Source

[doi:10.6084/m9.figshare.15117102.v2](https://doi.org/10.6084/m9.figshare.15117102.v2)

## References

Keller, A.G., Grason, E.W., McDonald, P.S., Ramon-Laca, A., Kelly, R.P.
(2022). Tracking an invasion front with environmental DNA. Ecological
Applications. 32(4): e2561. https://doi.org/10.1002/eap.2561
