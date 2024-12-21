[1-Basics](1-Basics.md) [README](Bash-Notes/README.md) [3-Setting_Up](3-Setting_Up.md)

Tags: #bash #linux #shell-scripting #job-control #flow-control #file-handling #text-processing #functions 

---
## Flow Control and Text Processing in Bash

### Overview

This guide introduces essential Bash scripting concepts, including flow control, loops, functions, and text processing tools like `grep`, `awk`, and `sed`. By the end, you’ll have a better grasp of building scripts to automate tasks and manage text efficiently.

---

### A) Test Operators

In Bash, **test operators** allow us to execute commands based on conditions, such as file attributes, string comparisons, or numerical evaluations. Understanding these operators is crucial for effective scripting.

#### 1. File Test Operators

These operators help determine file attributes:

|**Operator**|**Description**|
|:-:|---|
|`-d`|Checks if the file is a directory|
|`-r`|Checks if the file is readable|
|`-x`|Checks if the file is executable|
|`-w`|Checks if the file is writable|
|`-f`|Checks if the file is a regular file|
|`-s`|Checks if the file size is greater than zero|

Example:

```bash
file="example.txt"
if [ -f "$file" ]; then
    echo "$file exists and is a regular file."
else
    echo "$file does not exist."
fi
```

#### 2. String Comparison Operators

These operators compare string values:

|**Operator**|**Description**|
|:-:|---|
|`=`|Checks if two strings are equal|
|`!=`|Checks if two strings are not equal|
|`<`|Checks if one string is lexicographically smaller|
|`>`|Checks if one string is lexicographically greater|
|`-z`|Checks if a string is empty|
|`-n`|Checks if a string is not empty|

Example:

```bash
string="hello"
if [ -z "$string" ]; then
    echo "String is empty."
else
    echo "String is: $string"
fi
```

#### 3. Integer Comparison Operators

These operators compare numerical values:

| **Operator** | **Description**                                  |
| :----------: | ------------------------------------------------ |
|    `-eq`     | Checks if two numbers are equal                  |
|    `-ne`     | Checks if two numbers are not equal              |
|    `-gt`     | Checks if one number is greater than another     |
|    `-lt`     | Checks if one number is less than another        |
|    `-ge`     | Checks if one number is greater than or equal to |
|    `-le`     | Checks if one number is less than or equal to    |

Example:

```bash
number=5
if [ $number -gt 0 ]; then
    echo "$number is positive."
fi
```

---

### B) `if` Conditions

The `if` statement evaluates conditions and runs code blocks based on their results. Here’s the syntax:

```bash
if [ condition ]; then
    # Code block if the condition is true
else
    # Code block if the condition is false
fi
```

#### Multiple Conditions

Combine conditions using `&&` (AND) or `||` (OR):

```bash
if [ condition1 ] && [ condition2 ]; then
    echo "Both conditions are true."
fi
```

#### Example: Check Command Success

We can test the success of a command using an `if` statement:

```bash
if touch test_file; then
    echo "File created successfully."
else
    echo "Failed to create file."
fi
```

#### Using `elif`

The `elif` statement handles additional conditions:

```bash
if [ $value -eq 1 ]; then
    echo "Value is 1."
elif [ $value -eq 2 ]; then
    echo "Value is 2."
else
    echo "Value is neither 1 nor 2."
fi
```

---

### C) Functions

Functions in Bash allow you to organize and reuse code. Here’s the syntax:

```bash
function_name() {
    # Code block
}
```

#### Example: Check Root User

```bash
check_if_root() {
    if [ "$EUID" -eq 0 ]; then
        echo "User is root."
    else
        echo "User is not root."
    fi
}

check_if_root
```

#### Accepting Arguments in Functions

Use positional parameters `$1`, `$2`, etc., to pass arguments:

```bash
print_args() {
    echo "First: $1, Second: $2"
}

print_args "arg1" "arg2"
```

---

### D) Loops and Loop Control

#### 1. `while` Loops

The `while` loop runs a block of code as long as a condition is true:

```bash
while [ condition ]; do
    # Code block
done
```

Example:

```bash
count=0
while [ $count -lt 5 ]; do
    echo "Count: $count"
    ((count++))
done
```

#### 2. `until` Loops

The `until` loop runs until a condition becomes true:

```bash
until [ condition ]; do
    # Code block
done
```

Example:

```bash
count=0
until [ $count -ge 5 ]; do
    echo "Count: $count"
    ((count++))
done
```

#### 3. `for` Loops

The `for` loop iterates over a list of values:

```bash
for value in list; do
    # Code block
done
```

Example:

```bash
for file in *.txt; do
    echo "Processing $file"
done
```

#### 4. Loop Control: `break` and `continue`

- **`break`**: Exit the loop early.
- **`continue`**: Skip to the next iteration.

Example:

```bash
for i in {1..5}; do
    if [ $i -eq 3 ]; then
        echo "Skipping $i"
        continue
    fi
    echo "Processing $i"
done
```

---

### E) Text Processing and Parsing

#### 1. `grep`

The `grep` command searches for patterns in text files.

**Basic Usage:**

```bash
grep "pattern" file.txt
```

**Examples:**

- Search for specific text:
```bash
grep "error" log.txt
```

- Case-insensitive search:
```bash
grep -i "error" log.txt
```

- Search for multiple patterns:
```bash
grep -e "error" -e "warning" log.txt
```

#### 2. `awk`
The `awk` command extracts and processes fields in text files.

**Examples:**

- Print the first column:
```bash
awk '{print $1}' file.txt
```

- Print specific columns:
```bash
awk '{print $1, $3}' file.txt
```

- Change the field delimiter (e.g., from spaces to commas):
```bash
awk -F',' '{print $1}' file.csv
```

#### 3. `sed`

The `sed` command edits text streams.

**Examples:**

- Replace text:
```bash
sed 's/old/new/g' file.txt
```

- Delete lines containing a pattern:
```bash
sed '/pattern/d' file.txt
```

---

### F) Job Control

#### Background Processes

Run a process in the background using `&`:

```bash
sleep 100 &
```

#### Managing Jobs

- List background jobs:
```bash
jobs
```

- Bring a job to the foreground:
```bash
fg %1
```

- Suspend a job (Ctrl+Z) and send it back to the background:
```bash
bg %1
````

#### Persistent Background Jobs
Keep a process running even after logging out using `nohup`:
```bash
nohup ./script.sh &
````

---

### Tasks

Write a script that:

1. Accepts two arguments: a name and a target domain.
2. Checks if arguments are provided, else exits with an error.
3. Pings the target domain and indicates success or failure.
4. Logs results to a CSV file with:
    - The provided name
    - The target domain
    - Ping result
    - Current date and time

#### Example Solution

```bash
#!/usr/bin/env bash

name="$1"
domain="$2"

if [ -z "$name" ] || [ -z "$domain" ]; then
    echo "Error: Missing arguments." >&2
    exit 1
fi

if ping -c 1 "$domain" &> /dev/null; then
    result="success"
else
    result="failure"
fi

echo "$name,$domain,$result,$(date)" >> results.csv
```

