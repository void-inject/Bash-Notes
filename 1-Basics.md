Status: Active(5%)

Tags: #bash #linux #terminal

Links: [README](Scripting/Bash/0-Notes/README.md)
___

# 1) Basics of Bash Scripting

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

## C) Basic syntax

___
## References:
- You can find full list of Bash variables [here](https://www.gnu.org/software/bash/manual/html_node/Bash-Variables.html)
- Bash doesn't have an official style guide, But it is recommended to use Google's [Shell Style guide](https://google.github.io/styleguide/shellguide.html)
- 

Created:: 2024-11-27 12:35
