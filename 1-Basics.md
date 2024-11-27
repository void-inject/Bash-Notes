Status: Active(5%)

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

---

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

___
## References:
- You can find full list of Bash variables [here](https://www.gnu.org/software/bash/manual/html_node/Bash-Variables.html)
- Bash doesn't have an official style guide, But it is recommended to use Google's [Shell Style guide](https://google.github.io/styleguide/shellguide.html)
- Full list of Arithmetic Operators are [here](https://tldp.org/LDP/abs/html/ops.html)
- 

Created:: 2024-11-27 12:35
