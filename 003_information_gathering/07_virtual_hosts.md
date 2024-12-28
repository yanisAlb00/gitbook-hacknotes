# Virtual Hosts

## IP-based Virtual Hosting

A host can have multiple network interfaces. Multiple IP addresses, or interface aliases, can be configured on each network interface of a host.

## Name-based virtual hosting

Different FQDN point to the same IP and the difference is done at the applicative level.

curl -s http://$TARGET -H "Host: ap.inlanefreight.htb"

/opt/useful/SecLists/Discovery/DNS/namelist.txt

vHosts file :

app
blog
dev-admin
forum
help
m
my
shop
some
store
support
www

cat ./vhosts | while read vhost;do echo "\n********\nFUZZING: ${vhost}\n********";curl -s -I http://$TARGET -H "HOST: ${vhost}.inlanefreight.htb | grep "Content-Length: ";done

cat ./vhosts | while read vhost;do echo "\n********\nFUZZING: ${vhost}\n********";curl -s -I http://$TARGET -H "HOST: www.inlanefreight.htb" | grep "Content-Length: ";done


curl -s http://$TARGET -H "Host: dev-admin.randomtarget.com"

ffuf -w ./vhosts -u http://$TARGET -H "HOST: FUZZ.inlanefreight.htb" -fs 612

ffuf -w "SecLists/Discovery/DNS/namelist.txt" -u http://$TARGET -H "HOST: FUZZ.inlanefreight.htb" -mr "FLAG No. 1" -fs 10918 -mc 200

ap                      [Status: 200, Size: 102, Words: 3, Lines: 6, Duration: 5ms]
app                     [Status: 200, Size: 103, Words: 3, Lines: 6, Duration: 4ms]
citrix                  [Status: 200, Size: 100, Words: 3, Lines: 6, Duration: 13ms]
customers               [Status: 200, Size: 94, Words: 3, Lines: 6, Duration: 20ms]
dmz                     [Status: 200, Size: 95, Words: 2, Lines: 6, Duration: 40ms]
www2                    [Status: 200, Size: 96, Words: 2, Lines: 6, Duration: 3ms]

curl -s http://$TARGET -H "Host: ap.inlanefreight.htb"