---
title: "Percentage pie chart in R"
layout: post
categories: iris 'pie chart' CRAN-R ggplot2
---

Here is the code on drawing pie chart with percentages with ggplot2 and iris data set. Only Sepal
length variable is used in this tutorial

Load libraries

``` r
library(ggplot2)
library(dplyr)
```

Calculate the sum of the Sepal Lengths

``` r
Sum=sum(iris$Sepal.Length)
```

Now draw pie chart

``` r
iris %>%
    group_by(Species) %>%
    summarise(across("Sepal.Length", sum)) %>%
    mutate(perc=Sepal.Length/sum(Sepal.Length)*100) %>%
    ggplot(aes(x="", y=Sepal.Length, fill=Species))+
    geom_bar(stat="identity") +
    theme(legend.position = "None",
          axis.title = element_blank(),
          axis.ticks = element_blank()) +
    geom_text(aes(label = paste0(Species,"\n",round(perc,2),"%")), position = position_stack(vjust = 0.5), size=6) +
    coord_polar("y", start=0)
```


