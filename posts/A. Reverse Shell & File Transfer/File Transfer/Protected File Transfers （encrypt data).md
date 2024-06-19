- use a secure transport method such as HTTPS, SFTP, or SSH.

On Windows:
Use [[Z.Invoke-AESEncryption Script|Invoke-AESEncryption.ps1]] to encrypt before download.Set password to De/Encrypt the file.
**Use Strong && Unique Password ！**
```powershell
#string
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Text "Secret Text"
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Text "LtxcRelxrD..."
# File
Invoke-AESEncryption -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Path file.bin.aes
```

On Linux:
```bash
openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.enc
openssl enc -d -aes256 -iter 100000 -pbkdf2 -in passwd.enc -out passwd #-d decrypt
```