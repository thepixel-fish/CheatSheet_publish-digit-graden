# Nmap
```bash
nmap -n -Pn -sU -p69 -sV --script tftp-enum <IP>
```
# Python Script
script to Download the tftp file.
```bash
#metasploit
msf6> auxiliary/admin/tftp/tftp_transfer_util

import tftpy #pip install tftpy
client = tftpy.TftpClient(<ip>, <port>)
client.download("filename in server", "/tmp/filename", timeout=5)
client.upload("filename to upload", "/local/path/file", timeout=5)
```