---
layout: post
title: Split a data frame based on column names
tags: R "data frame"
---

Recently there is a request from user to split the data frame based on
the column names. Column names contain identical strings, but separated
by "\_". User request is to group identical columns, print their values,
followed by transposition of data frame. Let me explain with an example
below

``` r
d <- read.table(text ="
 2-1 3-1 3-2 4-1 4-2 4-3
GeneA 0.514 0.535 0.436 0.530 0.388 0.418
GeneB 0.111 0.222 0.333 0.444 0.555 0.666", 
                check.names = FALSE)

print(d)
```

    ##         2-1   3-1   3-2   4-1   4-2   4-3
    ## GeneA 0.514 0.535 0.436 0.530 0.388 0.418
    ## GeneB 0.111 0.222 0.333 0.444 0.555 0.666

Now user wants columns 2,3,4 be the row names and fill up the missing
series starting from 1. Now values for each column group (group 3 for
3-1,3-2 columns), must be presented in columns. Confused? Look at the
below solution. But first let us load the libraries.

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
library(tidyr)
library(tibble)
library(stringr)
library (splitstackshape)
library(purrr)
```

Now let us do the work.

``` r
d %>%
    rownames_to_column("genes") %>% 
    pivot_longer(-genes,names_to = "k", values_to = "v") %>%
    mutate(k=str_split_fixed(k,"-",2)[,1],
           k=as.integer(k)) %>% 
    group_by(genes) %>%
    group_map(~{
        tibble(complete(k = 1:max(k), fill = list(vol = 0),data=.x) %>%
            group_by(k) %>% 
            mutate(v=paste(v, collapse = ",")) %>% 
            distinct() %>% 
            ungroup() %>% 
            cSplit (.,"v")
            )
    })
```

    ## [[1]]
    ## # A tibble: 4 x 4
    ##       k    v_1    v_2    v_3
    ##   <int>  <dbl>  <dbl>  <dbl>
    ## 1     1 NA     NA     NA    
    ## 2     2  0.514 NA     NA    
    ## 3     3  0.535  0.436 NA    
    ## 4     4  0.53   0.388  0.418
    ## 
    ## [[2]]
    ## # A tibble: 4 x 4
    ##       k    v_1    v_2    v_3
    ##   <int>  <dbl>  <dbl>  <dbl>
    ## 1     1 NA     NA     NA    
    ## 2     2  0.111 NA     NA    
    ## 3     3  0.222  0.333 NA    
    ## 4     4  0.444  0.555  0.666

This would print two lists without list name.One of the side effects of
using `group_map` is that you would loose list names which is not good.
You need to write another function for this. There is another way to do
the same.

``` r
d %>%
    rownames_to_column("genes") %>%
    pivot_longer(-genes, names_to = "k", values_to = "v") %>%
    mutate(k = str_split_fixed(k, "-", 2)[, 1],
           k = as.integer(k)) %>%
    group_by(genes) %>%
    complete(k = 1:max(k), fill = list(vol = 0)) %>%
    group_by(genes, k) %>%
    mutate(v = paste(v, collapse = ",")) %>%
    distinct() %>%
    ungroup() %>%
    cSplit(., "v") %>%
    split(.$genes) %>%
    map(., ~ (data = .x %>% select(-genes)))
```

    ## $GeneA
    ##    k   v_1   v_2   v_3
    ## 1: 1    NA    NA    NA
    ## 2: 2 0.514    NA    NA
    ## 3: 3 0.535 0.436    NA
    ## 4: 4 0.530 0.388 0.418
    ## 
    ## $GeneB
    ##    k   v_1   v_2   v_3
    ## 1: 1    NA    NA    NA
    ## 2: 2 0.111    NA    NA
    ## 3: 3 0.222 0.333    NA
    ## 4: 4 0.444 0.555 0.666

Now this prints lists with names which is good :).
