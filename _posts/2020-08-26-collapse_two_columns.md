---
layout: post
title: Clean two column data using tidyr
categories: tidyr 'alternate columns' CRAN-R
---

### Let us look at the data first in system.

``` bash
cat ../_data/collapse_two_columns_example.txt
```

    ## Name Group-1 Name    Group-2 Name    Group-3
    ## A    2   A   1   A   4
    ## B    4   B   2   B   7
    ## C    7   C   4   I   6
    ## D    6   D   7   D   4
    ## E    4   F   6   M   9
    ##      H   4

Now we have to clean up alternate columns. Collapse “Name” columns to a
single column and fetch each group values under group specific columns.
Each group column must be appropriately named after group. We can do
this with tidyverse (with tidyr) libraries as follows. Before loading data, let us load the R libraries. 

``` r
library(tidyverse)
```

### Now let us load the data.

``` r
df=read.csv("../_data/collapse_two_columns_example.txt", sep="\t", strip.white = T)
```

### Let us look at the loaded data

``` r
df
```

    ##   Name Group.1 Name.1 Group.2 Name.2 Group.3
    ## 1    A       2      A       1      A       4
    ## 2    B       4      B       2      B       7
    ## 3    C       7      C       4      I       6
    ## 4    D       6      D       7      D       4
    ## 5    E       4      F       6      M       9
    ## 6           NA      H       4             NA

### Now let us clean the data.

``` r
df %>%
    rename_at(vars(starts_with("Name")), ~paste("Name", seq(1:(ncol(df)/2)),sep=".")) %>%
    pivot_longer(everything(),names_to = c(".value", "set"),names_pattern = "(\\D+)(\\d)") %>%
    drop_na()%>%
    pivot_wider(names_from="set", values_from="Group.", names_prefix = "Group.", values_fill = 0) %>%
    arrange(Name.)
```

    ## # A tibble: 9 x 4
    ##   Name. Group.1 Group.2 Group.3
    ##   <chr>   <int>   <int>   <int>
    ## 1 A           2       1       4
    ## 2 B           4       2       7
    ## 3 C           7       4       0
    ## 4 D           6       7       4
    ## 5 E           4       0       0
    ## 6 F           0       6       0
    ## 7 H           0       4       0
    ## 8 I           0       0       6
    ## 9 M           0       0       9

Data cleaning would do following steps:
1. Renames the columns starting with "Name" to Name.1, Name.2, Name.3 to match with group names
2. Includes following steps
	1. Breaks the column names into two patterns (non-digital (\\D+) and digital (\\d))
	2. First part of the pattern (\\D+) will hold original column values and second part of the pattern (\\d) will be stored in column named "set"
	3. Convert the data to longer format with input arguments
3. Remove empty rows
4. Convert long data to wider format and replace empty values (NA) with 0.
5. Sort the data frame by Name. column