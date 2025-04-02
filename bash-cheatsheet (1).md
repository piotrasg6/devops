# Bash Command and Scripting Cheat Sheet

## Basic Shell Usage

### Command Structure
```bash
command -options arguments
```

### Command History
```bash
history                    # Show command history
!n                         # Run command number n from history
!!                         # Repeat the last command
!string                    # Run the most recent command starting with 'string'
!?string                   # Run the most recent command containing 'string'
Ctrl+R                     # Reverse search through command history
```

### Keyboard Shortcuts
```bash
Ctrl+C                     # Interrupt (kill) current command
Ctrl+Z                     # Suspend current command
Ctrl+D                     # Exit current shell session
Ctrl+L                     # Clear the screen
Ctrl+A                     # Move cursor to beginning of line
Ctrl+E                     # Move cursor to end of line
Ctrl+U                     # Delete from cursor to beginning of line
Ctrl+K                     # Delete from cursor to end of line
Ctrl+W                     # Delete word before cursor
Alt+B                      # Move backward one word
Alt+F                      # Move forward one word
Tab                        # Auto-complete commands, filenames, and directories
```

## Navigation

### Directory Operations
```bash
pwd                        # Print working directory
cd directory               # Change to specified directory
cd                         # Change to home directory
cd ~                       # Change to home directory
cd -                       # Change to previous directory
cd ..                      # Go up one directory
cd ../..                   # Go up two directories
```

### Directory Listing
```bash
ls                         # List files and directories
ls -l                      # Long format listing
ls -a                      # List all files (including hidden)
ls -la                     # Long format listing including hidden files
ls -lh                     # Long format with human-readable file sizes
ls -lt                     # List sorted by modification time (newest first)
ls -lS                     # List sorted by size (largest first)
ls -R                      # List recursively including subdirectories
ls -d */                   # List only directories
```

## File Operations

### File Management
```bash
touch file                 # Create empty file or update timestamp
mkdir directory            # Create a directory
mkdir -p dir1/dir2/dir3    # Create nested directories
rm file                    # Remove a file
rm -f file                 # Force remove without confirmation
rm -r directory            # Remove directory and contents recursively
rm -rf directory           # Force remove directory and contents recursively
cp source destination      # Copy file
cp -r source destination   # Copy directory recursively
mv source destination      # Move/rename file or directory
ln -s file linkname        # Create symbolic link
```

### File Viewing
```bash
cat file                   # Display file content
less file                  # View file with paging
head file                  # Display first 10 lines
head -n 20 file            # Display first 20 lines
tail file                  # Display last 10 lines
tail -n 20 file            # Display last 20 lines
tail -f file               # Display last lines and follow updates (real-time)
```

### Finding Files
```bash
find path -name "pattern"  # Find files by name
find path -type f          # Find only files
find path -type d          # Find only directories
find path -mtime -7        # Find files modified less than 7 days ago
find path -size +10M       # Find files larger than 10MB
find path -exec command {} \;  # Execute command on each found file
```

### File Permissions
```bash
chmod permissions file     # Change file permissions
chmod 755 file             # rwx for owner, rx for group and others
chmod +x file              # Add execute permission
chmod -w file              # Remove write permission
chmod -R 755 directory     # Recursively change permissions
chown user:group file      # Change file owner and group
chown -R user:group dir    # Recursively change owner and group
```

### Compression and Archives
```bash
tar -cf archive.tar files      # Create tar archive
tar -xf archive.tar            # Extract tar archive
tar -czf archive.tar.gz files  # Create compressed tar archive
tar -xzf archive.tar.gz        # Extract compressed tar archive
tar -cjf archive.tar.bz2 files # Create bzip2 compressed archive
tar -xjf archive.tar.bz2       # Extract bzip2 compressed archive
gzip file                      # Compress file (creates .gz)
gunzip file.gz                 # Decompress .gz file
zip archive.zip files          # Create zip archive
unzip archive.zip              # Extract zip archive
```

## Text Processing

### File Content Manipulation
```bash
grep pattern file          # Search for pattern in file
grep -i pattern file       # Case-insensitive search
grep -r pattern directory  # Recursive search in directory
grep -v pattern file       # Show lines NOT matching pattern
grep -n pattern file       # Show line numbers
grep -l pattern files      # Only show filenames of matches
grep -c pattern file       # Count matching lines

sed 's/old/new/' file      # Replace first occurrence of 'old' with 'new'
sed 's/old/new/g' file     # Replace all occurrences of 'old' with 'new'
sed -i 's/old/new/g' file  # Replace in place (modifies file)
sed '1,5d' file            # Delete lines 1-5
sed '/pattern/d' file      # Delete lines containing pattern

awk '{print $1}' file      # Print first column
awk -F, '{print $1}' file  # Print first column with comma as delimiter
awk '{print $NF}' file     # Print last column
awk '{sum+=$1} END {print sum}' file  # Sum first column

sort file                  # Sort file lines alphabetically
sort -n file               # Sort numerically
sort -r file               # Sort in reverse order
sort -k2 file              # Sort by second column

uniq file                  # Remove adjacent duplicate lines
uniq -c file               # Count occurrences of lines
uniq -d file               # Only show duplicate lines

cut -d: -f1 file           # Cut out first field using : as delimiter
cut -c1-10 file            # Cut out first 10 characters

wc file                    # Count lines, words, and characters
wc -l file                 # Count lines only
wc -w file                 # Count words only
wc -c file                 # Count characters only
```

### Text File Comparison
```bash
diff file1 file2           # Compare two files line by line
diff -u file1 file2        # Unified format diff (more readable)
sdiff file1 file2          # Side-by-side comparison
cmp file1 file2            # Compare files byte by byte
```

## Process Management

### Process Information
```bash
ps                         # List current processes
ps aux                     # List all processes in BSD format
ps -ef                     # List all processes in full format
top                        # Display and update sorted process info
htop                       # Interactive process viewer (may need installation)
```

### Process Control
```bash
kill PID                   # Send TERM signal to process
kill -9 PID                # Force kill process (SIGKILL)
killall process_name       # Kill all processes with the name
pkill pattern              # Kill processes matching pattern
pgrep pattern              # List process IDs matching pattern
bg                         # Move job to background
fg                         # Move job to foreground
fg %n                      # Move job number n to foreground
jobs                       # List all background jobs
nohup command &            # Run command immune to hangups
command &                  # Run command in background
disown                     # Detach a background process from terminal
```

## System Information

### System Status
```bash
uname -a                   # Print all system information
hostname                   # System hostname
uptime                     # Show system uptime and load
date                       # Display current date and time
cal                        # Display calendar
w                          # Show who is logged in and what they're doing
whoami                     # Show current username
id                         # Show user and group IDs
```

### System Resources
```bash
free -h                    # Display memory usage in human readable format
df -h                      # Show disk space usage
du -sh directory           # Show directory space usage
lsblk                      # List block devices
lsusb                      # List USB devices
lspci                      # List PCI devices
vmstat                     # Report virtual memory statistics
iostat                     # Report CPU and I/O statistics
```

### Network Information
```bash
ifconfig                   # Display network interfaces (may need net-tools)
ip addr                    # Modern alternative to ifconfig
netstat -tuln              # List listening ports
ss -tuln                   # Modern alternative to netstat
ping host                  # Ping a host
traceroute host            # Trace route to host
dig domain                 # DNS lookup
nslookup domain            # Another DNS lookup utility
host domain                # Simple DNS lookup
curl url                   # Transfer data from/to a server
wget url                   # Download files from the web
ssh user@host              # Connect to host as user via SSH
scp file user@host:/path   # Copy file to remote host
rsync -av source dest      # Synchronize files/directories
```

## Input/Output Redirection

### Redirection Operators
```bash
command > file             # Redirect stdout to file (overwrite)
command >> file            # Redirect stdout to file (append)
command < file             # Redirect file as stdin to command
command 2> file            # Redirect stderr to file
command &> file            # Redirect both stdout and stderr to file
command | another_command  # Pipe stdout of command to another_command
command |& another_command # Pipe both stdout and stderr
command1 && command2       # Run command2 if command1 succeeds
command1 || command2       # Run command2 if command1 fails
command &                  # Run command in background
```

### Special Files
```bash
/dev/null                  # Discard output (black hole)
/dev/zero                  # Source of null bytes
/dev/random                # Source of random bytes
/dev/urandom               # Source of non-blocking random bytes
```

## Bash Scripting

### Script Basics
```bash
#!/bin/bash                # Shebang line (script interpreter)
# Comment                  # Single line comment
set -e                     # Exit on error
set -x                     # Print commands for debugging
set -u                     # Treat unset variables as errors
set -o pipefail            # Return value of pipeline is status of last failed command
```

### Variables
```bash
var="value"                # Assign value to variable (no spaces around =)
echo $var                  # Access variable value
echo ${var}                # Access variable value (explicit form)
readonly var="constant"    # Create read-only variable
unset var                  # Remove variable
export var                 # Export variable to child processes
VAR=value command          # Set variable only for the duration of command
```

### Variable Operations
```bash
${var:-default}            # Use default if var is unset or empty
${var:=default}            # Set var to default if var is unset or empty
${var:+value}              # Use value if var is set, otherwise empty string
${var:?error}              # Display error and exit if var is unset or empty
${#var}                    # Length of var
${var#pattern}             # Remove shortest match of pattern from beginning
${var##pattern}            # Remove longest match of pattern from beginning
${var%pattern}             # Remove shortest match of pattern from end
${var%%pattern}            # Remove longest match of pattern from end
${var/pattern/replacement} # Replace first match of pattern with replacement
${var//pattern/replacement}# Replace all matches of pattern with replacement
${var^}                    # Convert first character to uppercase
${var^^}                   # Convert all characters to uppercase
${var,}                    # Convert first character to lowercase
${var,,}                   # Convert all characters to lowercase
```

### Special Variables
```bash
$0                         # Script name
$1, $2, ...                # Positional parameters
$#                         # Number of positional parameters
$*                         # All positional parameters as single word
$@                         # All positional parameters as separate strings
$?                         # Exit status of last command
$$                         # Process ID of current shell
$!                         # Process ID of last background command
$_                         # Last argument of previous command
```

### Arrays
```bash
array=("value1" "value2")  # Define array
array[0]="value1"          # Set array element
echo ${array[0]}           # Access array element
echo ${array[@]}           # Access all array elements
echo ${#array[@]}          # Array length
echo ${!array[@]}          # Array indices
unset array[1]             # Remove array element
```

### Conditional Statements
```bash
# If statement
if condition; then
    commands
elif condition; then
    commands
else
    commands
fi

# Case statement
case value in
    pattern1)
        commands;;
    pattern2)
        commands;;
    *)
        commands;;  # Default case
esac
```

### Conditional Tests
```bash
# File tests
[ -e file ]                # File exists
[ -f file ]                # Regular file exists
[ -d directory ]           # Directory exists
[ -r file ]                # File is readable
[ -w file ]                # File is writable
[ -x file ]                # File is executable
[ -s file ]                # File is not empty
[ file1 -nt file2 ]        # File1 is newer than file2
[ file1 -ot file2 ]        # File1 is older than file2

# String tests
[ -z string ]              # String is empty
[ -n string ]              # String is not empty
[ string1 = string2 ]      # Strings are equal
[ string1 != string2 ]     # Strings are not equal
[[ string =~ pattern ]]    # String matches regex pattern

# Numeric tests
[ num1 -eq num2 ]          # Equal
[ num1 -ne num2 ]          # Not equal
[ num1 -lt num2 ]          # Less than
[ num1 -le num2 ]          # Less than or equal
[ num1 -gt num2 ]          # Greater than
[ num1 -ge num2 ]          # Greater than or equal

# Logical operators
[ condition1 -a condition2 ]   # AND (obsolete, use && instead)
[ condition1 -o condition2 ]   # OR (obsolete, use || instead)
[ ! condition ]                # NOT

# Modern condition syntax
[[ condition1 && condition2 ]] # AND
[[ condition1 || condition2 ]] # OR
[[ ! condition ]]              # NOT
```

### Loops
```bash
# For loop (iterate over values)
for var in value1 value2 value3; do
    commands
done

# For loop (iterate over range)
for ((i=0; i<10; i++)); do
    commands
done

# While loop
while condition; do
    commands
done

# Until loop
until condition; do
    commands
done

# Loop control
break                      # Exit loop
continue                   # Skip to next iteration
```

### Functions
```bash
# Function definition
function_name() {
    commands
    return value           # Optional return value (0-255)
}

# Function with parameters
function_name() {
    local var="value"      # Local variable
    echo $1                # First parameter
    echo $2                # Second parameter
}

# Function call
function_name arg1 arg2

# Capture function output
result=$(function_name arg1 arg2)
```

### Input and Output
```bash
echo "Text"                # Print text
echo -e "Text\nNewline"    # Interpret escape sequences
echo -n "No newline"       # Don't add newline

printf "Format %s %d" "string" 42  # Formatted output

read var                   # Read input into variable
read -p "Prompt: " var     # Read with prompt
read -s var                # Read silently (for passwords)
read -n 5 var              # Read only 5 characters
read -t 10 var             # Timeout after 10 seconds
read -a array              # Read into array
```

### Command Substitution
```bash
result=$(command)          # Store command output in variable
result=`command`           # Old syntax for command substitution
```

### Arithmetic
```bash
((result=10+5))            # Arithmetic expression
result=$((10+5))           # Arithmetic expansion
let "result = 10 + 5"      # Another way for arithmetic

# Arithmetic operators
$((a + b))                 # Addition
$((a - b))                 # Subtraction
$((a * b))                 # Multiplication
$((a / b))                 # Division (integer)
$((a % b))                 # Modulus
$((a ** b))                # Exponentiation
$((a++))                   # Post-increment
$((++a))                   # Pre-increment
$((a--))                   # Post-decrement
$((--a))                   # Pre-decrement
```

### Debug Mode
```bash
bash -n script.sh          # Check syntax without executing
bash -v script.sh          # Display script lines as they are read
bash -x script.sh          # Display commands and their arguments during execution
```

## Advanced Features

### Here Documents
```bash
cat << EOF > file.txt
This is a
multi-line
text.
EOF

# Here document with parameter substitution disabled
cat << 'EOF' > file.txt
This $variable will not be expanded.
EOF
```

### Process Substitution
```bash
diff <(command1) <(command2)   # Compare outputs of two commands
command <(command1)            # Use output of command1 as a file input
```

### Traps
```bash
trap 'commands' SIGNAL      # Execute commands when signal is received
trap 'echo "Exiting"; exit' EXIT    # Execute on script exit
trap 'echo "Ctrl+C caught"; cleanup' INT   # Handle Ctrl+C
trap - SIGNAL              # Reset trap for signal
```

### Subshells
```bash
(commands)                 # Run commands in subshell
(cd /tmp && command)       # Change directory only for subshell
```

### Named Pipes
```bash
mkfifo pipe                # Create named pipe
command > pipe &           # Write to pipe in background
command < pipe             # Read from pipe
```

### Case Modifiers
```bash
${var^}                    # Uppercase first character
${var^^}                   # Uppercase all characters
${var,}                    # Lowercase first character
${var,,}                   # Lowercase all characters
```

### Brace Expansion
```bash
echo {1..5}                # Expands to "1 2 3 4 5"
echo {01..5}               # Expands to "01 02 03 04 05"
echo {a..e}                # Expands to "a b c d e"
echo {1..10..2}            # Expands to "1 3 5 7 9"
echo file{1,2,3}.txt       # Expands to "file1.txt file2.txt file3.txt"
```

### Parameter Expansion
```bash
${parameter:-word}         # Use default if parameter unset or null
${parameter:=word}         # Assign default if parameter unset or null
${parameter:?word}         # Display error if parameter unset or null
${parameter:+word}         # Use alternate if parameter set and not null
${parameter:offset}        # Substring from offset
${parameter:offset:length} # Substring from offset with length
```

### Miscellaneous
```bash
set                        # Show all shell variables
alias name='command'       # Create command alias
unalias name               # Remove alias
exec command               # Replace current shell with command
eval command               # Evaluate command
source script              # Run script in current shell
. script                   # Same as source
time command               # Measure execution time
tee file                   # Read from stdin and write to stdout and files
```

## Bash Tips and Best Practices

### Performance Improvements
- Use `[[` instead of `[` for conditionals (faster and more features)
- Avoid piping when not necessary
- Use built-in commands instead of external commands when possible
- Use process substitution instead of temporary files

### Robustness
- Use quotes around variables to prevent word splitting and globbing
- Set `set -e` to exit on error
- Set `set -u` to error on undefined variables
- Use `set -o pipefail` to fail on pipe errors
- Add error handling with traps

### Debugging
- Add `set -x` to show executed commands
- Use `PS4='+(${BASH_SOURCE}:${LINENO}): '` for better debug output
- Use `bash -n script.sh` to check syntax

### Style
- Use meaningful variable and function names
- Add comments for complex sections
- Use indentation for readability
- Break long commands with backslashes
- Group related commands in functions
