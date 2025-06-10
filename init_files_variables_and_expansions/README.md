# Shell, Init Files, Variables and Expansions

## Table of Contents
- [Shell Basics](#shell-basics)
- [Init Files](#init-files)
- [Variables](#variables)
- [Expansions](#expansions)
- [Best Practices](#best-practices)
- [Common Examples](#common-examples)

## Shell Basics

The shell is a command-line interpreter that provides a user interface for Unix-like operating systems. It processes commands and executes programs.

### Popular Shells
- **Bash** (Bourne Again Shell) - Most common on Linux
- **Zsh** (Z Shell) - Default on macOS (since Catalina)
- **Fish** (Friendly Interactive Shell) - User-friendly with syntax highlighting
- **Dash** - Lightweight POSIX shell

### Check Your Current Shell
```bash
echo $SHELL
# or
ps -p $$
```

## Init Files

Init files are configuration files that shells read when they start up. They allow you to customize your shell environment.

### Bash Init Files

#### Interactive Login Shell
1. `/etc/profile` (system-wide)
2. `~/.bash_profile`
3. `~/.bash_login`
4. `~/.profile`

#### Interactive Non-Login Shell
1. `/etc/bash.bashrc` (system-wide)
2. `~/.bashrc`

#### Non-Interactive Shell
- Uses `$BASH_ENV` if set

### Zsh Init Files

#### Order of Execution
1. `/etc/zshenv`
2. `~/.zshenv`
3. `/etc/zprofile` (login shells)
4. `~/.zprofile` (login shells)
5. `/etc/zshrc` (interactive shells)
6. `~/.zshrc` (interactive shells)
7. `/etc/zlogin` (login shells)
8. `~/.zlogin` (login shells)

### Common Init File Usage

```bash
# ~/.bashrc or ~/.zshrc

# Environment variables
export PATH="$HOME/bin:$PATH"
export EDITOR="vim"

# Aliases
alias ll='ls -la'
alias grep='grep --color=auto'

# Functions
mkcd() {
    mkdir -p "$1" && cd "$1"
}

# Shell options
set -o vi  # Vi mode
```

## Variables

### Types of Variables

#### Local Variables
```bash
# Set local variable
my_var="hello"

# Display variable
echo $my_var
echo "$my_var"  # Recommended to prevent word splitting
```

#### Environment Variables
```bash
# Set environment variable
export MY_VAR="hello world"

# Or in one line
MY_VAR="hello world"
export MY_VAR

# Display all environment variables
env
printenv
```

#### Special Variables

| Variable | Description |
|----------|-------------|
| `$0` | Script name |
| `$1, $2, ...` | Positional parameters |
| `$#` | Number of parameters |
| `$@` | All parameters as separate words |
| `$*` | All parameters as single word |
| `$$` | Process ID of current shell |
| `$!` | Process ID of last background command |
| `$?` | Exit status of last command |

### Variable Assignment Rules

```bash
# Correct
VAR="value"
VAR=value

# Incorrect (spaces around =)
VAR = "value"  # This won't work

# Multiple variables
VAR1="value1" VAR2="value2" command
```

### Reading Variables

```bash
# Read user input
read -p "Enter your name: " username
echo "Hello, $username"

# Read with default value
read -p "Enter port [8080]: " port
port=${port:-8080}
```

## Expansions

Shell expansions are performed before command execution. Understanding them is crucial for effective shell scripting.

### Parameter Expansion

#### Basic Expansion
```bash
name="John"
echo $name        # John
echo ${name}      # John (explicit form)
```

#### Advanced Parameter Expansion

```bash
# Default values
echo ${VAR:-default}     # Use "default" if VAR is unset or empty
echo ${VAR:=default}     # Set VAR to "default" if unset or empty
echo ${VAR:+alternate}   # Use "alternate" if VAR is set and non-empty
echo ${VAR:?error}       # Display error if VAR is unset or empty

# String manipulation
string="Hello World"
echo ${string#Hello }    # "World" (remove shortest match from beginning)
echo ${string%World}     # "Hello " (remove shortest match from end)
echo ${string/World/Universe}  # "Hello Universe" (substitute)
echo ${string//l/L}      # "HeLLo WorLd" (substitute all)

# Length
echo ${#string}          # 11 (length of string)

# Substring
echo ${string:0:5}       # "Hello" (substring from position 0, length 5)
echo ${string:6}         # "World" (substring from position 6)
```

### Command Substitution

```bash
# Modern syntax (preferred)
current_date=$(date)
files=$(ls -1)

# Old syntax (still works)
current_date=`date`
files=`ls -1`

# Nested command substitution
echo "Today is $(date +%A), $(date +%B) $(date +%d)"
```

### Arithmetic Expansion

```bash
# Basic arithmetic
echo $((2 + 3))          # 5
echo $((10 * 5))         # 50

# With variables
a=10
b=5
echo $((a + b))          # 15
echo $((a * b))          # 50

# Increment/decrement
count=0
echo $((++count))        # 1 (pre-increment)
echo $((count++))        # 1 (post-increment, count becomes 2)
```

### Brace Expansion

```bash
# Range expansion
echo {1..5}              # 1 2 3 4 5
echo {a..z}              # a b c ... z
echo {01..10}            # 01 02 03 ... 10

# List expansion
echo {apple,banana,cherry}  # apple banana cherry

# Combination
echo file{1..3}.txt      # file1.txt file2.txt file3.txt

# Nested braces
echo {a,b}{1,2}          # a1 a2 b1 b2
```

### Pathname Expansion (Globbing)

```bash
# Wildcards
ls *.txt                 # All .txt files
ls file?.txt             # file1.txt, fileA.txt, etc.
ls file[1-3].txt         # file1.txt, file2.txt, file3.txt

# Extended globbing (bash)
shopt -s extglob
ls !(*.txt)              # All files except .txt files
ls *.@(jpg|png|gif)      # Files with jpg, png, or gif extensions
```

### Tilde Expansion

```bash
echo ~                   # /home/username
echo ~/Documents         # /home/username/Documents
echo ~user               # /home/user
echo ~+                  # Current directory (same as $PWD)
echo ~-                  # Previous directory (same as $OLDPWD)
```

## Best Practices

### Variable Naming
```bash
# Good
USER_NAME="john"
DATABASE_URL="localhost:5432"

# Avoid
username="john"          # Might conflict with system variables
2var="value"             # Can't start with number
my-var="value"           # Hyphens not allowed
```

### Quoting
```bash
# Always quote variables that might contain spaces
file_name="my file.txt"
cat "$file_name"         # Correct
cat $file_name           # Wrong - will fail

# Use single quotes for literal strings
echo 'The variable $HOME will not be expanded'

# Use double quotes for variable expansion
echo "Your home directory is $HOME"
```

### Safe Scripting
```bash
#!/bin/-bash
set -euo pipefail        # Exit on error, undefined vars, pipe failures

# Check if variable is set
if [[ -n "${VAR:-}" ]]; then
    echo "VAR is set to: $VAR"
fi

# Default values
CONFIG_FILE="${CONFIG_FILE:-/etc/myapp.conf}"
```

## Common Examples

### Profile Setup Example
```bash
# ~/.bashrc or ~/.zshrc

# Environment variables
export EDITOR="code"
export BROWSER="firefox"
export PATH="$HOME/.local/bin:$PATH"

# Aliases
alias la='ls -la'
alias ll='ls -l'
alias ..='cd ..'
alias ...='cd ../..'
alias grep='grep --color=auto'
alias df='df -h'
alias du='du -h'

# Functions
extract() {
    if [ -f "$1" ]; then
        case "$1" in
            *.tar.bz2)   tar xjf "$1"     ;;
            *.tar.gz)    tar xzf "$1"     ;;
            *.bz2)       bunzip2 "$1"     ;;
            *.rar)       unrar x "$1"     ;;
            *.gz)        gunzip "$1"      ;;
            *.tar)       tar xf "$1"      ;;
            *.tbz2)      tar xjf "$1"     ;;
            *.tgz)       tar xzf "$1"     ;;
            *.zip)       unzip "$1"       ;;
            *.Z)         uncompress "$1"  ;;
            *.7z)        7z x "$1"        ;;
            *)           echo "'$1' cannot be extracted" ;;
        esac
    else
        echo "'$1' is not a valid file"
    fi
}

# Git prompt (for bash)
parse_git_branch() {
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/'
}
PS1='\u@\h:\w$(parse_git_branch)\$ '
```

### Script Template
```bash
#!/bin/bash
set -euo pipefail

# Script configuration
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
SCRIPT_NAME="$(basename "$0")"

# Default values
VERBOSE=${VERBOSE:-false}
DRY_RUN=${DRY_RUN:-false}

# Functions
log() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $*" >&2
}

usage() {
    cat << EOF
Usage: $SCRIPT_NAME [OPTIONS]

OPTIONS:
    -v, --verbose    Enable verbose output
    -n, --dry-run    Show what would be done without executing
    -h, --help       Show this help message

EXAMPLES:
    $SCRIPT_NAME --verbose
    $SCRIPT_NAME --dry-run

EOF
}

# Parse command line arguments
while [[ $# -gt 0 ]]; do
    case $1 in
        -v|--verbose)
            VERBOSE=true
            shift
            ;;
        -n|--dry-run)
            DRY_RUN=true
            shift
            ;;
        -h|--help)
            usage
            exit 0
            ;;
        *)
            log "Unknown option: $1"
            usage
            exit 1
            ;;
    esac
done

# Main script logic
main() {
    log "Starting $SCRIPT_NAME"
    
    if [[ "$VERBOSE" == true ]]; then
        log "Verbose mode enabled"
    fi
    
    if [[ "$DRY_RUN" == true ]]; then
        log "Dry run mode enabled"
    fi
    
    # Your script logic here
    
    log "Finished $SCRIPT_NAME"
}

# Run main function
main "$@"
```

This README covers the fundamentals of shell environments, configuration, variables, and expansions. Use it as a reference for understanding and working with shell scripting and customization.
