`nc -nv 172.21.0.0 22 # Might give header`
# Nmap
```bash
ls -lh /usr/share/nmap/scripts/ | grep ssh
nmap 172.16.0.0 -p 22 -sV ssh-hostkey --script-args ssh_hostkey=full
nmap 172.16.0.0 -p 22 -sV ssh-auth-methods --script-args="ssh.user=root" #Auth Method
```
# CrackMapExec
```bash
crackmapexec ssh 172.21.0.0 -u root -p password/passwordfile --no-bruteforce
crackmapexec ssh 172.21.0.0 -u root -p password/passwordfile --no-bruteforce -x whoami
```
# ssh-audit
Source: https://github.com/jtesta/ssh-audit
```bash
python ssh-audit.py [-1246pbcnjvlt] 172.21.0.0
```