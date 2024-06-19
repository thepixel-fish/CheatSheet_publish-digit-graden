d### LOLBAS
```powershell
net user <jeffadmin> /domain #<domain_user>
net group "Sales Department" /domain
```
### PowerView
```powershell
Get-NetUser
Get-NetGroup | select cn
Get-NetUser | select cn,pwdlastset,lastlogon

Get-DomainUser -PreauthNotRequired
 
Find-LocalAdminAccess
# 获取当前机器的登录用户, server 2019及同期版本已不可用
Get-NetSession -ComputerName files04 -Verbose 
Get-NetSession -ComputerName web04 -Verbose
#HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity -> SrvsvcSessionInfo
Get-Acl -Path HKLM:SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity\ | fl
```
# Exploit
LocalAdminAccess -> [[My Cheat Sheet/4. POST/Windows & Domain PrivEsc/0. Quick_Note&Domain/Whole Domain Stuff/A. Authentication Attack/Cached AD Credentials|Cached AD Credentials]]
User/Pass --> [[Spraying]]
## in Domain -- AS-REP Roasting
[[My Cheat Sheet/4. POST/Windows & Domain PrivEsc/0. Quick_Note&Domain/Whole Domain Stuff/A. Authentication Attack/Cracker#AS-REP Roasting|AS-REP Roasting for No-preAuth Users]]