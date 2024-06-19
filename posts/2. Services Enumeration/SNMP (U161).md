[[OSCP/1. Information Gathering Cycle/Active Reconnaissance#SNMP Enumeration|SNMP Enum For Detail & MIB]]
# snmpwalk
```bash
snmpwalk -c public -v1 ipaddress 1
snmpwalk -c private -v1 ipaddress 1
snmpwalk -c manager -v1 ipaddress 1
snmpwalk -v [VERSION_SNMP] -c [COMM_STRING] [DIR_IP] NET-SNMP-EXTEND-MIB::nsExtendObjects
sudo snmpbulkwalk -c public -v2c 192.168.214.149 NET-SNMP-EXTEND-MIB:nsExtendobjects

#利用MIB对应的分支OID
snmpwalk -c public -v1 192.168.50.151 1.3.6.1.4.1.77.1.2.25 
```
# Nmap NSE
```bash
ls -lsaht | grep -i snmp*
nmap  -Pn -sU -p 161 --script= -oN open-snmp.txt --open 172.21.0.0
```
# onesixtyone
```bash
onesixtyone -c /usr/share/doc/onesixtyone/dict.txt 172.21.0.X
onesixtyone -c /usr/share/doc/onesixtyone/dict.txt -i ips.txt
```

