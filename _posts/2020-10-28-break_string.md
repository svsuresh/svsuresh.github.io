---
title: Generate overlapping strings
tags: [cran-R "overlapping strings" stringr]
layout: post
---
Some times, for some obscure reason, you are asked to generate strings
of letters from a larger string with restriction on string size and
overlapping Window. For eg. You are asked to generate 3 letter
word/string from “**Apple**” with one alphabet overlap. From Apple, you
would be generating, **App**, **ppl**, **ple** (one character overlap). You also
would like to put in a data fram for further manipulation. Here is an
example with radom letters of DNA code (ATCG).

``` r
print ("string is ATCGATCGGGTTTAC")
```

[1] "string is ATCGATCGGGTTTAC"

Now the requirement is to print 6 letter strings, with one letter
overlap. There are many ways to do it. Let us do it in a simple way.

``` r
library(stringr)
str_match_all("ATCGATCGGGTTTAC", "(?=(.{6}))")[[1]][,2]
```

[1] "ATCGAT" "TCGATC" "CGATCG" "GATCGG" "ATCGGG" "TCGGGT" "CGGGTT" "GGGTTT"

[9] "GGTTTA" "GTTTAC"

Now that we did this, this is temporary stop gap. Now let us make a
general function, where user would provide a character vector (with long
string), length of required of word and overlap window.

``` r
seq="ATCGATCGGGTTTAC"
extract_kmers=function(Seq,Len,Wind){
    df=str_sub(seq,seq(1,nchar(seq)-(Len-1),Len-Wind),seq(Len,nchar(seq),Len-Wind))
    return(as.data.frame(table(df, dnn = "String"), responseName="Counts"))
}
```

Now let us extract 6 letter length (hexamers) words with one character
overlap

``` r
extract_kmers(seq,2,1)
```

String Counts
1.     AC	1
2.     AT	2
3.     CG	2
4.     GA	1
5.     GG	2
6.     GT	1
7.     TA	1
8.     TC	2
9.     TT	2

Now let us extract 6 letter length (hexamers) words with two character
overlap

``` r
extract_kmers(seq,6,2)
```

String Counts
1. ATCGAT	1
2. ATCGGG	1
3. GGTTTA	1

Now let us extract 6 letter length (hexamers) words with 5 character
overlap

``` r
extract_kmers(seq,6,5)
```

 String Counts
1.  ATCGAT	1
2.  ATCGGG	1
3.  CGATCG	1
4.  CGGGTT	1
5.  GATCGG	1
6.  GGGTTT	1
7.  GGTTTA	1
8.  GTTTAC	1
9.  TCGATC	1
10. TCGGGT	1

Now let us extract 8 letter length (hexamers) words with one character
overlap

``` r
extract_kmers(seq,8,2)
```

  String Counts
1. ATCGATCG	1
2. CGGGTTTA	1
