>ftp下载是全文件下载。在小文件传输以及版本迭代时，rsync只需要对比版本号以及文件修改时间，同步新的代码片段或者被修改的部分代码，有效减少了带宽占用与传输时间。*delta encoding* linux系统下的数据同步软件，常用于备份与镜像。可以在跨平台同步（win&linux)*在多数linux版本中都有*
```bash
$ rsync [OPTION] … [USER@]HOST::SRC [DEST]
#基本格式
$ rsync --list-only {target_IP}::
#anonymous登录，只查看文件列表，不同步.(--list-only参数基本必备)

$ rsync --list-only {target_IP}::public
#查看文件列表中的public文件夹

$ rsync {target_IP}::public/flag.txt flag.txt
#将SRC目录下的文件下载到目标路径(~)中（当前路径）

rsync -av --list-only rsync://username@192.168.0.123/shared_name
rsync -av rsync://username@192.168.0.123:8730/shared_name ./rsyn_shared
#上传文件，获取ssh
rsync -av ./authorized_keys anonymous@192.168.179.126::fox/.ssh #目录会自动创建
```



