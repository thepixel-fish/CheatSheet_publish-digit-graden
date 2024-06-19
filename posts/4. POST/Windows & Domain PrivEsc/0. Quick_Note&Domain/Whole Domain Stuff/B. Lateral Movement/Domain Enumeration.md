关于Domain 内容还有可以吸收的： https://github.com/saisathvik1/OSCP-Cheatsheet?tab=readme-ov-file
# Impacket
使用这些工具可以查看域的一些基本信息，类似LDAP的域基本信息
```bash
impacket-services [domain]/[user]:[Password/Password Hash]@[Target IP Address] [Action] #service enumeration --> list...
impacket-lookupsid [domain]/[user]:[password/password hash]@[Target IP Address] #User enumeration on target
```