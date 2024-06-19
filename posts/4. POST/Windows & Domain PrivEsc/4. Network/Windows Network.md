### List all network interfaces, IP, and DNS.
```powershell
ipconfig /all
Get-NetIPConfiguration | ft InterfaceAlias,InterfaceDescription,IPv4Address
Get-DnsClientServerAddress -AddressFamily IPv4 | ft
arp -a
```
### List all current connections
```powershell
netstat -ano
```
### List ==Net Share==
域中的sysvol中旧政策存在内置管理员密码，
**可以用Kali:gpp-decrypt破解**
```cmd
net share
```
### List firewall state & current configuration
```powershell
netsh advfirewall firewall dump
netsh firewall show state
netsh firewall show config
#fireWall blocked port
$f=New-object -comObject HNetCfg.FwPolicy2;$f.rules |  where {$_.action -eq "0"} | select name,applicationname,localports
#Disable firewall
netsh firewall set opmode disable #Older Versions of Windows
netsh advfirewall set allprofiles state off
```
### Route Table
域中较为有用
```powershell
route print
Get-NetRoute -AddressFamily IPv4 | ft DestinationPrefix,NextHop,RouteMetric,ifIndex
```

