# CDN Check
>通过各种查询方式来找寻是否启动了CDN服务
### 1. Ping目标主域名
当ping出来的域名很长一串或者存在明显的cdn，ali，tencent字段大概率存在CDN。![[Pasted image 20240625222056.png|500]]
### 2. 域名查询
DNSDumpster: www.dnsdumpster.com
**一个域名对应多个IP可判断大概率使用的CDN**
#### Dig
```bash
$ dig {$DOMAIN_NAME} TYPE
$ dig @{$DOMAIN_NAME_SERVER} TYPE

#e.g.
$ dig tryhackme.com MX
$ dig @1.1.1.1 tryhackme.com MX
```
#### NsLookUp
```bash
$ nslookup {$DOMAIN_NAME}
$ nslookup OPTIONS {$DOMAIN_NAME_SERVER}

#e.g.
$ nslookup -type=A tryhackme.com 1.1.1.1
```
![[Pasted image 20240625222131.png|300]]
### 3. 多地Ping，使用下列网站实现多地ping
	http://ping.chinaz.com/
	https://www.17ce.com/
	https://www.itdog.cn/ping
	http://ping.aizhan.com/
	http://ce.cloud.360.cn/
提供了多个IP地址；查看多地Ping的结果，境外访问的IP大概率是真实IP
![[Pasted image 20240625222252.png]]
### 4. 通过子域名查找真实IP
CDN服务昂贵，通常只有访问量较大或主站会使用CDN服务。可以通过查找域的子域名，根据子域名的IP确定真实IP
360quake/fofa/hunter/钟馗之眼等进行查找`domain="<Domain_name>"  domain:"<Domain_name>"`
[DNSdb](https://dnsdb.io/zh-cn/) --> `<Domain_name> type:<type>`
#### 测验
通过子域信息查询的真实IP再使用多地PING进行查验
### 5. DNS History
查看IP与域名绑定的历史记录，可能存在使用CDN前的记录
```bash
https://sitereport.netcraft.com/?url= #Online site query *

http://viewdns.info/ #DNS, IP query
https://tools.ipip.net/cdn.php #CDN IP query
https://x.threatbook.cn/ # threatbook OSINT

https://SecurityTrails.com #Precise!
```
***MX记录：如果服务器上同时托管了邮箱服务，IP地址也会在MX记录中找到***
### 网站漏洞查找
1）目标敏感文件泄露，例如：phpinfo之类的探针、GitHub信息泄露等。
2）XSS盲打，命令执行反弹shell，SSRF等。
3）无论是用社工还是其他手段，拿到了目标网站管理员在CDN的账号，从而在从CDN的配置中找到网站的真实IP。
### 网站邮件订阅查找
RSS邮件订阅，很多网站都自带 sendmail，会发邮件给我们，此时查看邮件源码里面就会包含服务器的真实 IP 了。