﻿# Codacy Analysis

![](./Screenshots/1.png)

![](./Screenshots/2.png)

There are 33 issues in total, 19 errors and 14 warnings respectively.

## Security Issues

![](./Screenshots/3.PNG)

- Null-terminated(0-terminated)

In computer programming, a null-terminated string is a character string stored as an array containing the characters and terminated with a null character (a character with a value of zero)

Consequences if a string is not null-terminated: Many library functions accept a string or wide string argument with the constraint that the string they receive is properly null-terminated. Passing a character sequence or wide character sequence that is not null-terminated to such a function can result in accessing memory that is outside the bounds of the object.

Problem happens at
```
strlen(argv[argc-1])
```

- Possible Solution Example

This code example is noncompliant because the wide character sequence cur_msg will not be null-terminated when passed to wcslen(). This will occur if lessen_memory_usage() is invoked while cur_msg_size still has its initial value of 1024.
```
#include <stdlib.h>
#include <wchar.h>
  
wchar_t *cur_msg = NULL;
size_t cur_msg_size = 1024;
size_t cur_msg_len = 0;
 
void lessen_memory_usage(void) {
  wchar_t *temp;
  size_t temp_size;
 
  /* ... */
 
  if (cur_msg != NULL) {
    temp_size = cur_msg_size / 2 + 1;
    temp = realloc(cur_msg, temp_size * sizeof(wchar_t));
    /* temp &and cur_msg may no longer be null-terminated */
    if (temp == NULL) {
      /* Handle error */
    }
 
    cur_msg = temp;
    cur_msg_size = temp_size;
    cur_msg_len = wcslen(cur_msg);
  }
}
```
In this compliant solution, cur_msg will always be null-terminated when passed to wcslen():
```
#include <stdlib.h>
#include <wchar.h>
  
wchar_t *cur_msg = NULL;
size_t cur_msg_size = 1024;
size_t cur_msg_len = 0;
 
void lessen_memory_usage(void) {
  wchar_t *temp;
  size_t temp_size;
 
  /* ... */
 
  if (cur_msg != NULL) {
    temp_size = cur_msg_size / 2 + 1;
    temp = realloc(cur_msg, temp_size * sizeof(wchar_t));
    /* temp and cur_msg may no longer be null-terminated */
    if (temp == NULL) {
      /* Handle error */
    }
 
    cur_msg = temp;
    /* Properly null-terminate cur_msg */
    cur_msg[temp_size - 1] = L'\0';
    cur_msg_size = temp_size;
    cur_msg_len = wcslen(cur_msg);
  }
}
```

## Include Nested Too Deeply

![](./Screenshots/4.png)

- Reasons

When two (or more) files include each other, it can lead to an infinite cycle of includes. You can fix this by making sure that files never include files that include themselves.

- Sample Solutions

use include guards to make sure that each file is only defined once.

Non-Compliant code:
```
1	/* helloworld.cpp */
2	#include "foo.h"
3	#include "bar.h"


1	/* foo.h */
2	#include "bar.h"

1	/* bar.h */
2	#include "foo.h"
```
Compliant code:
```
/* helloworld.cpp */
2	#include "foo.h"
3	#include "bar.h"

1	/* foo.h */
2	#ifndef FOO_H
3	#define FOO_H
4	#include "bar.h"

1	/* bar.h */
2	#ifndef BAR_H
3	#define BAR_H
4	#include "foo.h"
```

- Practices on Project

In async.h file:

```
#ifndef __HIREDIS_ASYNC_H
#define __HIREDIS_ASYNC_H
#include "hiredis.h"

#ifdef __cplusplus
extern "C" {
#endif
```

In hiredis.h file

```
#ifndef __HIREDIS_H
#define __HIREDIS_H

#if defined(_MSC_VER)
#define HIREDIS_WIN 1
#endif

#include <stdio.h> /* for size_t */
#include <stdarg.h> /* for va_list */

#ifndef HIREDIS_WIN
#include <sys/time.h> /* for struct timeval */
#else
#include <winsock2.h>
#include <ws2tcpip.h>
#endif
```

## Memory Leak

![](./Screenshots/5.png)

Memory leakage occurs in C++ when programmers allocates memory by using new keyword and forgets to deallocate the memory by using delete() function or delete[] operator. 

- Problematic Code(./tools/serialization/hiredis/sds.c)

```
sds sdsnewlen(const void *init, size_t initlen) {
    struct sdshdr *sh;

    sh = malloc(sizeof(struct sdshdr)+initlen+1);
#ifdef SDS_ABORT_ON_OOM
    if (sh == NULL) sdsOomAbort();
#else
    if (sh == NULL) return NULL;
#endif
    sh->len = initlen;
    sh->free = 0;
    if (initlen) {
        if (init) memcpy(sh->buf, init, initlen);
        else memset(sh->buf,0,initlen);
    }
    sh->buf[initlen] = '\0';
    return (char*)sh->buf;
}
```
Variable sh is a sdshdr-type pointer and allocated spaces of size (size of struct sdshdr + initlen + 1). 1 is for null-terminated string. Outside of the scope of the function, pointer sh is not deallocated which causes memory leak.

## va_list, va_copy, va_end

![](./Screenshots/6.png)

### Basic Concepts
- va_list

This type is used as a parameter for the macros defined in cstdarg.h to retrieve the additional arguments of a function.

va_start initializes an object of this type in such a way that subsequent calls to va_arg sequentially retrieve the additional arguments passed to the function.
```
void va_start (va_list ap, paramN);
```

Before a function that has initialized a va_list object with va_start returns, the va_end macro shall be invoked.
```
void va_end (va_list ap);
```

- va_copy
```
void va_copy (va_list dest, va_list src);
```

Initializes dest as a copy of src (in its current state).

The next argument to be extracted from dest is the same as the one that would be extracted from src.

A function that invokes va_copy, shall also invoke va_end on dest before it returns.

### Practical Code(tools/serialization/hiredis/sds.c)

In sdscatvprintf(sds s, const char *fmt, va_list ap) function va_list variable cpy is declared:
```
va_list cpy;
```

cpy is initialized by va_copy:
```
va_copy(cpy,ap);
```

According to va_copy invoke rules: A function that invokes va_copy, shall also invoke va_end on dest before it returns.

At the end of the function:
![](./Screenshots/6.png)


# Manual Review





