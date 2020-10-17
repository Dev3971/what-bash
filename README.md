# what-bash

`what` is a Bash function that gets info about a command, like what exactly it is and where.

Along with it is `symlink_info`, which details complicated symlinks. `what` uses it on symlinked executable files.

## `what`

### Usage

Source `what.sh` to get the function `what`. The script can also be run directly, but it's not recommended since it won't have access to the shell environment like aliases.

### Example

```none
$ what if type what awk sh ls
if
    keyword
type
    builtin
what
    function
        source: /home/wja/scripts/lib/bash/what.sh:348
        export: no
awk
    file
        /usr/bin/awk
            symlink: /etc/alternatives/awk
            symlink: /usr/bin/mawk
        file type: ELF 64-bit LSB shared object
sh
    file
        /bin/sh
            symlink: dash
            canonical path: /bin/dash
        file type: ELF 64-bit LSB shared object
ls
    alias
        possible source: /home/wja/.bash_aliases
    file
        file type: ELF 64-bit LSB shared object
```

### Help

```none
$ what -h
Usage: what [-h] [-dnt] [name ...]

Give information about Bash command names, like a more thorough "type".

Arguments:
    NAME    Command name to give information about.
            If none are given, input is taken from stdin.

Options:
    -d      Print definitions for aliases and functions.
    -h      Print this help message and exit.
    -n      Provide more info if a command is not found.
            Uses "/usr/lib/command-not-found" (available on Debian/Ubuntu)
    -t      Print only types, similar to "type -at".

Info provided per type (types ordered by precedence):
    alias
        - possible source file and line number
            - (with option "-d": definition in file)
        - (with option "-d": current definition)
    keyword
    function
        - source file and line number
        - marked for export (yes/no)
        - (with option "-d": definition)
    builtin
    hashed file (though not a type per se)
        - path
        - (if hashed file does not exist: warning)
    file
        - path
            - (if symlink: target, recursively)
            - (if relative symlink: canonical path)
        - file type

Always iterates over multiple types/instances, e.g:
    - echo: builtin and file
    - zsh: two files on Debian

Exit Status:
    3 - Invalid options
    1 - At least one NAME is not found, or any other error
    0 - otherwise

For example:
    "what if type ls what zsh sh /"
    - Covers keyword, builtin, alias/file, function, multiple files/absolute symlinks, relative symlink (on Debian/Ubuntu), and non-command.

Known issues:
    - Some versions of Bash have different output between "type COMMAND" and "type -a COMMAND" if COMMAND is a file but is not executable. "what" will error if affected.
```

## `symlink_info`

Resolve a symlink, recursively and canonically

### Example

Borrowing from the above `what` example:

```none
$ symlink_info /usr/bin/awk /bin/sh
/usr/bin/awk
    symlink: /etc/alternatives/awk
    symlink: /usr/bin/mawk
/bin/sh
    symlink: dash
    canonical path: /bin/dash
```

## Installation

If you want command name completions, add `complete -c what` to your shell startup.

Everything else is your choice.

### Requirements

* Bash 4.3+
    * Untested on earlier versions
* Intended for Debian/Ubuntu, but should work on other Linux distros

## Roadmap

* Break the indentation functions into their own script

`what`

* Add colour

`symlink_info`

* Break into functions

## License

GNU GPLv3
