---
title: Average biological replicates
tags: [cran-r, aggregate, average, replicates]
layout: post
---
In general, biologists have to repeat experiments multiple times to
reproduce the results and for statistical analysis. Many a times,
biologist may have to average (mean, median or mode) or do any other
transformation for replicates (duplicates, triplicates etc).

Let us create example data with 3 samples (3 triplicates each, named
from sample 1 through 9) and for 6 genes.

``` r
df = structure(
    list(
        Sample_1 = c(0, 4.9, 2.45, 0, 46.55, 14.7),
        Sample_2 = c(1,2, 0, 1, 55.9, 7.99),
        Sample_3 = c(0, 1.57, 0, 1.57, 53.42, 4.71),
        Sample_4 = c(0, 1.26, 0, 0, 52.73, 10.04),
        Sample_5 = c(0,0, 0, 0, 66.4, 23.24),
        Sample_6 = c(2.19, 3.28, 0, 0, 65.56,9.83),
        Sample_7 = c(0, 0, 0, 0, 74.11, 18.96),
        Sample_8 = c(0,0, 0, 0, 66.88, 13.38),
        Sample_9 = c(0, 0, 0, 0, 50.78, 13.08)
    ),
    row.names = c("Gene1", "Gene2", "Gene3", "Gene4", "Gene5","Gene6"),
    class = "data.frame"
)
```

Now let’s look at the data

``` r
str(df)
```

    ## 'data.frame':    6 obs. of  9 variables:
    ##  $ Sample_1: num  0 4.9 2.45 0 46.55 ...
    ##  $ Sample_2: num  1 2 0 1 55.9 7.99
    ##  $ Sample_3: num  0 1.57 0 1.57 53.42 ...
    ##  $ Sample_4: num  0 1.26 0 0 52.73 ...
    ##  $ Sample_5: num  0 0 0 0 66.4 ...
    ##  $ Sample_6: num  2.19 3.28 0 0 65.56 ...
    ##  $ Sample_7: num  0 0 0 0 74.1 ...
    ##  $ Sample_8: num  0 0 0 0 66.9 ...
    ##  $ Sample_9: num  0 0 0 0 50.8 ...

Now, let us average over the replicates

``` r
data.frame(apply(array(as.matrix(df), c(
    nrow(df), 3, ncol(df) / 3
)), 3, rowMeans), row.names = row.names(df))
```

    ##               X1        X2       X3
    ## Gene1  0.3333333  0.730000  0.00000
    ## Gene2  2.8233333  1.513333  0.00000
    ## Gene3  0.8166667  0.000000  0.00000
    ## Gene4  0.8566667  0.000000  0.00000
    ## Gene5 51.9566667 61.563333 63.92333
    ## Gene6  9.1333333 14.370000 15.14000

Explanation for the code is: we are converting data frame to array (3
dimensional) with following dimentions: 3 groups (no of columns/number
of replicates- here it is triplicates), with 6 rows and 3 columns.
Groups and columns vary as per the replicates. Let us say we have 10
samples with duplicate samples, then the groups would be 5, and columns
would be 2. Post convertion of array into (6 rowsx3 columnsx3 groups),
we are taking row means for every row in each group. Then we are
printing the output.

We can do the same with **aggregate** function

``` r
t(aggregate(t(df), list(rep(1:(ncol(df)/3),each=3)), mean)[,-1])
```

    ##             [,1]      [,2]     [,3]
    ## Gene1  0.3333333  0.730000  0.00000
    ## Gene2  2.8233333  1.513333  0.00000
    ## Gene3  0.8166667  0.000000  0.00000
    ## Gene4  0.8566667  0.000000  0.00000
    ## Gene5 51.9566667 61.563333 63.92333
    ## Gene6  9.1333333 14.370000 15.14000

Now what if your data has triplicate name, as below and you want to
average

``` r
df = structure(
    list(
        Sample_1 = c(0, 4.9, 2.45, 0, 46.55, 14.7),
        Sample_1 = c(1,2, 0, 1, 55.9, 7.99),
        Sample_1 = c(0, 1.57, 0, 1.57, 53.42, 4.71),
        Sample_2 = c(0, 1.26, 0, 0, 52.73, 10.04),
        Sample_2 = c(0,0, 0, 0, 66.4, 23.24),
        Sample_2 = c(2.19, 3.28, 0, 0, 65.56,9.83),
        Sample_3 = c(0, 0, 0, 0, 74.11, 18.96),
        Sample_3 = c(0,0, 0, 0, 66.88, 13.38),
        Sample_3 = c(0, 0, 0, 0, 50.78, 13.08)
    ),
    row.names = c("Gene1", "Gene2", "Gene3", "Gene4", "Gene5","Gene6"),
    class = "data.frame"
)
head(df,3)
```

    ##       Sample_1 Sample_1 Sample_1 Sample_2 Sample_2 Sample_2 Sample_3 Sample_3
    ## Gene1     0.00        1     0.00     0.00        0     2.19        0        0
    ## Gene2     4.90        2     1.57     1.26        0     3.28        0        0
    ## Gene3     2.45        0     0.00     0.00        0     0.00        0        0
    ##       Sample_3
    ## Gene1        0
    ## Gene2        0
    ## Gene3        0

Averaging such files much easier. Do as follows:

``` r
limma::avearrays(df)
```

    ##         Sample_1  Sample_2 Sample_3
    ## Gene1  0.3333333  0.730000  0.00000
    ## Gene2  2.8233333  1.513333  0.00000
    ## Gene3  0.8166667  0.000000  0.00000
    ## Gene4  0.8566667  0.000000  0.00000
    ## Gene5 51.9566667 61.563333 63.92333
    ## Gene6  9.1333333 14.370000 15.14000
