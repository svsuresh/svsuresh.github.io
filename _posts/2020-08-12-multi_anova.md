---
layout: post
title:  "Run multiple Anova tests in parallel"
categories: Anova CRAN-R
---

Let us load the libraries

``` r
library(purrr)
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
library(broom)
library(knitr)
library(tibble)
library(kableExtra)
```
    ## 
    ## Attaching package: 'kableExtra'

    ## The following object is masked from 'package:dplyr':
    ## 
    ##     group_rows

Let us use inbuilt iris data for our work.

``` r
iris %>%
    pivot_longer(-Species, names_to="measurement", values_to="value") %>%
    nest(data=c(Species,value)) %>%
    mutate(model=map(data, ~TukeyHSD(aov (value ~ Species, data=.))),
           tidy=map(model,tidy)) %>%
    unnest(c("tidy")) %>%
    select(c(5,10)) %>%
    add_column(Measurement="",.before="contrast") %>%
    mutate(adj.p.value=-log10(adj.p.value)) %>%
    kable(., "html", booktabs = T, escape = F, caption = "Flower measurement statistics",
          col.names = c("","Comparison", "Ajdusted P value, (-log10p)")) %>%
    kable_styling("striped",full_width = F) %>%
    column_spec(2:3, width = "5cm") %>%
    pack_rows("Sepal Length", 1, 3) %>%
    pack_rows("Sepal Width", 4, 6) %>%
    pack_rows("Petal Length", 7, 9) %>%
    pack_rows("Petal Width", 10,12)
```

<table class="table table-striped" style="width: auto !important; margin-left: auto; margin-right: auto;">

<caption>

Flower measurement statistics

</caption>

<thead>

<tr>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

Comparison

</th>

<th style="text-align:right;">

Ajdusted P value, (-log10p)

</th>

</tr>

</thead>

<tbody>

<tr grouplength="3">

<td colspan="3" style="border-bottom: 1px solid;">

<strong>Sepal Length</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

</td>

<td style="text-align:left;width: 5cm; ">

versicolor-setosa

</td>

<td style="text-align:right;width: 5cm; ">

13.470290

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

</td>

<td style="text-align:left;width: 5cm; ">

virginica-setosa

</td>

<td style="text-align:right;width: 5cm; ">

14.523226

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

</td>

<td style="text-align:left;width: 5cm; ">

virginica-versicolor

</td>

<td style="text-align:right;width: 5cm; ">

8.081573

</td>

</tr>

<tr grouplength="3">

<td colspan="3" style="border-bottom: 1px solid;">

<strong>Sepal Width</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

</td>

<td style="text-align:left;width: 5cm; ">

versicolor-setosa

</td>

<td style="text-align:right;width: 5cm; ">

13.508986

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

</td>

<td style="text-align:left;width: 5cm; ">

virginica-setosa

</td>

<td style="text-align:right;width: 5cm; ">

8.866226

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

</td>

<td style="text-align:left;width: 5cm; ">

virginica-versicolor

</td>

<td style="text-align:right;width: 5cm; ">

2.056495

</td>

</tr>

<tr grouplength="3">

<td colspan="3" style="border-bottom: 1px solid;">

<strong>Petal Length</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

</td>

<td style="text-align:left;width: 5cm; ">

versicolor-setosa

</td>

<td style="text-align:right;width: 5cm; ">

14.523226

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

</td>

<td style="text-align:left;width: 5cm; ">

virginica-setosa

</td>

<td style="text-align:right;width: 5cm; ">

14.523226

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

</td>

<td style="text-align:left;width: 5cm; ">

virginica-versicolor

</td>

<td style="text-align:right;width: 5cm; ">

14.523226

</td>

</tr>

<tr grouplength="3">

<td colspan="3" style="border-bottom: 1px solid;">

<strong>Petal Width</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

</td>

<td style="text-align:left;width: 5cm; ">

versicolor-setosa

</td>

<td style="text-align:right;width: 5cm; ">

14.523226

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

</td>

<td style="text-align:left;width: 5cm; ">

virginica-setosa

</td>

<td style="text-align:right;width: 5cm; ">

14.523226

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

</td>

<td style="text-align:left;width: 5cm; ">

virginica-versicolor

</td>

<td style="text-align:right;width: 5cm; ">

14.523226

</td>

</tr>

</tbody>

</table>

How are we sure that our results are correct. Let us run anova and
TukeyHSD on one variable (Sepal Width)

``` r
sl_anova <- aov(Sepal.Width ~ Species, data=iris)
-log10(TukeyHSD(sl_anova)$Species[,4])
```

    ##    versicolor-setosa     virginica-setosa virginica-versicolor 
    ##            13.508986             8.866226             2.056495
