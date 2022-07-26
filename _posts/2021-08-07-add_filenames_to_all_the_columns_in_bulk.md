---
layout: post
title: Append file name at the end of each column for all CSV files
tags: awk R csv
---

One the requests I got was to append file name at the end of each file (csv) in a directory. This can be easily done with Awk, but user wanted in R. Let us do it in awk

```bash
$ for i in *.csv; do awk 'NR==1{gsub(/,|$/,"_"FILENAME","); sub (/,$/,"")}1' $i > ${i%\.csv}"_new.csv"; done

```
Though I prefer `find` or `fd`, quick way is to use `ls`. Example input and output as follows:

```bash
input:

$ cat test.csv 
col1,col2
line1,line1
line2,line2

output:

$ cat test_new.csv 
col1_test.csv,col2_test.csv
line1,line1
line2,line2

directory tree:

$ tree  . -P "*.csv"
.
├── test1.csv
├── test1_new.csv
├── test2.csv
├── test2_new.csv
├── test.csv
└── test_new.csv


```

Now let us do the same thing in R. Difference is that instead of writing files, it stores as objects.

```R
files = list.files(pattern = ".csv", path = "~/Desktop/")

Method 1:
append_name=function(x){
    iname=sub("\\.csv","",x)
    f=read.csv(x, header = T)
    colnames(f)=paste(colnames(f),iname,sep = "_")
    assign(paste0(iname,"_new"),f)
}

sapply(files, append_name,simplify = FALSE,USE.NAMES = TRUE)

**Method:2**

for (i in files){
    iname=sub("\\.csv","",i)
    f=read.csv(i, header = T)
    colnames(f)=paste(colnames(f),iname,sep = "_")
    assign(paste0(iname,"_new"),f)
}
ls()

```
Method1 produces a list of data frames, method 2 saves the data farmes as individual objects