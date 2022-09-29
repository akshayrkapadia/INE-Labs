# Offline Brute Force & Password Cracking

## John the Ripper

### Crack Admin Password

```
unshadow /etc/passwd /etc/shadow > crackme
john crackme --user=admin
```

![1.1](./imgs/1.1.png)

### Crack MS Word Password

```
python3 /usr/share/john/office2john.py MS_Word_Document.docx > hash.txt
john hash.txt --wordlist=/root/Desktop/wordlists/1000000-password-seclists.txt
```

![2.1](./imgs/2.1.png)

## Hashcat

### Crack Admin Password

Check hash function used
```
cat /etc/login.defs | grep "ENCRYPT_METHOD"
```

![3.1](./imgs/3.1.png)

-a ATTACK_MODE (0 = Straight)<br>
-m HASH_TYPE (1800 = sha512crypt $6$, SHA512 (Unix))<br>
--status shows status updates
```
unshadow /etc/passwd /etc/shadow > crackme
hashcat -a 0 -m 1800 --status crackme /root/Desktop/wordlists/1000000-password-seclists.txt
```

![3.2](./imgs/3.2.png)

### Crack MS Word Password

Word 2013 Document

![4.1](./imgs/4.1.png)

-m HASH_TYPE (9600 = MS Office 2013)
```
python3 /usr/share/john/office2john.py MS_Word_Document.docx > hash.txt
```

Modify hash file to use with hashcat<br>
Remove "MS_Word_Document.docx:"

```
hashcat -a 0 -m 9600 --status hash.txt /root/Desktop/wordlists/1000000-password-seclists.txt
```

![4.2](./imgs/4.2.png)