---
title : glibc linking to an older symbol
category : glibc
tags: [C, glibc]
---

# Objective

For some reasons, the tools we wrote in c and complied in glibc need to link to  an older glibc symbol. This article is a record of how to achieve it. 

# **issue**

The machine I uses to complied the source code is a ubuntu15.04 with glibc 2.21. 
If we run the exectuable on an centos6.8 with glibc 2.12, it will
encounter a error which tell the user the exectuable is linking to a newer symbol and the exectuable can not be exectuted on the macnine which has an older glibc. 

# solution

1. **find which symbol causing this error**
use 
	objdump -T "exectuable"
we can see all the symbols and their linking glibc version. 
In my situation is caused by memcpy: 
```
	0000000000000000      DF *UND*	0000000000000000  GLIBC_2.14  memcpy
```
2. **tell the linker user an older version**

include this definiton in the source code:

```c
#define GLIBC_COMPAT_SYMBOL(FFF) __asm__(".symver " #FFF "," #FFF "@GLIBC_2.0");
```
And let the module which use the memcpy to saw this below:

```c
GLIBC_COMPAT_SYMBOL(memcpy)
```

# reference

[ref](https://web.archive.org/web/20160107032111/http://www.trevorpounds.com/blog/?p=103)