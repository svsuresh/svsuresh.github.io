---
title: "Column operations using dplyr and tidyr"
tags: [dataframe 'column operations' dplyr tidyr 'CRAN-R']
layout: post
---

Data operations in R can be done in so many ways that it’s confusing for newbies. This note denotes simple operations using dplyr (and other
packages from tidyverse). Let us do this using standard datasets available within R.

#### Load libraries

``` r
suppressPackageStartupMessages(library(dplyr))
suppressPackageStartupMessages(library(tidyr))
```

### Column wise operations

Let us do some basic statistics.

##### Find maximum value in each column

``` r
iris %>% 
    summarise(across(where(is.numeric), max))
```

    ##   Sepal.Length Sepal.Width Petal.Length Petal.Width
    ## 1          7.9         4.4          6.9         2.5

##### Find maximum value in each group

``` r
iris %>% 
    group_by(Species) %>% 
    summarise(across(where(is.numeric), max))
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

    ## # A tibble: 3 x 5
    ##   Species    Sepal.Length Sepal.Width Petal.Length Petal.Width
    ##   <fct>             <dbl>       <dbl>        <dbl>       <dbl>
    ## 1 setosa              5.8         4.4          1.9         0.6
    ## 2 versicolor          7           3.4          5.1         1.8
    ## 3 virginica           7.9         3.8          6.9         2.5

##### Find minimum and maximum value within each column

``` r
iris %>% 
  summarise(across(where(is.numeric), list(min = min, max = max)))
```

    ##   Sepal.Length_min Sepal.Length_max Sepal.Width_min Sepal.Width_max
    ## 1              4.3              7.9               2             4.4
    ##   Petal.Length_min Petal.Length_max Petal.Width_min Petal.Width_max
    ## 1                1              6.9             0.1             2.5

Now as you see, the table has grown horizontally with two functions (max and min). Imagine having 5 functions and multiple groups. Let us format
this to a better table, with 3 functions (min, max, mean).

``` r
iris %>%
    summarise(across(where(is.numeric), list(Min=min, Max=max, Mean=mean)))  %>%
    pivot_longer(cols = where(is.numeric),
                 names_to = c(".value", "Stat"),
                 names_pattern = "(\\w+\\W\\w+)_(\\w+)")
```

    ## # A tibble: 3 x 5
    ##   Stat  Sepal.Length Sepal.Width Petal.Length Petal.Width
    ##   <chr>        <dbl>       <dbl>        <dbl>       <dbl>
    ## 1 Min           4.3         2            1           0.1 
    ## 2 Max           7.9         4.4          6.9         2.5 
    ## 3 Mean          5.84        3.06         3.76        1.20

There is easier way to do this outside dplyr.

``` r
suppressPackageStartupMessages(library(Rfast))
data.frame(apply(iris[, -5], 2, colMinsMaxs), row.names = c("Min", "Max"))
```

    ##     Sepal.Length Sepal.Width Petal.Length Petal.Width
    ## Min          4.3         2.0          1.0         0.1
    ## Max          7.9         4.4          6.9         2.5

with Rfast package, finding min and maximum values in a vector might be easier, but not extensible. For eg, let us say you want mean, median and
sd. You cannot get all of them in the same function. However, dplyr code allows to include as many functions as you need and format as per your
needs.

##### Find minimum and maximum value within each column, for each plant species:

Now let us find minimum and maximum in each group (Species)

``` r
iris %>%
  group_by(Species) %>% 
  summarise(across(where(is.numeric), list(min = min, max = max)))
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

    ## # A tibble: 3 x 9
    ##   Species Sepal.Length_min Sepal.Length_max Sepal.Width_min Sepal.Width_max
    ##   <fct>              <dbl>            <dbl>           <dbl>           <dbl>
    ## 1 setosa               4.3              5.8             2.3             4.4
    ## 2 versic…              4.9              7               2               3.4
    ## 3 virgin…              4.9              7.9             2.2             3.8
    ## # … with 4 more variables: Petal.Length_min <dbl>, Petal.Length_max <dbl>,
    ## #   Petal.Width_min <dbl>, Petal.Width_max <dbl>

Let us format this table to a meaningful table as below:

``` r
iris %>%
    group_by(Species) %>% 
    summarise(across(where(is.numeric), list( min=min,max=max, mean=mean)))  %>%
    pivot_longer(cols = where(is.numeric),
                 names_to = c(".value", "Stat"),
                 names_pattern = "(\\w+\\W\\w+)_(\\w+)")
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

    ## # A tibble: 9 x 6
    ##   Species    Stat  Sepal.Length Sepal.Width Petal.Length Petal.Width
    ##   <fct>      <chr>        <dbl>       <dbl>        <dbl>       <dbl>
    ## 1 setosa     min           4.3         2.3          1          0.1  
    ## 2 setosa     max           5.8         4.4          1.9        0.6  
    ## 3 setosa     mean          5.01        3.43         1.46       0.246
    ## 4 versicolor min           4.9         2            3          1    
    ## 5 versicolor max           7           3.4          5.1        1.8  
    ## 6 versicolor mean          5.94        2.77         4.26       1.33 
    ## 7 virginica  min           4.9         2.2          4.5        1.4  
    ## 8 virginica  max           7.9         3.8          6.9        2.5  
    ## 9 virginica  mean          6.59        2.97         5.55       2.03

We can further beautify this table with kable package.
