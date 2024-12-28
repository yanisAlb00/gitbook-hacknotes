# Passive Subdomain Enumeration

- VirusTotal

- Certificates
https://censys.io
https://crt.sh

export TARGET="facebook.com"
curl -s "https://crt.sh/?q=${TARGET}&output=json" | jq -r '.[] | "\(.name_value)\n\(.common_name)"' | sort -u > "${TARGET}_crt.sh.txt"

head -n20 facebook.com_crt.sh.txt

export TARGET="facebook.com"
export PORT="443"
openssl s_client -ign_eof 2>/dev/null <<<$'HEAD / HTTP/1.0\r\n\r' -connect "${TARGET}:${PORT}" | openssl x509 -noout -text -in - | grep 'DNS' | sed -e 's|DNS:|\n|g' -e 's|^\*.*||g' | tr -d ',' | sort -u

- TheHarvester

cat sources.txt
    baidu
    bufferoverun
    crtsh
    hackertarget
    otx
    projecdiscovery
    rapiddns
    sublist3r
    threatcrowd
    trello
    urlscan
    vhost
    virustotal
    zoomeye

export TARGET="facebook.com"
cat sources.txt | while read source; do theHarvester -d "${TARGET}" -b $source -f "${source}_${TARGET}";done

cat *.json | jq -r '.hosts[]' 2>/dev/null | cut -d':' -f 1 | sort -u > "${TARGET}_theHarvester.txt"

cat facebook.com_*.txt | sort -u > facebook.com_subdomains_passive.txt

cat facebook.com_subdomains_passive.txt | wc -l