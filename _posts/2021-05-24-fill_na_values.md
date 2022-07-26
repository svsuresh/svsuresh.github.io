---
layout:  post
title:  Replace empty values in text files with awk
tags:  awk datamash
---

One of the strengths of AWK is column operation which makes column based manipulation of text files are easy. One such example as follows below. Example text is as follows:

```bash
$ cat test.txt 
	1:924024
	1:924310
SAMD11 	1:930353
SAMD11 	1:930939
NOC2L  	1:944858
NOC2L  	1:946247
KLHL17 	1:960891
KLHL17 	1:961945

```

First two lines have no values in first column and each column is separated by tab.  User request is to fill it up with incremental numbers, followed by a text `:na`. It is easy with awk as follows:

```awk
$ awk -F "\t" -v OFS="\t" '{if ($1=="") $1=NR":na"}1' test.txt 

1:na	1:924024
2:na	1:924310
SAMD11 	1:930353
SAMD11 	1:930939
NOC2L  	1:944858
NOC2L  	1:946247
KLHL17 	1:960891
KLHL17 	1:961945

```

using ternary operator in awk:
```awk

$ awk -F "\t" -v OFS="\t" '{ ($1=="")? ($1=NR":na"):$1}1' test.txt 

1:na	1:924024
2:na	1:924310
SAMD11 	1:930353
SAMD11 	1:930939
NOC2L  	1:944858
NOC2L  	1:946247
KLHL17 	1:960891
KLHL17 	1:961945

```

But what if user wants to number each and every line, but empty one being filled with `na`

```awk
$ awk -F "\t" -v OFS="\t" '{ ($1==" ")? ($1=NR":na"):($1=NR":"$1)}1' test.txt

1:na	1:924024
2:na	1:924310
3:SAMD11	1:930353
4:SAMD11	1:930939
5:NOC2L	1:944858
6:NOC2L	1:946247
7:KLHL17	1:960891
8:KLHL17	1:961945


```

Now this is easy. Right? But user wants a different way. All duplicated entries must have same numbering, while empty ones should have individual entries. It's tricky. Without writing large code, I could not collapse rows, based on a column in awk. So I used datamash.  I wish datamash had expand option for any given column just the way they have group based collapse.

here is the code any way. A little bit messy, but works.

```awk

$ awk -F "\t" -v OFS="\t" '$1==" " {$1="na_"NR}1' test.txt \
	| datamash -g1  collapse 2 \
	| awk -F "\t" -v OFS='\t' '{split($2,a,",");for(i in a) print NR,$1,a[i]}' \
	| awk -v OFS="\t" '{gsub(/_.*/,"",$2)}{print $1":"$2,$3}' 

1:na	1:924024
2:na	1:924310
3:SAMD11	1:930353
3:SAMD11	1:930939
4:NOC2L	1:944858
4:NOC2L	1:946247
5:KLHL17	1:960891
5:KLHL17	1:961945

```