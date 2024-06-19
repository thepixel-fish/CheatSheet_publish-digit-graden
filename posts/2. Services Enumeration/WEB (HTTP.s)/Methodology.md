1. 获取网站地图, 及中间件/CMS版本等 --> robots.txt, sitemap.xml, CHANGLOG, .git, README
2. 网站应用程序架构/技术栈 --> Wappalyzer, Whatweb, Builtwith (addon)
3. CMS利用 --> Plugin, Users, themes (Wordpress => wpscan) /
	1. 下载该CMS(git clone)，查看DirTree/docs/comments
4. 子域VHost寻找/爆破      # OSCP考试中尚未见
5. 目录爆破 --> Dirb/Dirsearch/gobuster
6. 漏扫 --> Nikto, Nuclei
7. 网站功能直接利用 => 文件上传/表单提交/注册与登录/数据库交互/命令执行
8. 手动测试


1.获取网站的技术栈信息。进行漏洞查找
```bash
#查找这些技术的公开漏洞 ---> 3. 是否使用一些Web Tech可以使用特定的扫描技术: wordpress/Dr...
whatweb -a 1 <URL> #Stealthy
whatweb -a 3 <URL> #Aggresive
webtech -u <URL>

searchsploit ...
```
2. 浏览(爬取)网站信息
	- 手动浏览网站，查看信息
	- **浏览源代码**
	- 查看常见的子目录信息
	`/robots.txt /sitemap.xml /Changelog /.git /RAEDME`
	`/.well-known/ /crossdomain.xml /clientaccesspolicy.xml`
3. 使用广泛/特定的扫描工具进行扫描
```bash
# General Purpose
Nikto -h <URL>/ whatweb -a 4 <URL>  / wapiti -u <URL>
nuclei -ut && nuclei -target <URL> / node puff.js -w ./wordlist-examples/xss.txt -u "http://www.xssgame.com/f/m4KKGHi2rVUN/?query=FUZZ"

# CMS Scanner
cmsmap [-f W] -F -d <URL>
wpscan --force update -e --url <URL>
joomscan --ec -u <URL>
joomlavs.rb #https://github.com/rastating/joomlav
```
4. DirBuster(**永远不要忘记递归爆破**)
```bash
dirb / Feroxbuster # 递归搜索
gobuster
Dirsearch（Python）#它不允许自签名证书，但允许递归搜索。
```
5. Manual Test (Deive into)
	1. LFI/RFI || SQLi || file_upload || RCE
	2. miscellaneous(Mics.)
```bash
		#API Visit
		#使用不同的HTTP动词，如 PATCH、DEBUG 或错误的 FAKE
		#在 CSS 文件中查找到其他文件的链接。
		#PUT method / WebDAV --> upload file
		#Find Vuln Para：Arjun、parameth、x8 和 Param Miner  --> find hidden Para
		#Check Comment For Info.
		 # nmap -- script=“http-ntlm-info.nse #如果该网站使用的NTLM Auth
		
```





