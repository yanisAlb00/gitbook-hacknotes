# MYSQL

Client-Server : One MYSQL server for databases management system & One or more MYSQL clients

These databases are often stored in a single file with the file extension .sql

MariaDB is a fork of the original MYSQL code.

Port : TCP/3306

# Configuration

sudo apt install mysql-server -y
cat /etc/mysql/mysql.conf.d/mysqld.cnf | grep -v "#" | sed -r '/^\s*$/d'

## Interesting parameters 

user
password
admin_address
debug
sql_warnings
secure_file_priv

# Footprinting the service

## Enumeration

sudo nmap $TARGET -sV -sC -p3306 --script mysql*

|   Valid usernames: 
|     root:<empty> - Valid credentials

-> These kind of results are often F+


## Interaction

mysql -u root -pP4SSw0rd -h $TARGET


show databases;
select version();
use mysql;
show tables;
show columns from <table>;
select host, unique_users from host_summary;
