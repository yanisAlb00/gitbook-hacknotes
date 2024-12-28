# 02 nse nmap script engine

## NSE (Nmap Script Engine)

```
sudo nmap --script
```

Exemple :&#x20;

```
sudo nmap 10.129.2.28 -p 25 --script banner,smtp-commands
```

## Agressive Scan&#x20;

```
sudo nmap 10.129.2.28 -p 80 -A
```

-> Agressive scan : this scans the target with multiple options as service detection (-sV), OS detection (-O), traceroute (--traceroute), and with the default NSE scripts (-sC).

## Vulnerability Assessement

```
sudo nmap 10.129.2.28 -p 80 -sV --script vuln
```
