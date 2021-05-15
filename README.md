# Overview of Codacy Analysis

![](./Screenshots/1.png)

![](./Screenshots/2.png)

There are 33 issues in total, 19 errors and 14 warnings respectively.

## Security Issues

![](./Screenshots/3.png)

- Null-terminated(0-terminated)

In computer programming, a null-terminated string is a character string stored as an array containing the characters and terminated with a null character (a character with a value of zero)

Consequences if a string is not null-terminated: Many library functions accept a string or wide string argument with the constraint that the string they receive is properly null-terminated. Passing a character sequence or wide character sequence that is not null-terminated to such a function can result in accessing memory that is outside the bounds of the object.

Problem happens at
```
strlen(argv[argc-1])
```

- Possible Solution Example
```
