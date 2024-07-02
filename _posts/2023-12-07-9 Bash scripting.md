---
title: 9. Bash Scripting
date: 2023-12-07 11.11.11 +/-TTTT
categories: [Notes, Linux]
tags: [bash scripting ]    
img_path: /assets/img/notes/linux
---

# Intro to BASH


## Bourne Again Shell

- `Bash` is a scripting language we use to communicate with unix based os and give commands to the system.
- Since May 2019 , windows provides, `windows subsystem for linux` that allows us to use bash in windows environment.
-  scripting language structure can be divided into:
	- `Input` & `Output`
	- `Arguments`, `Variables` & `Arrays`
	- `Conditional execution`
	- `Arithmetic`
	- `Loops`
	- `Comparison operators`
	- `Functions`

***

# Working Component


## Condition Execution

- allows us to control the flow of our script by reaching different conditions.

#### IF Else Condition

```bash

#!/bin/bash # interpreter line.

if [ condition ] 
then
	echo "some code"

elif [ condition ] 
then
	echo " some code "

else
	echo " some code "
fi

```


***
## Argument, Variables, and Arrays

- we can pass upto 9 argument. (`$0 - $9`  `$0` is reserved for the script)

```shell-session
cyber7ron@htb[/htb]$ ./script.sh ARG1 ARG2 ARG3 ... ARG9
       ASSIGNMENTS:       $0      $1   $2   $3 ...   $9
```

- Special variables use the [Internal Field Separator](https://bash.cyberciti.biz/guide/$IFS) (`IFS`) to identify when an argument ends and the next begins.

|**IFS**|**Description**|
|---|---|
|`$#`|This variable holds the number of arguments passed to the script.|
|`$@`|This variable can be used to retrieve the list of command-line arguments.|
|`$n`|Each command-line argument can be selectively retrieved using its position. For example, the first argument is found at `$1`.|
|`$$`|The process ID of the currently executing process.|
|`$?`|The exit status of the script. This variable is useful to determine a command's success. The value 0 represents successful execution, while 1 is a result of a failure.|
#### Variables

- there must be no spaces between the names and values. `domain=value` 

#### Arrays

- using array we can process an ordered sequence of specific type values.

```bash
domains=(www.inlanefreight.com ftp.inlanefreight.com vpn.inlanefreight.com www2.inlanefreight.com)
```

***
## Comparison Operators

- Used to compare specific values with each other.

1. String operators
2. integer operators
3. file operators
4. boolean operators

#### String operators

|**Operator**|**Description**|
|---|---|
|`==`|is equal to|
|`!=`|is not equal to|
|`<`|is less than in ASCII alphabetical order|
|`>`|is greater than in ASCII alphabetical order|
|`-z`|if the string is empty (null)|
|`-n`|if the string is not null|
> [!info] String comparison operators "`<` / `>`" works only within the double square brackets `[[ <condition> ]]`.

#### Integer Operators

|**Operator**|**Description**|
|---|---|
|`-eq`|is equal to|
|`-ne`|is not equal to|
|`-lt`|is less than|
|`-le`|is less than or equal to|
|`-gt`|is greater than|
|`-ge`|is greater than or equal to|

#### File operators

|**Operator**|**Description**|
|---|---|
|`-e`|if the file exist|
|`-f`|tests if it is a file|
|`-d`|tests if it is a directory|
|`-L`|tests if it is if a symbolic link|
|`-N`|checks if the file was modified after it was last read|
|`-O`|if the current user owns the file|
|`-G`|if the file’s group id matches the current user’s|
|`-s`|tests if the file has a size greater than 0|
|`-r`|tests if the file has read permission|
|`-w`|tests if the file has write permission|
|`-x`|tests if the file has execute permission|

#### Boolean and Logical operators

- we get `true` or `false` as a result with logical operators.
-  we use `[[ <condition> ]]` for boolean operators.

**logical operators**

With logical operators, we can define several conditions within one.

| **Operator** | **Description**        |
| ------------ | ---------------------- |
| `!`          | logical negotation NOT |
| `&&`         | logical AND            |
| `\|`         | logical OR             |
***
## Arithmetic

In Bash, we have seven different `arithmetic operators` we can work with

|**Operator**|**Description**|
|---|---|
|`+`|Addition|
|`-`|Substraction|
|`*`|Multiplication|
|`/`|Division|
|`%`|Modulus|
|`variable++`|Increase the value of the variable by 1|
|`variable--`|Decrease the value of the variable by 1|

```bash
#!/bin/bash

increase=1
decrease=1

echo "Addition: 10 + 10 = $((10 + 10))"
echo "Substraction: 10 - 10 = $((10 - 10))"
echo "Multiplication: 10 * 10 = $((10 * 10))"
echo "Division: 10 / 10 = $((10 / 10))"
echo "Modulus: 10 % 4 = $((10 % 4))"

((increase++))
echo "Increase Variable: $increase"

((decrease--))
echo "Decrease Variable: $decrease"
```

We can also calculate the length of the variable. Using this function `${#variable}`
***

# Script Control


## Input and Output Control

**How to give input to our program**

```bash
# Available options
<SNIP>
echo -e "Additional options available:"
echo -e "\t1) Identify the corresponding network range of target domain."
echo -e "\t2) Ping discovered hosts."
echo -e "\t3) All checks."
echo -e "\t*) Exit.\n"

read -p "Select your option: " opt

case $opt in
	"1") network_range ;;
	"2") ping_host ;;
	"3") network_range && ping_host ;;
	"*") exit 0 ;;
esac
```

- the first echo lines display available options
- `read` is used to read a line of input from standard input. `-p` ensure that input remains on the same line.
- out input is stored in the variable `opt` 
- `case` is used to execute corresponding function start.

**How to display output on screen while script is running** 

```bash
# Identify Network range for the specified IP address(es)
function network_range {
	for ip in $ipaddr
	do
		netrange=$(whois $ip | grep "NetRange\|CIDR" | tee -a CIDR.txt)
		cidr=$(whois $ip | grep "CIDR" | awk '{print $2}')
		cidr_ips=$(prips $cidr)
		echo -e "\nNetRange for $ip:"
		echo -e "$netrange"
	done
}

<SNIP>

# Identify IP address of the specified domain
hosts=$(host $domain | grep "has address" | cut -d" " -f4 | tee discovered_hosts.txt)

<SNIP>
```

- When using `tee`, we transfer the received output and use the pipe (`|`) to forward it to `tee`. 
- The "`-a` / `--append`" parameter ensures that the specified file is not overwritten but supplemented with the new results. 
- At the same time, it shows us the results and how they will be found in the file.
***

***
## Flow Control - Loops

each control strucutre is either a branch or loop.

- Branches:
    
    - `If-Else` Conditions
    - `Case` Statements
- Loops:
    
    - `For` Loops
    - `While` Loops
    - `Until` Loops

#### For Loops

- process on each item from list or array.

```bash
for variable in 1 2 3 4
do
	echo $variable
done
```


```bash
for variable in file1 file2 file3
do
	echo $variable
done
```


```bash
for ip in "10.10.10.170 10.10.10.174 10.10.10.175"
do
	ping -c 1 $ip
done
```

#### While loop

- A statement is executed as long as a condition is fulfilled (`true`).

```bash

while [ <condition> ]
do
	#some statement
done
```

#### Until Loops

- The code inside a `until` loop is executed as long as the particular condition is `false`.

```bash
until [ $counter -eq 10 ]
do
  # Increase $counter by 1
  ((counter++))
  echo "Counter: $counter"
done
```



***
## Flow Control - Branches

#### case statement

In Bash, the `case` statement provides a way to evaluate a variable or expression against a list of patterns

```bash
case <expression> in
	pattern_1 ) statements ;;
	pattern_2 ) statements ;;
	pattern_3 ) statements ;;
esac
```

```bash
fruit="apple"

case $fruit in
    "apple")
        echo "It's an apple."
        ;;
    "banana")
        echo "It's a banana."
        ;;
    "orange")
        echo "It's an orange."
        ;;
    *)
        echo "It's something else."
        ;;
esac

```

```bash
# Available options
echo -e "Additional options available:"
echo -e "\t1) Identify the corresponding network range of target domain."
echo -e "\t2) Ping discovered hosts."
echo -e "\t3) All checks."
echo -e "\t*) Exit.\n"

read -p "Select your option: " opt

case $opt in
	"1") network_range ;;
	"2") ping_host ;;
	"3") network_range && ping_host ;;
	"*") exit 0 ;;
esac
```


***

# Execution Flow


## Functions

- There are two methods to define the functions:

```bash
function name {
	<commands>
}
```


```bash
name() {
	<commands>
}
```

- How to execute function

```bash
function_name
```

```bash
function_name arg1 arg2
```


#### Return value

- numerical value that represents the success or failure of a command or script. It's typically an integer ranging from 0 to 255, where 0 indicates success (or true) and any non-zero value indicates failure (or false).
- We use `$?` to access its return code.

```bash
ls non_exits_file ; echo "Exit status: $?"

# this will print exit status: 2 (if file not found)
```


|**Return Code**|**Description**|
|---|---|
|`1`|General errors|
|`2`|Misuse of shell builtins|
|`126`|Command invoked cannot execute|
|`127`|Command not found|
|`128`|Invalid argument to exit|
|`128+n`|Fatal error signal "`n`"|
|`130`|Script terminated by Control-C|
|`255\*`|Exit status out of range|



***
## Debugging

- Bash debugging is the process of removing errors from out code.
- bash allows us to debug code using `-x` and `-v` options.

```shell-session
bash -x CIDR.sh

+ '[' 0 -eq 0 ']'
+ echo -e 'You need to specify the target domain.\n'
You need to specify the target domain.

+ echo -e Usage:
Usage:
+ echo -e '\tCIDR.sh <domain>'
	CIDR.sh <domain>
+ exit 1
```

- Here Bash shows us precisely which function or command was executed with which values. This is indicated by the plus sign (`+`) at the beginning of the line.
-  If we want to see all the code for a particular function, we can set the "`-v`" option that displays the output in more detail.

```shell-session
cyber7ron@htb[/htb]$ bash -x -v CIDR.sh

#!/bin/bash

# Check for given argument
if [ $# -eq 0 ]
then
	echo -e "You need to specify the target domain.\n"
	echo -e "Usage:"
	echo -e "\t$0 <domain>"
	exit 1
else
	domain=$1
fi
+ '[' 0 -eq 0 ']'
+ echo -e 'You need to specify the target domain.\n'
You need to specify the target domain.

+ echo -e Usage:
Usage:
+ echo -e '\tCIDR.sh <domain>'
	CIDR.sh <domain>
+ exit 1
```

***
