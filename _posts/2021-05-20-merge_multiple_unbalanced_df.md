---
layout: post
title: Merge multiple data frames in R
tags: CRAN-R sqldf dplyr
---

It is very common to have datasets of different lengths and we may have to merge them. Wherever data is not available, we may have to fill it up
with 0s or NAs. Let us start with simulated data. One requirement for such merges need one common column across data frames.

``` r
x <- data.frame(gene_symbol = c("a","b","c","f","g"), sample1 = 1:5, stringsAsFactors=FALSE)
y <- data.frame(gene_symbol = c("b","c","d"), sample2 = 4:6, stringsAsFactors=FALSE)
z <- data.frame(gene_symbol = c("f","g","k","l"), sample3 = 7:10, stringsAsFactors=FALSE)
```

Let us print them

``` r
x
```

    ##   gene_symbol sample1
    ## 1           a       1
    ## 2           b       2
    ## 3           c       3
    ## 4           f       4
    ## 5           g       5

``` r
y
```

    ##   gene_symbol sample2
    ## 1           b       4
    ## 2           c       5
    ## 3           d       6

``` r
z
```

    ##   gene_symbol sample3
    ## 1           f       7
    ## 2           g       8
    ## 3           k       9
    ## 4           l      10

Now as you see, there are 3 data frames with varying lengths and now we
have to merge them. Following are the solutions

``` r
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
x %>%
    left_join(y) %>% 
    left_join(z) %>%
    replace(is.na(.), 0)
```

    ## Joining, by = "gene_symbol"

    ## Joining, by = "gene_symbol"

    ##   gene_symbol sample1 sample2 sample3
    ## 1           a       1       0       0
    ## 2           b       2       4       0
    ## 3           c       3       5       0
    ## 4           f       4       0       7
    ## 5           g       5       0       8

Now let us do the same with `sqldf` library

``` r
library(sqldf)
```

    ## Loading required package: gsubfn

    ## Loading required package: proto

    ## Loading required package: RSQLite

``` r
sqldf('select x.*, y.sample2, z.sample3 from x 
        left join y on x.gene_symbol=y.gene_symbol 
        left join z on x.gene_symbol=z.gene_symbol')
```

    ##   gene_symbol sample1 sample2 sample3
    ## 1           a       1      NA      NA
    ## 2           b       2       4      NA
    ## 3           c       3       5      NA
    ## 4           f       4      NA       7
    ## 5           g       5      NA       8