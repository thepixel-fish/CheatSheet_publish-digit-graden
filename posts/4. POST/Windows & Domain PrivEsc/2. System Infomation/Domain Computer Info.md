### PowerView
```powershell
Get-NetComputer | select operatingsystem,dnshostname
Get-NetComputer | select dnshostname,operatingsystem,operatingsystemversion
```
# Resolve-IPaddress
对域内的域名反向解析出它的IP地址
```powershell
resolve-IPaddress file04.corp.com
@("SERVER1", "SERVER2") | Resolve-IPAddress
```