# Path Abuse

PATH is an environment variable that specifies the set of directories where an executable can be located. An account's PATH variable is a set of absolute paths, allowing a user to type a command without specifying the absolute path to the binary. 

echo $PATH

/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games

Creating a script or program in a directory specified in the PATH will make it executable from any directory on the system :

pwd && conncheck 

/usr/local/sbin
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1189/sshd       
tcp        0     88 10.129.2.12:22          10.10.14.3:43218        ESTABLISHED 1614/sshd: mrb3n [p
tcp6       0      0 :::22                   :::*                    LISTEN      1189/sshd       
tcp6       0      0 :::80                   :::*                    LISTEN      1304/apache2    

## Adding . to a user's PATH 

PATH=.:${PATH}
export PATH
echo $PATH

touch ls
echo 'echo "PATH ABUSE!!"' > ls
chmod +x ls

ls
PATH ABUSE!!

--> Our malicious ls script is executed and not the real /bin/ls thanks to the . we added to the PATH

## LAB

echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/tmp
