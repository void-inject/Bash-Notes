Status: Complete

Tags: #bash #linux #shell

Links: [README](README.md) [2-FC_TP](2-FC_TP.md)
___

# Basics of Bash Scripting

## A) Exploring the Shell

- **Environment Variables**:  
    `#-> env`  
     Displays a list of _environment variables_ loaded by Bash for every session. These variables can be used by programs for various purposes.
    
- **Check Variable Values**:  
    `#-> echo ${SHELL}`  
     Example command to display the value of an individual environment variable.
    
- **Command Documentation**:
    `#-> man echo`  
     The `man` (manual) command provides detailed documentation about any terminal command. Precede any command with `man` to access its manual.
    `#-> echo -h` or `#-> echo --help`  
     Displays a concise help guide for the command, similar to the `man` command but placed after the command.

---

## B) Bash Elements

### The Shebang Line

Every script should begin with a _shebang_ line to specify the script interpreter:

```bash
#!/bin/bash
```

Alternatively, you may encounter this:

```bash
#!/usr/bin/env bash
```

This is a more portable shebang, as it ensures the script uses the correct `bash` interpreter even if its location differs across systems.

#### Optional Shebang Arguments:

The shebang can include arguments to modify script behavior:

```bash
#!/bin/bash -x
# Prints all commands and their arguments to the terminal as they execute.
```

```bash
#!/bin/bash -n
# Checks the script for syntax errors without executing it (useful for debugging).
```

```bash
#!/bin/bash -r
# Restricts the execution of potentially dangerous commands.
```

**Alternate Execution Method**:  
Instead of editing the shebang, pass the arguments directly when running the script:  
`#-> bash -rx script.sh`

### Script Execution Basics

Scripts can be as simple as two lines:

1. The shebang line.
2. The command(s).

#### Example:

Create a script that displays a message:

```bash
#!/usr/bin/env bash

echo "CTRL+C won’t save you now"
```

**Steps to Run the Script**:

1. Save the file as `script.sh`.
2. Open a terminal and navigate to the directory containing the script.
3. Make the script executable:  
    `#-> chmod u+x script.sh`
4. Execute the script:  
    `#-> ./script.sh`

**Alternate Execution Without Permissions**:  
You can run the script directly with Bash:  
`#-> bash script.sh`

---

## C) Basic syntax

Most basic scripts are just bunch of Linux commands:
```bash
#!/usr/bin/env bash

mkdir killyourself
touch killyourself/withpistol
ls -l killyourself
```
When command runs, bash waits until it finishes before advancing to the next line. But, what if you use long-lasting commands like downloading packages? remaining commands won't execute until all packages are downloaded, and you won't know which stage is script currently.
Writing sophisticated programs often requires using features like variables, conditions, loops, and tests.

### _Variables_
In other scripting languages variables have different types like _integers,_ _strings,_ and _arrays_
In bash, variables are untyped.

Rules of naming bash variables:
- They can include alphanumeric characters.
- They can't start with a number
- They can contain an underscore ( _ )
- They can't contain whitespaces

Assigning  and accessing variables:
```bash
#!/usr/bin/env bash

fact="You have no bitches"

echo "Fun fact about you:"
echo "${fact}"
# echo "$fact" prints same output. 
# still using {} considered best practice
 ```
You can also assign the output of a command to a variable by using the command substitution syntax $():
```bash
#!/usr/bin/env bash

root_directory=$(ls -ld /)

echo "${root_directory}"
```
you can unassign variable using: `#-> unset variable`

Global variables are those available to the entire program. But variables in bash can also be scoped so that they are accessible only from within a certain block of code. These local variables are declared using the _local_ keyword:
```bash
#!/bin/bash

PUBLISHER="No Starch Press"

print_name(){
local name
name="Black Hat Bash"
echo "${name} by ${PUBLISHER}"
}

print_name

echo "Variable ${name} will not be printed because it is a local variable."
```

### Arithmetic Operators

| Operator | Description                |
| :------: | -------------------------- |
|    +     | Addition                   |
|    -     | Subtraction                |
|    *     | Multiplication             |
|    /     | Division                   |
|    %     | Modulo                     |
|    +=    | Incrementing by a constant |
|    -=    | decrementing by a constant |

You can perform operations in bash with few methods:
- Using _let_ command:
```bash
  let result='4 * 5'
  echo ${result}
  ```
- Using `$((expression))`:
```bash
result=$((5 * 5))
echo ${result}
```
- Using _expr_ command:
```bash
result=$(expr 5 + 505)
echo ${result}
```
   The _expr_ command evaluates expressions, which don't have to be arithmetic operations.
### Arrays
Bash allows you to create single-dimension arrays. You might use arrays whenever you need to iterate over multiple strings and run the same commands on each one
```bash
#!/usr/bin/env bash

# set an array
IP_ADDRESSES=(192.168.1.1 192.168.1.2 192.168.1.3)

# print all elements
echo "${IP_ADDRESSES[*]}"

# print first element only
echo "${IP_ADDRESSES[0]}"

# removes second element
unset IP_ADDRESSES[1]

# swaps third element
IP_ADDRESSES[2]="192.168.1.10"

```

### Streams
Streams are files that act as communication channels between a program and its environment. Bash has 3 standard data streams:

| Stream name              | Description                         | File descriptor number |
| ------------------------ | ----------------------------------- | ---------------------- |
| Standard input (stdin)   | Data coming into a program as input | 0                      |
| Standard output (stdout) | Data coming out of a program        | 1                      |
| Standard error (stderr)  | Errors coming out of a program      | 2                      |

### Control Operators
In bash, control operators are tokens that perform a control function:

| Operator | Descriptor                                                                                                 |
| :------: | ---------------------------------------------------------------------------------------------------------- |
|    &     | Sends a command to the background.<br>Next command will run even previous command hasn't finished its work |
|    &&    | Logical AND. The second command will run only if the first command evaluates to true                       |
| ( and )  | Used for command grouping                                                                                  |
|    ;     | Used as a list terminator                                                                                  |
|    ;;    | Ends a case statement                                                                                      |
|    \|    | Redirects the output of a command as input to another command                                              |
|   \|\|   | Logical OR. The second command will run if the first one evaluates to false                                |

### Redirection Operators
Redirection is taking output from one command
or script and using it as input to another script or file for writing purposes

| Operator | Description                                                                            |
| -------- | -------------------------------------------------------------------------------------- |
| >        | Redirects stdout to a file                                                             |
| >>       | Redirects stdout to a file by appending it to existing content                         |
| &> or >& | Redirects stdout and stderr to a file                                                  |
| &>>      | same as previous but appends to existing content                                       |
| <        | Redirects input to a command                                                           |
| <<       | Called a _here document_, or _heredoc_, redirects multiple input lines<br>to a command |

### Positional Arguments
Also called _parameters_. A bash script can access arguments passed to it on the command line
by using the variables $1, $2, and so on.

|     Variable     | Description                                      |
| :--------------: | ------------------------------------------------ |
|        $0        | The name of script file                          |
| $1, $2, $3, .... | Positional Argument                              |
|        $#        | The Number of passed parameters                  |
|        $*        | All positional arguments                         |
|        $@        | same, where each argument is individually quoted |

### Input Prompting
Some bash scripts don’t take any arguments during execution. However, they may need to ask the user for information in an interactive way and have the response feed into their runtime. In these cases, we can use the _read_ command.
```bash
#!/usr/bin/env bash

# Takes input from the user and assigns it to variables
echo "What is your first name?"
read -r firstname

echo "What is your last name?"
read -r lastname

echo "Your first name is ${firstname} and your last name is ${lastname}"
```

### Exit Codes
Bash commands return exit codes, which indicate whether the execution of
the command succeeded. Exit codes fall in the 0 to 255 range, where 0 means
success, 1 means failure, 126 means that the command was found but is not
executable, and 127 means the command was not found. The meaning of any
other number depends on the specific command being used and its logic.

```bash
#!/usr/bin/env bash
# experiment with exit codes

ls -l > /dev/null
echo "The exit code of the ls command was: $?"

lzl 2> /dev/null
echo "The exit code of the lzl command was: $?"
```

```bash
#!/usr/bin/env bash
# setting exit code manually

echo "Exiting with exit code: 223"
exit 223
```
Run the script and check exit code after it finishes with `echo $?` which means:
You can use the $? variable to check the returned exit code not only of a script but also of individual commands after they finished their task.

___

## Task: Recording Your Name and the Date
Write a script that does the following:
1. Accepts two arguments on the command line and assigns them to variables. The first argument should be your first name, and the second should be your last name.
2. Creates a new file named _output.txt_
3. Writes the current date to _output.txt_ by using the _date_ command. (Bonus points if you can make the date command print the date in the DD-MM-YYYY format; use `#-> man date` to learn how this works.)
4. Writes your full name to _output.txt_
5. Makes a backup copy of _output.txt_, named _backup.txt_, using the _cp_ command. (Use _man cp_ if you aren’t sure of the command’s syntax.)
6. Prints the content of the _output.txt_ file to the standard output stream.

You can find an example solution, in dedicated branch named [here](link)

---

## References:
- You can find full list of Bash variables [here](https://www.gnu.org/software/bash/manual/html_node/Bash-Variables.html)
- Bash doesn't have an official style guide, But it is recommended to use Google's [Shell Style guide](https://google.github.io/styleguide/shellguide.html)
- Full list of Arithmetic Operators are [here](https://tldp.org/LDP/abs/html/ops.html)

Created:: 2024-11-27 12:35
