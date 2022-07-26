---
layout: post
title: Determine file type without extension or with extension 
tags: python filetype
---

Several times, we would like to know the file types and then run the functions on the file. In *nix, there is a command named "file" that would help user to find the file type based on magic number of file type. Here is the python code to find the type of a file and example file check is gzip. Please run the code and let me know if you have any issues.

```python
import magic
import os
import sys

input_file=sys.argv[1]
detected = magic.from_file(input_file, mime=True)
extn= detected.split(sep="/")[1]
print("file type is " + extn)

if extn=="gzip":
    print ("file is a gzip")
else:
    print("not gzip")
```