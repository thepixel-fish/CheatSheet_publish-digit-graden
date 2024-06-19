### /etc/passwd
```bash
cat /etc/passwd
less /etc/passwd
#name:pass:UID:GID:Comment:Home:LoginShell
getent passwd | awk -F: '{ print $1}'
cut -d: -f1 /etc/passwd

getent passwd {1000..6000} #获取桌面用户
```
### id
```bash
id
id -nG
getent group <group-name>
```
### groups
```bash
groups
less /etc/group
getent groups
getent group | awk -F: '{ print $1}'
```
# add user
```bash
adduser afsimmons # (alt, use full path: /usr/sbin/adduser afsimmons)
passwd afsimmons
useradd -G {group-name} afsimmons

/usr/sbin/usermod -aG sudo afsimmons
```