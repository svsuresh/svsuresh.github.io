---
layout: post
title: Remove double extension in python
tags: python
---

I don't want to post one line notes (May be I have done in the past without knowing). But here is one, I want to post. I looked at multiple posts and several mind numbing ways to remove double extension in python. In biology, it's very common (fq.gz, fastq.gz, fa.gz, gtf.gz etc). Here is the simple code to remove double extension

```python
import pathlib as pl

test=pl.Path("x.tar.gz")
print(test.with_suffix('').with_suffix(''))
```
This would print `x`.