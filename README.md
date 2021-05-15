# Overview of Codacy Analysis

![](./Screenshots/1.png)

![](./Screenshots/2.png)

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


