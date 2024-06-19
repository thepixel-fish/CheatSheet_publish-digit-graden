# Copy/Download File By Drive
```bash
xfreerdp /u:username /p:password /v:remote_address /drive:local_name,path_to_local_folder
```
1. **这里 local_name 是远程会话中显示的驱动器名**，而 path_to_local_folder 是你想要重定向的本地文件夹的路径。
2. 连接到远程桌面：使用上述命令连接后，你应该能够在远程桌面的“我的电脑”或者类似的位置看到一个额外的驱动器，它代表了你重定向的本地文件夹。
3. 文件的复制/下载：在远程桌面会话中，你可以像操作普通文件一样复制和粘贴文件。你可以把远程桌面上的文件复制到你重定向的驱动器中，这样这些文件就会被复制到你本地的文件夹。
4. s断开连接：完成文件操作后，你可以正常断开 RDP 连接。此时，在你本地的重定向文件夹中应该能看到你从远程桌面复制过来的文件。

## e.g.
```bash
xfreerdp /u:username /p:password /v:remote_address /drive:local_drive,/home/kali/oscp
x
```
# Copy By Clipboard
```bash
xfreerdp /u:username /p: password /clipboard
#复制文件比较难
```