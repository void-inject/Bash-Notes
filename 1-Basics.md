Status: Complete

Tags: #bash #linux #shell-scripting #variables #flow-control #streams #file-redirection #debugging

Links: [README](README.md) [2-FC_TP](2-FC_TP.md)

---

# Basics of Bash Scripting

## A) Exploring the Shell

### Environment Variables

Environment variables are dynamic values that affect the behavior of processes running on a system.

- **View All Environment Variables**:

```bash
env
```
Displays a list of all environment variables loaded for the current Bash session.

- **Check the Value of a Specific Variable**:

```bash
echo "${SHELL}"
```
Prints the value of the `SHELL` variable, which specifies the default shell.

### Command Documentation

Use these commands to access manuals and help pages:

- **Manual Pages**:

```bash
man echo
```
Opens the full manual for the `echo` command.

- **Command Help**:

```bash
echo --help
```
Displays a concise help page, often a shorter alternative to `man`.

---

## B) Bash Script Essentials

### The Shebang Line

Every script should begin with a _shebang_ line, which specifies the interpreter for the script:

```bash
#!/bin/bash
```

This assumes `bash` is located at `/bin/bash`. For portability, use:

```bash
#!/usr/bin/env bash
```

This ensures the correct Bash interpreter is used, regardless of its location.

#### Shebang with Arguments

You can pass options to the Bash interpreter directly in the shebang line:

- **Debug Mode (-x):** Prints commands as they are executed.

```bash
#!/bin/bash -x
```

- **Syntax Check (-n):** Checks the script for syntax errors without executing it.

```bash
#!/bin/bash -n
```

- **Restricted Mode (-r):** Limits access to potentially harmful commands.

```bash
#!/bin/bash -r
```

#### Alternative Execution

Instead of modifying the shebang, pass options when running the script:

```bash
bash -x script.sh
```

### Running a Script

1. Create the script:

```bash
#!/usr/bin/env bash
echo "Hello, World!"
```
Save it as `hello.sh`.

2. Make the script executable:

```bash
chmod +x hello.sh
```

3. Run the script:

```bash
./hello.sh
```

Alternatively, execute the script without making it executable:

```bash
bash hello.sh
```

---

## C) Bash Syntax Basics

### Variables

Variables in Bash are untyped and can store any type of data (e.g., strings, numbers, command outputs).

#### Naming Rules:

- Use alphanumeric characters and underscores.
- Cannot start with a number.
- Cannot contain spaces.

#### Variable Assignment and Usage:

```bash
#!/usr/bin/env bash

name="John Doe"
echo "Hello, ${name}!"
```

Using `${}` is preferred for clarity and compatibility.

#### Command Substitution:

Assign the output of a command to a variable:

```bash
current_time=$(date +%T)
echo "The current time is ${current_time}."
```

#### Local Variables

Use the `local` keyword to create variables scoped to a function:

```bash
#!/bin/bash

greet_user() {
    local username="Alice"
    echo "Hello, ${username}!"
}

greet_user
echo "${username}"  # Will not print as 'username' is local.
```

---

### Arithmetic Operations

Bash supports basic arithmetic using different methods:

#### 1. Using `let`:

```bash
let result=5+5
echo "Result: ${result}"
```

#### 2. Using `$(( ))`:

```bash
result=$((10 * 2))
echo "Multiplication Result: ${result}"
```

#### 3. Using `expr`:

```bash
result=$(expr 50 / 2)
echo "Division Result: ${result}"
```

#### Arithmetic Operators Table:

|Operator|Description|
|---|---|
|+|Addition|
|-|Subtraction|
|*|Multiplication|
|/|Division|
|%|Modulo (Remainder)|

---

### Arrays

Bash supports single-dimensional arrays:

```bash
#!/usr/bin/env bash

# Define an array
servers=("192.168.1.1" "192.168.1.2" "192.168.1.3")

# Access elements
echo "First server: ${servers[0]}"

# Print all elements
echo "All servers: ${servers[@]}"

# Replace an element
servers[1]="192.168.1.20"

# Remove an element
unset servers[2]
```

---

### Standard Streams

Bash has three standard data streams:

|Stream Name|File Descriptor|Description|
|---|---|---|
|Standard Input|0|Input sent to a program|
|Standard Output|1|Program's standard output|
|Standard Error|2|Program's error messages|

#### Example:

Redirect stdout and stderr to separate files:

```bash
command > output.txt 2> error.txt
```

---

### Control Operators

Control operators manage the execution of commands:

|Operator|Description|
|---|---|
|`&&`|Execute next command only if previous succeeded.|
|`||
|`&`|Run command in the background.|
|`;`|Run commands sequentially.|

#### Example:

```bash
#!/usr/bin/env bash
mkdir test && echo "Directory created." || echo "Failed to create directory."
```

---

### Redirection Operators

Redirect input/output to files or commands:

|Operator|Description|
|---|---|
|`>`|Redirect output to a file.|
|`>>`|Append output to a file.|
|`2>`|Redirect error messages.|
|`<`|Redirect input from a file.|

#### Example:

```bash
echo "Hello World" > file.txt
cat < file.txt
```

---

## Task: Recording Your Name and the Date

### Script Requirements

Write a script that:

1. Accepts two command-line arguments: first name and last name.
2. Creates a file `output.txt`.
3. Writes the current date in `DD-MM-YYYY` format to `output.txt`.
4. Appends the full name to `output.txt`.
5. Creates a backup named `backup.txt`.
6. Displays the content of `output.txt`.

#### Example Solution

```bash
#!/usr/bin/env bash

# Accept arguments
firstname=$1
lastname=$2

# Validate input
if [[ -z "${firstname}" || -z "${lastname}" ]]; then
    echo "Usage: $0 <First Name> <Last Name>"
    exit 1
fi

# Write to output.txt
date_formatted=$(date +%d-%m-%Y)
echo "Date: ${date_formatted}" > output.txt
echo "Full Name: ${firstname} ${lastname}" >> output.txt

# Create a backup
cp output.txt backup.txt

# Display content
cat output.txt
```

#### Run the Script

```bash
chmod +x registration.sh
./registration.sh Void Inject
```