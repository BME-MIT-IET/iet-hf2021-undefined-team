# Codacy Analysis

![](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/YifangMeng/Screenshots/1.PNG)

![](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/YifangMeng/Screenshots/2.PNG)

There are 33 issues in total, 19 errors and 14 warnings respectively.

## Security Issues

![](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/YifangMeng/Screenshots/3.PNG)

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

![](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/YifangMeng/Screenshots/4.PNG)

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

![](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/YifangMeng/Screenshots/5.PNG)

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

![](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/YifangMeng/Screenshots/6.PNG)

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

## HIREDIS

Hiredis is a minimalistic C client library for the Redis database.

It is minimalistic because it just adds minimal support for the protocol, but at the same time it uses an high level printf-alike API in order to make it much higher level than otherwise suggested by its minimal code base and the lack of explicit bindings for every Redis command.

Apart from supporting sending commands and receiving replies, it comes with a reply parser that is decoupled from the I/O layer. It is a stream parser designed for easy reusability, which can for instance be used in higher level language bindings for efficient reply parsing.

### Usage

The following code creates a connection to Redis using the hiredis synchronous API:
```
#include "hiredis.h"

redisContext *c = redisConnect("hostname", port);
if (c != NULL && c->err) {
    printf("Error: %s\n", c->errstr);
    // handle error
} else {
    printf("Connected to Redis\n");
}

redisReply *reply;
reply = redisCommand(c, "AUTH password");
freeReplyObject(reply);

...

redisFree(c);
```

To adapt this example to your code, make sure that you replace the following values with those of your database:

1. In line 1, the first argument to redisConnect should be your database’s hostname or IP address

2. In line 1, the second argument to redisConnect should be your database’s port

3. In line 6, replace “password” with your database’s password

### Sending Commands using redisCommand

```
reply = redisCommand(context, "SET foo bar");
```
The specifier %s interpolates a string in the command, and uses strlen to determine the length of the string:
```
reply = redisCommand(context, "SET foo %s", value);
```
When you need to pass binary safe strings in a command, the %b specifier can be used. Together with a pointer to the string, it requires a size_t length argument of the string:
```
reply = redisCommand(context, "SET foo %b", value, valuelen);
```
Internally, Hiredis splits the command in different arguments and will convert it to the protocol used to communicate with Redis. One or more spaces separates arguments, so you can use the specifiers anywhere in an argument:
```
reply = redisCommand(context, "SET key:%s %s", myid, value);
```
### Using reply

The return value of redisCommand holds a reply when the command was successfully executed. When an error occurs, the return value is NULL and the err field in the context will be set (see section on Errors). 

The standard replies that redisCommand are of the type redisReply. The type field in the redisReply should be used to test what kind of reply was received:

- `REDIS_REPLY_STATUS`

The command replied with a status reply. The status string can be accessed using reply->str. The length of this string can be accessed using reply->len.

- `REDIS_REPLY_ERROR`

The command replied with an error. The error string can be accessed identical to REDIS_REPLY_STATUS.

## Recommender System

In this section, we will go through basic concepts of recommender system and Collaborative Filtering first, then demonstrate how they are implemented in the project.

Collaborative methods for recommender systems are methods that are based solely on the past interactions recorded between users and items in order to produce new recommendations. These interactions are stored in the so-called “user-item interactions matrix”.

Model based approaches assume an underlying “generative” model that explains the user-item interactions and try to discover it in order to make new predictions.
```
struct model_parameters
{
	size_t			users_number;
	size_t			items_number;

	size_t			dimensionality; /* dimensionality of the joint latent factor space */
	size_t			iteration_number;

	size_t			training_set_size;  /* The number of known ratings */

	float			lambda;          /* The constant lambda controls the extent of regularization */
	float			step;            /* step size in stochastic gradient descent algorithm */

	float			step_bias;
	float			lambda_bias;

	int				algoithm_type;

	int 			seed;
	int 			bin_width;
	int 			proj_family_size;
	float			betha;
};

typedef struct model_parameters model_parameters_t;
```

- Matrix Factorization

![](./Screenshots/7.png)

The dot product can be calculated by utils.c:
```
/*
 * Calculate the dot product
 */
double
dot_product(double* vect1, double* vect2, size_t dim)
{
	double sum = 0;
	size_t i;

	for (i = 0; i < dim; i++)
		sum += vect1[i] * vect2[i];
	
	return sum;
}
```
Reconstruct errors: 

regularized_squared_error:  Return the the regularized squared error on the set of known ratings:
```
double
regularized_squared_error(
			  double* user_vector,
			  double* item_vector,
			  double r,
			  double lambda,
			  size_t size)
{
	double diff = 
		(r - estimate_item_rating(user_vector, item_vector, size));

	return  diff * diff + 
		lambda * (length2(user_vector, size) + length2(item_vector, size));
}
```










