Status: Active(5%)

Tags: #bash #linux #terminal

Links: none
___

# 1) Basics

## A) exploring shell
- `#-> env`: shows the list of *environment variables* which BASH loads for every new session. Programs can use these variables for various purposes
- `#-> echo ${SHELL}`: example of how to check the value of individual variable
- `#-> man echo`: man(manual) is terminal-based guide that explains all about command, just insert it before any command
- `#-> echo -h/--help`: same as *man* but insert after command

## B) Bash Elements
### _The Shebang Line_
 Every script should start with _shebang_ line:
 ```bash
 #!/bin/bash
```
Sometimes, you will face with:
```bash
#!/usr/bin/env bash
```
This method of shebang is more portable than first one cause you will never know whether your target changed location of interpreter.

The shebang line also takes optional arguments like:
```bash
#!/bin/bash -x

# prints all commands and their argument to terminal as they are being executed
```

```bash
#!/bin/bash -n

# used for debugging instead of executing
```

```bash
#!/bin/bash -r

# restricts potentially dangrous commands within script
```

Specifying the shebang argument means modifying script, but you can also pass the argument:
`#-> bash -rx script.sh`

### _Execution_

Scripts can be as short as 2 lines: The shebang Line and Command
For our first script let's write this:
```bash
#!/usr/bin/env bash

echo "CTRL+C won’t save you now"
```
_P.S: CTRL+C used to kill the proccess on terminal_

Now, save the file as "script.sh".
open terminal navigate to directory where script is saved.
Make script executable: `#-> chmod u+x script.sh`
Then, Run the script: `#-> ./script.sh`

Also, you can do this: `#-> bash script.sh` instead of making script executable and then run.

## C) Basic syntax

___
## References:
- You can find full list of Bash variables [here](https://www.gnu.org/software/bash/manual/html_node/Bash-Variables.html)
- Bash doesn't have an official style guide, But it is recommended to use Google's [Shell Style guide](https://google.github.io/styleguide/shellguide.html)
- 

Created:: 2024-11-27 12:35
