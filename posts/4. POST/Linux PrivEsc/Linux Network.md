### Network Info
```bash
/sbin/ifconfig -a
/sbin/ip addr
ip addr show

cat /etc/network/interfaces
cat /etc/sysconfig/network
```
### Network Configuration
```bash
iptables -L
cat /etc/networks
cat /etc/sysconfig/network

cat /etc/resolv.conf
dnsdomainname
```
### List Current Connect
```bash
lsof -i
lsof -i :80
grep 80 /etc/services

ss -anp
netstat -antup
netstat -antpx
netstat -tulpn

chkconfig --list
chkconfig --list | grep 3:on
```
### List FireWall Setting
```bash
#Debian
cat /etc/iptables/rules.v4

#History Command, Need to Wait for some time.
iptables-save 
iptables-restore 
```
### Route
```bash
routel #or route

arp -e
route
route -n 
/sbin/route -nee
ip route list
```