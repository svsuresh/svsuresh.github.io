---
layout: post
title: "Duplicate a data frame"
tags: ["dataframe", "duplication","CRAN-R"]
---

Some times, you may need to duplicate a dataframe with same column names. This can be either simple duplication where in duplicated data
frame is next to current data frame. Variation of this would be to have
duplicated columns next to each other with same column name.

Let us create a data frame

``` r
df <- data.frame(
    a = c(1,2,3,4,5),
    b = c('a','b','c','d','e'),
    c = c(1,'b',3,'d',5),
    d = c('a',2,'c',4,'e'))
df
```

    ##   a b c d
    ## 1 1 a 1 a
    ## 2 2 b b 2
    ## 3 3 c 3 c
    ## 4 4 d d 4
    ## 5 5 e 5 e

First let us take the first case where duplicated data frames are next
to each other

``` r
data.frame(do.call(cbind,rep(df,2)), check.names = F)
```

    ##   a b c d a b c d
    ## 1 1 a 1 a 1 a 1 a
    ## 2 2 b b 2 2 b b 2
    ## 3 3 c 3 c 3 c 3 c
    ## 4 4 d d 4 4 d d 4
    ## 5 5 e 5 e 5 e 5 e

Let us take the second case wheren in duplicated columns are next to
each other.

``` r
data.frame(do.call(cbind,rep(df, each=2)), check.names = F)
```

    ##   a a b b c c d d
    ## 1 1 1 a a 1 1 a a
    ## 2 2 2 b b b b 2 2
    ## 3 3 3 c c 3 3 c c
    ## 4 4 4 d d d d 4 4
    ## 5 5 5 e e 5 5 e e
