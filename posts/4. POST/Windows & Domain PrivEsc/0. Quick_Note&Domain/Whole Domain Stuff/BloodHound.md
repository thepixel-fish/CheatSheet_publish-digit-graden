- Look for high value targets, see what your goal should be!
# BloodHound

###Source: 

- https://github.com/BloodHoundAD/BloodHound

## In Kali: 
Neo4j has to be running for Bloodhound web app to work:
```
$ sudo neo4j console
```
Set the password if you haven't already. 

Start bloodhound:
```
$ sudo ./Bloodhound --no-sandbox

MATCH (m:Computer) RETURN m
MATCH (m:User) RETURN m
```
## Pre-Compiled Binaries

- https://github.com/BloodHoundAD/BloodHound/releases

## SharpHound: 

- https://github.com/BloodHoundAD/SharpHound3

Execute on target:
```
C:\> .\SharpHound.exe -c all
C:\> SharpHound.exe --CollectionMethods Session --Loop --Loopduration 03:09:41
```
or in Powershell with .ps1 version

```cmd
C:\> import-module .\sharphound.ps1
C:\> Get-Help Invoke-BloodHound
C:\> invoke-bloodHound -CollectionMethod all -domain <target-domain> -LDAPUser <username> -LDAPPass <password> -OutputDirectory C:\tools  "corp audit"
```
OPTION: `-domain`, `-LDAPUser`,`-LDAPPass`, ` -OutputDirectory`, `-OutputPrefix`
NEED: `-CollectionMethod` 
Other useful sharphound flags:
- `--encryptzip`: zip文件密码
- `--zipfilename`: zip文件名

```cmd
#非域内主机，但可以连接到
C:\> runas /netonly /user:DOMAIN\USER powershell.exe
```
### Bloodhound for python 
Note: Only compatiable with BloodHound 3.0 or newer
https://github.com/fox-it/BloodHound.py
### Evil-WinRM
```bash
evil-winrm -i x.x.x.x -u user -p pass -s /pathtoscript/
Load SharpHound.ps1
Menu
(goto path you can write to)
Invoke-BloodHound -Domain HTB -LDAPUser #User -LDAPPass #Pass -CollectionMethod All -DomainController xxx -ZipFileName test.zip
```