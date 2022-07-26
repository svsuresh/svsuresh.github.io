---
title: "Generate substrings in python"
tags: [substrings "Python 3"]
layout: post
---
Let us say you are asked to generate substrings of a large string with a defined length and overlap, following is python(3) script.

#### Import the libraries

```python
import numpy as np
import sys
```
#### Collect user input, as three arguments. First input is a string and will be converted as string. Second argument is length of the substring and third argument is overlap window size. All must be supplied by a space in between.
```python
string = str(sys.argv[1])
Len = int(sys.argv[2])
Wind = int(sys.argv[3])
```
#### If user enters a window size larger than or equal to string size, the code stops.
```python
if Wind >= Len:
    print("String is smaller than or equal to over lapping window")
    sys.exit()
```
#### Now, let us generate stard and end positions of the substrings on user input string
```python
Start = list(range(0, len(string) + 1 - Len, Len - Wind))
End = list(range(Len, len(string) + 1, Len - Wind))
```
#### Store output in a list named "test"
```python
test = [string[i: j] for i, j in zip(Start, End)]
```
#### Now, in the list count the unique strings and print the substring and number of their occurences.
```python
values, counts = np.unique(test, return_counts=1)
for i, j in zip(values, counts):
    print(i, "\t", j)
```
#### Save the script as **print_overlaps.py".
Command line argument would as follows below with an example:
```python
python3 print_overlaps.py mystring 3 2
```
**Note** Output is sorted alphabetically.

#### Output looks like below:
```python
> python3 print_overlaps.py mystring 4 2

myst 	 1
ring 	 1
stri 	 1
```
#### another example:
```python
> python3 print_overlaps.py mystring 5 3
mystr 	 1
strin 	 1
```
