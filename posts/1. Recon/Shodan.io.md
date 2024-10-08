# Shodan.io

## ASN
查询一家(例如tryhackme)，找到的IP地址是CLoudFlare的，它充当着Tryhackme与它真实服务器的代理服务器。因此对渗透这样一个大公司来说，这个IP地址没有意义，我们可以使用**自治系统编号(ASN)** 来进一步查找，[根据IP查找ASN](https://www.ultratools.com/tools/asnInfo?ref=skerritt.blog)
>自治系统号（ASN）是一组IP地址的全球标识符。如果你是像谷歌这样的大公司，你很可能拥有自己的ASN，用于管理你拥有的所有IP地址。

在Shodan.io上，我们可以使用ASN过滤器进行搜索。过滤器是 `ASN:[number]` ，其中number是我们之前得到的数字，即AS14061。

## filter expample
`ASN:[number]`:搜索ASN号码下属设备
`product:MySQL`：运行MySQL设备
`vuln:[ms17-010]`: 搜索有漏洞的设备，例：搜索有永恒之蓝漏洞的服务器

other filter: City Country Geo (coordinates) Hostname net (based on IP / CIDR) os (find operating systems) port before/after (timeframes)

## Shodan API
>该API允许我们以编程方式搜索Shodan并返回一个IP地址列表。如果我们是一家公司，我们可以编写一个脚本来检查我们的IP地址，看看它们中是否有任何漏洞。

[如何使用shodan_API](https://github.com/bee-san/How-I-Hacked-Your-Pi-Hole/blob/master/README.md?ref=skerritt.blog)
## Shodan Monitor
>跟踪您已经连接到互联网的设备。设置通知，启动扫描，并完全了解您所连接的内容。[shodan monitor](https://monitor.shodan.io/dashboard)



## Shodan Dorking
>Shodan有一些可爱的网页和Dorks，让我们可以找到一些东西。他们的搜索示例网页上有一些。
[搜索案例](https://beta.shodan.io/search/examples?ref=skerritt.blog)

有趣的过滤器：`has_screenshot:true encrypted attention` ——使用光学字符识别和远程桌面来查找因勒索软件而受到威胁的互联网上的机器。
`screenshot.label:ics`——使用机器学习，Shodan可以识别与互联网连接的工业控制系统。
```bash
# 1. 查询时可以接续查询下favicon.hash 2. 通过网站的商标文件位置查询
http.favicon.hash:<hash> #使用企业的商标搜索资产
```
通过商标路径查询：主要使用了[mmh3](https://github.com/hajimes/mmh3)来得到hash
```python
import mmh3
import requests
 
response = requests.get('https://www.baidu.com/favicon.ico')
favicon = response.content.encode('base64') #将获取的favicon base64编码
hash = mmh3.hash(favicon) #通过mmh3.hash进行哈希
print(hash)
```