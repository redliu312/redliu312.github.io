---
title : gcc predined macro in mingw(msys2)
category : C
tags : C
---

Recently I need to merge some code of our team to the tool developed by the  
vendor, I need to figure out some predefined macro in mingw(msys2) and some other stuff... 

In mingw console, use the command as follow: 

```
gcc -E -dM msys2Test.c | grep "WIN"
```

will get: 

```
#define _WINT_T
#define __WINT_MIN__ 0
#define __WIN32 1
#define __WIN64 1
#define __WINNT 1
#define __WINNT__ 1
#define __WIN32__ 1
#define __SIZEOF_WINT_T__ 2
#define _WIN32_WINNT 0x502
#define WIN32 1
#define WIN64 1
#define __WINT_TYPE__ short unsigned int
#define WINNT 1
#define __WIN64__ 1 
```