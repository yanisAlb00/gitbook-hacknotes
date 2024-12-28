# 04 nmap performance

Scanning performance plays a significant role when we need to scan an extensive network or are dealing with low network bandwidth.

Optimized RTT :&#x20;

```
sudo nmap 10.129.2.0/24 -F --initial-rtt-timeout 50ms --max-rtt-timeout 100ms 
```

```
sudo nmap 10.129.2.0/24 -F --max-retries 0 | grep "/tcp" | wc -l
```

```
sudo nmap 10.129.2.0/24 -F -oN tnet.minrate300 --min-rate 300 
```

```
sudo nmap 10.129.2.0/24 -F -oN tnet.T5 -T 5
```

&#x20;(Timing = 5 -> Agressive Timing = 0 --> Nice)

Find open ports with Default scans :&#x20;

```
cat tnet.default | grep "/tcp" | wc -l
```

Find open ports with Optimized scans :&#x20;

```
cat tnet.minrate300 | grep "/tcp" | wc -l
```

