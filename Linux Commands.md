TAGS: #LinuxCommands #Enumeration 

######################################

Ping Sweep

for i in `seq 1 255`; do ping -c 1 172.16.1.$i | tr \\n ' ' | awk '/1 received/ {print $2}'; done

We can use fping aswell


######################################

Enumeration

find hidden files

find . -type f -name '*.py'  <----you can edit this to find php, py, html, txt, whatever file you want.

-------------------------------------------------------------------------------------

```
find /home -name .zsh_history
find /home -name .bashrc -exec grep [PATTERN] {} \;

# grep to only match the line starting with passwd by using `^`
find /home -name .bash_history -exec grep -A 1 '^passwd' {} \;

find / -name .bash_history -exec grep -A 1 '^passwd' {} \; 2>/dev/null

```

---------------------------

Find writeable files in a linux box IE, if you want to download an exploit etc etc

find /home -printf "%f\t%p\t%u\t%g\t%m\n" 2>/dev/null | column -t

find / -writable -type d 2>/dev/null      # world-writeable folders

find / -perm -222 -type d 2>/dev/null     # world-writeable folders

find / -perm -o w -type d 2>/dev/null     # world-writeable folders

find / -perm -o x -type d 2>/dev/null     # world-executable folders

find / \( -perm -o w -perm -o x \) -type d 2>/dev/null   # world-writeable & executable folders

find / -perm -2 ! -type l -ls 2>/dev/null  # world readable and writeable folders - maybe a cron job running as root :)

find / -type f -exec grep -l "flag.txt" {} \;  ##Find a file with a particular name

---------------------------
-- Finding GUID
find / -perm -g=s -type f 2>/dev/null

-- Finding SUID
find / -perm -u=s -type f 2>/dev/null

writable files by root
find / -user root -perm -a+w 2>/dev/null
find / -xdev -user root -perm -a+w 2>/dev/null

writable files owned by root and writable by owner
find / -user root -perm -u+w 2>/dev/null

**find filtering by path, filename, and permissions**

find /path -iname "FILTER" -perm PERM

**find with flags used to list or delete files found**

find /path -iname "FILTER" -ls

**find with grep to quickly identify files of interest**

find /path -iname "FILTER" -exec grep -i "CONTENT" {} \;

**find things like shadow.bak etc**

find / -iname "shadow*"

**find** with flags used to list or delete files found (w/error redirection)

find / -iname "shadow*" -ls 2>/dev/null  

**find** results can also be filtered by file permissions as well (-perm) flag

find / -iname "shadow*" -perm /o+r -ls 2>/dev/null  

**find** with grep to search for strings inside of files:

find /home -iname "*.txt" 2>/dev/null -exec grep -i 'pass' {} \;  

**find** with grep to quickly identify files of interest:

find /home -iname "*.txt" 2>/dev/null -exec grep -li 'pass' {} \;  

**find** with egrep to quickly identify files of interest using regular expressions:

find /home -iname "*.txt" 2>/dev/null -exec egrep -li "^.+@.+$" {} \;  

_**Syntax: Recursively list all hidden files and directories on Linux/Unix**_

The basic syntax is as follows:

find /dir/to/search/ -name ".*" -print

OR

find /dir/to/search/ -name ".*" -ls

OR search only hidden files:

find /dir/to/search/ -type f -iname ".*" -ls

OR search only hidden directories:

find /dir/to/search/ -type d -iname ".*" -ls

OR

find /dir/to/search -path '*/.*' -print

find /dir/to/search -path '*/.*' -ls

_**Find files based in attributes e.g.; list files that are text, ascii, unicode etc etc**_

file /home/bandit4/inhere/*

_**Find a readable file, that is not executable, and has a certain size (within same directory)**_

find -readable -size 1033c ! -executable

```
remove special chars with \ in front. Example: \,
sed 's/[/,]/ /g' | sed 's/open tcp/ /g' | sed 's/       / /g' | sed 's/   //g'

remove chars
sed 's/[A-Za-z]//g'
```
