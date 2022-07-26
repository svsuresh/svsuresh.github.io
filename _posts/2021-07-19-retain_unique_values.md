---
layout: post
title: "Retain only unique values in the data"
tags: dplyr tidyr unique 
---
Many tools are available for removing duplicates and retaining one representative value (minimum, maxium, sum, mean etc). But it is not straight forward to delete all the duplicate values from a data frame. Let us work on that today. Following is the example data.

```code
> df
    Skin   Ear  Nose
1 A4GALT A4GNT  AACS
2   AAAS  AAAS AABAD
3   AACS  AACS AAGAB
4  AADAC AADAC  AAK1
5  AADAT AAGAB AAMDC
```

Now you see that there are genes, present multiple times in multiple tissues. Now user wants only genes unique to each tissue. In general, many methods exist to retain one distinct value. Following is the code to retain only unique genes i.e unique to each tissue. Following is the code:

```R
library(tidyr)
library(dplyr)
df=read.csv("input.tsv", sep="\t")
df %>% 
    pivot_longer(everything(),"k",values_to = "v") %>% 
    group_by(v) %>% 
    filter(n() == 1) %>% 
    ungroup() %>% 
    add_rownames() %>% 
    pivot_wider(names_from = k, values_from = v) %>% 
    select(-rowname)
```

output would be:

```
# A tibble: 6 x 3
  Skin     Ear     Nose   
  <chr>    <chr>   <chr>  
1 "A4GALT" ""      ""     
2 ""       "A4GNT" ""     
3 ""       ""      "AABAD"
4 ""       ""      "AAK1" 
5 "AADAT"  ""      ""     
6 ""       ""      "AAMDC"
```