# Use it
```bash
curl --proxy http://10.10.11.131:3128 http://10.10.11.131

# /etc/proxychains.conf --> add http <Proxy_ip> 3128 [user] [pass]
proxychains nmap -sT -n -p- localhost #use it to scan the intranet.
```