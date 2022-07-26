---
layout: post
title: Format fasta headers
tags: [fasta, awk, bash]
---

Following is an example and code to append ">" on every second line (from start) including very first line.

**input**
~~~ bash
$ cat test.fa

Gene1
AAAGTTGTTGCAG
Gene2
ACATACGATTCCGGAGAGG
~~~
**output**
~~~bash
$ awk '{ if ((NR % 2) == 1) printf(">")}1' test.fa

>Gene1
AAAGTTGTTGCAG
>Gene2
ACATACGATTCCGGAGAGG

$ sed  '1~2 s/^/>/' test.fa
>Gene1
AAAGTTGTTGCAG
>Gene2
ACATACGATTCCGGAGAGG
~~~

Now let us do some thing else. User wants 
- Remove all headers in a file
- Use file name as fasta header
- Print only the sequences
  
Confused? Look at the input and expected output:
 
 **input**
 ~~~ 
$ cat test.fa

>gene1
ATGCTA
>gene2
TGATGCT
>gene3
TAGTATG 
 ~~~
 **Expected output**
 ~~~
 >test.fa
ATGCTA
TGATGCT
TAGTATG
 ~~~
 **Code**
 ~~~~
 $  awk 'BEGIN {print ">"ARGV[1]};!/^>/{print}' test.fa

>test.fa
ATGCTA
TGATGCT
TAGTATG

$ cat <(echo ">"$basename test.fa) <(grep -v ">" test.fa) 
> test.fa
ATGCTA
TGATGCT
TAGTATG
 ~~~~
 (**Note**: There is an extra space in later code)
