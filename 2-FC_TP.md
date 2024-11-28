Status: On-Going

Tags: #bash #linux #shell 

Links: [1-Basics](1-Basics.md) [README](README.md) [3-Setting_Up](3-Setting_Up.md) 
___

# Flow Control and Text Processing
## A) Test Operators
Bash lets us selectively execute commands when certain conditions of interest are met. We can use test operators to craft a wide variety of conditions, such as whether one value equals another value, whether a file is of a certain type, or whether one value is greater than another. We often rely on such tests to determine whether to continue running a block of code, so being able to construct them is fundamental to bash programming.

- File test Operators:

| Operator | Description                                       |
| :------: | ------------------------------------------------- |
|    -d    | Checks whether the file is a directory            |
|    -r    | Checks whether the file is readable               |
|    -x    | Checks whether the file is executable             |
|    -w    | Checks whether the file is writable               |
|    -f    | Checks whether the file is a regular file         |
|    -s    | Checks whether the file size is greater than zero |

-  String Comparison Operators:

| Operator | Description                                                  |
| :------: | ------------------------------------------------------------ |
|    =     | Checks whether a string is equal to another string           |
|    ==    | Synonym of = when used within [[]] constructs                |
|    !=    | Checks whether a string is not equal to another string       |
|    <     | Checks whether a string comes before another string<br>(A-z) |
|    >     | Checks whether a string comes after another string<br>(A-z)  |
|    -z    | Checks whether a string is null                              |
|    -n    | Checks whether a string is not null                          |

- Integer Comparison Operators

| Operator | Description                                        |
| :------: | -------------------------------------------------- |
|   -eq    | Checks whether a number is equal to another number |
|   -ne    | Checks whether a number is not equal               |
|   -ge    | Checks whether a number is greater than/equal to   |
|   -gt    | Checks whether a number is greater than            |
|   -lt    | Checks whether a number is less than               |
|   -le    | Checks whether a number is less than/ equal to     |

---

## B) if Conditions
 Syntax:
```bash
#!/usr/bin/env bash

if [[ condition ]]; then
	# Code Block if condition is true
else
	# Code Block if condition is flase
fi

# In some operating systems, such as those often used in containers,
# the default shell might not necessarily be bash.
# To account for these cases, you may want to use single square 
# brackets ([...]) rather than double to enclose your condition.
# This use of single brackets meets the Portable Operating System Interface 
# standard and should work on almost any Unix derivative, including Linux.
```

We can check for multiple conditions within one if condition using `&&` and  `||` operators:
```bash
#!/usr/bin/env bash

if [ condition_1 && condition_2 ] then
	.....
else
	.....
fi
```

As we continue with bash scripting, not all of commands will end with success due to some reasons like:
- Lack of permissions
- Absence of command package
- Lack of space when downloading package
- Lack of data connection

Thanks to if condition we can test the exit code of commands:
```bash
if touch test123; then
	echo "SUCCESS: file created"
else
	echo "ERROR: file already exists"
fi
```

We can use `else if` as `elif`

---

## C) Functions

___
References: 
- Full List of file test operators are [here](https://ss64.com/bash/test.html)
- 

Created:: 2024-11-28 10:14