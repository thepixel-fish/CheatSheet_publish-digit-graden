在Windows 11上，默认情况下为RDP连接启用了网络级身份验证（[NLA](https://en.wikipedia.org/wiki/Network_Level_Authentication)）。
# rdesktop
不是加入域的机器，rdesktop无法连接到它。
```bash
rdesktop -u offsec -p lab -g %80+150+100 #-g 设置下屏幕的大小
```
## 文件下载
驱动器重定向：rdesktop 支持将本地驱动器重定向到远程会话，这样你可以在远程桌面会话中访问本地驱动器，然后进行文件复制。使用 `-r disk:<name>=<path>` 参数可以实现此功能。例如：
```bash
rdesktop -r disk:mydisk=/home/user/localfolder <remote_ip>
```
# Xfreerdp
我们可以使用xfreerdp代替，它支持非域加入机器的NLA。
[[Download,Copy File By Xfreerdp]]
```bash
xfreerdp /u:offsec /p:lab /w:1200 /h:700 #/w /h调整显示的宽高 1200:700
xfreerdp /u:offsec /p:lab /cert:ignore #/proxy:[<proto>://][<user>:<password>@]<host>:<port>
xfreerdp <config.rdp> /d:SKYLARK /u:kiosk #use .rdp file
xfreerdp ./cpub-SkylarkStatus-QuickSessionCollection-CmsRdsh.rdp /w:1300 /h:700 /drive:share,/home/kali/skylark/221  /u:kiosk /d:skylark.com /p:'XEwUS^9R2Gwt8O914'
```
## Some Thing Went Wrong
xfreerdp /cert-ignore /compression /auto-reconnect /u:USERNAME /p:PASSWORD /v:IP_ADDRESS

sudo rdesktop -u USERNAME -p PASSWORD -g 90% -r disk:local="/home/kali/Desktop/" IP-ADDRESS

rdesktop -u USERNAME -p PASSWORD -a 16 -P -z -b -g 1280x860 IP_ADDRESS

 xfreerdp /u:USERNAME /p:PASSWORD /cert:ignore /v:IP_ADDRESS /w:2600 /h:1400

xfreerdp +nego +sec-rdp +sec-tls +sec-nla /d: /u: /p: /v:IP_ADDRESS /u:USERNAME /p:PASSWORD /size:1180x708

rdesktop -z -P -x m -u USERNAME -p PASSWORD

xfreerdp /cert-ignore /bpp:8 /compression -themes -wallpaper /auto-reconnect /smart-sizing /drive:tmp,. /u:stephanie /p:'LegmanTeamBenzoin!!' /d:corp.com /v:192.168.x.75
## Trouble Shooting
Can you try to do the following to resolve your issue
stop any machines started
disconnect from your VPN and run the command "sudo killall -w openvpn"
logout from your Portal and clear your browsers data and cache
please also run the commands below

sudo bash -c " echo nameserver 8.8.8.8 > /etc/resolv.conf"
sudo bash -c " echo nameserver 8.8.4.4 >> /etc/resolv.conf"
sudo chattr +i /etc/resolv.conf

download a new VPN pack (make sure that you are on your Course Page and not on the Main Page) and make sure to delete all the old VPN's

Once done, please try to reconnect to your VPN and see how it goes.