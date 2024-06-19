- 可以使用https进行上传/下载
- 测试：确保通过导航到 http://localhost/SecretUploadDirectory 来保证**目录列表未启用**（apache默认启用目录）。

# Nginx
相比于Apache启用上传，Nginx更加安全且易于配置。通过
```bash
sudo mkdir -p /var/www/uploads/SecretUploadDirectory #create a dir to handle the upload
sudo chown -R www-data:www-data /var/www/uploads/SecretUploadDirectory

# /etc/nginx/sites-available/upload.conf 创建配置文件，通过https抓取文件
server {
    listen 9001;
    
    location /SecretUploadDirectory/ {
        root    /var/www/uploads;
        dav_methods PUT;
    }
}

sudo ln -s /etc/nginx/sites-available/upload.conf /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default #可以移除默认配置
sudo systemctl restart nginx.service


curl -T /etc/passwd http://localhost:9001/SecretUploadDirectory/users.txt
```