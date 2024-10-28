---
title: File descriptor
date: 2020-12-21 06:10:10 +/-TTTT
categories: [CTF, pwnable]
tags: [c,fd]
---

This challenges are hosted on pwnable.kr website.

`ssh fd@pwnable.kr -p2222` using password `guest`.

we got three file.`flag`,`fd.c` and `fd` 

**fc.c**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
char buf[32];
int main(int argc, char* argv[], char* envp[]){
        if(argc<2){
                printf("pass argv[1] a number\n");
                return 0;
        }
        int fd = atoi( argv[1] ) - 0x1234;
        int len = 0;
        len = read(fd, buf, 32);
        if(!strcmp("LETMEWIN\n", buf)){
                printf("good job :)\n");
                system("/bin/cat flag");
                exit(0);
        }
        printf("learn about Linux file IO\n");
        return 0;

}
```

lets breakdown the code.


```c
buf[32]
```
create variable buf and asign 32 char of buffer space.

```c
if(argc<2){
                printf("pass argv[1] a number\n");
                return 0;
        }
```
check for argument if we doesn't provide any argument it print `pass argv[1] a number`

```c
int fd = atoi( argv[1] ) - 0x1234;
```
create variable fd. `atoi()` function convert strings to interger. and it subtract in `0x1234` . `0x1234` is hex number which is `4660` in decimal

```c
int len = 0;
```
define length variable and asign the value `0`.

```c
len = read(fd, buf, 32);
if(!strcmp("LETMEWIN\n", buf)){
                printf("good job :)\n");
                system("/bin/cat flag");
                exit(0);
        }
```
`read()` is a function that reads data previously written to a file.

`fd` : means file descriptor. there are three type of file descriptor.

1. stdin : standard input (0)
2. stdout : standard output (1)
3. stderr : standard output (2)

- when we run program and give input thats stdin
- when program gives output thats stdout.
- when pgogram gives us error that is stderr.

here we have to control `fd` and make it `0` 
because `read()` need `fd` as `0` to read our input.then we  get into `if` statement.

`if` statement compare `buf` to `LETMEWIN` and print flag.

we can control fd `int fd = atoi( argv[1] ) - 0x1234;` so if we subtract 4660 we get `fd` as `0` and we can get flag.

![fd.png](/assets/img/fd.png)